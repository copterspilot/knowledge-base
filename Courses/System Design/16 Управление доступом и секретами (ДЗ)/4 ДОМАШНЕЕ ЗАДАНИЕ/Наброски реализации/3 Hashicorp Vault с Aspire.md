```plantuml
@startuml
' Стили
skinparam rectangle {
    roundCorner 10
}
skinparam component {
    roundCorner 10
}

title Подключение .NET Aspire API к HashiCorp Vault

' Kubernetes/Orchestration Environment
package "Kubernetes Cluster" {
    rectangle "Aspire Application\n(Distributed App Host)" as aspire_host #e3f2fd {
        rectangle "Aspire Dashboard" as aspire_dashboard #bbdefb
        rectangle "App API Service\n(Aspire Resource)" as app_api_service #e8f5e8 {
            rectangle "API Container" as api_container #c8e6c9
            rectangle "Vault Agent Sidecar" as vault_agent_sidecar #f3e5f5
        }
        rectangle "Vault Injector\n(Webhook)" as vault_injector #fce4ec
    }

    rectangle "HashiCorp Vault\n(Server)" as vault_server #f3e5f5 {
        rectangle "Vault Core" as vault_core #e1bee7
        rectangle "Auth Backend\n(Kubernetes)" as vault_auth #d1c4e9
        rectangle "Secret Engine\n(KV, Database)" as vault_secrets #d1c4e9
        rectangle "Storage Backend\n(Consul/Integrated)" as vault_storage #d1c4e9
        rectangle "Audit Backend" as vault_audit #d1c4e9
    }

    rectangle "Consul\n(Storage Backend)" as consul_backend #fff3e0
    rectangle "External Systems" as external #e0f2f1 {
        rectangle "Database" as database #b2dfdb
        rectangle "Monitoring\n(Prometheus)" as monitoring #b2dfdb
    }
}

' Внешние пользователи
actor "Developer/Operator" as developer #e1f5fe
actor "End User" as user #e1f5fe

' Связи Aspire Host
aspire_host --> aspire_dashboard : Manages\nResources
aspire_host --> app_api_service : Deploys &\nManages
aspire_host --> vault_injector : Uses for\nInjection

' Vault Agent Sidecar взаимодействие
vault_agent_sidecar --> vault_server : 1. Auth &\nSecret Fetch
vault_agent_sidecar --> api_container : 2. Provide Secrets\n(Files/Tokens)

' Vault Server внутренние связи
vault_core --> vault_auth : Uses for\nAuthentication
vault_core --> vault_secrets : Manages\nSecrets
vault_core --> vault_storage : Stores\nEncrypted Data
vault_core --> vault_audit : Logs\nOperations
vault_server --> consul_backend : Data Storage

' Приложение использует Vault напрямую (альтернатива/дополнение)
api_container --> vault_server : 3. Direct API\nCalls (Optional)

' Приложение использует внешние системы
api_container --> database : 4. Use DB\n(with Vault secrets)
api_container --> monitoring : 5. Export\nMetrics

' Внешние взаимодействия
developer --> aspire_host : Deploys App,\nConfigures Vault
user --> app_api_service : 6. Calls API
vault_auth --> aspire_host : Validates\nKubernetes JWT

note top of vault_agent_sidecar
Vault Agent Sidecar:
- Аутентифицируется в Vault
- Получает секреты по политикам
- Предоставляет их приложению
- Автоматически обновляет при истечении
end note

note left of vault_auth
Kubernetes Auth:
- Использует ServiceAccount JWT
- Привязывает к Vault Roles
- Предоставляет Vault Tokens
end note

note bottom of vault_secrets
Secret Engines:
- KV: Хранение конфигов, API ключей
- Database: Динамические учетные записи БД
end note

@enduml
```

**Пояснение ключевых элементов:**

1.  **Aspire Application (Distributed App Host):** Оркестратор, запускающий ваше распределенное приложение.
2.  **App API Service:** Сервис Aspire, представляющий ваше API-приложение. В Kubernetes это обычно Pod.
3.  **API Container:** Контейнер с вашим .NET API приложением.
4.  **Vault Agent Sidecar:** Отдельный контейнер в том же Pod'е, который взаимодействует с Vault от имени вашего приложения. Это рекомендуемый подход для автоматического получения и обновления секретов.
5.  **Vault Injector (Webhook):** Компонент Vault (чаще всего это самостоятельный injector, интегрированный с Kubernetes), который автоматически добавляет Vault Agent Sidecar в Pod на основе аннотаций.
6.  **HashiCorp Vault:** Центральный сервер Vault.
    *   **Auth Backend (Kubernetes):** Метод аутентификации, позволяющий Pod'ам Kubernetes аутентифицироваться с помощью их ServiceAccount JWT.
    *   **Secret Engine:** Движки для генерации и хранения секретов (например, KV для статических секретов, Database для динамических учетных записей БД).
7.  **Поток данных:**
    *   Vault Agent (1) аутентифицируется в Vault и получает секреты.
    *   Vault Agent (2) предоставляет секреты API-приложению (например, через файл в общей директории или через localhost API).
    *   API-приложение (3) может также напрямую вызывать Vault API (дополнительный/альтернативный способ).
    *   API-приложение (4) использует полученные секреты для подключения к БД.
    *   API-приложение (5) экспортирует метрики для мониторинга.
    *   Пользователь (6) вызывает API.
    *   Vault Auth (7) проверяет JWT от Pod'а для аутентификации Vault Agent.

Эта диаграмма демонстрирует типичную и безопасную интеграцию .NET Aspire приложения с HashiCorp Vault в Kubernetes-среде.