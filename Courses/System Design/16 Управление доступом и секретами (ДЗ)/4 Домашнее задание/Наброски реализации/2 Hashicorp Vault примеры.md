## 🗺️ **C4 Model для HashiCorp Vault в Kubernetes**

### **Уровень 1: Контекст (Context)**

```plantuml
@startuml
' C4 Context Level
skinparam rectangle {
    roundCorner 10
}

rectangle "Внешние системы" as external {
    rectangle "Приложения\n(Kubernetes Pods)" as apps #e1f5fe
    rectangle "Операторы\n(DevOps/SRE)" as operators #e1f5fe
    rectangle "Мониторинг\n(Prometheus/Grafana)" as monitoring #e1f5fe
}

rectangle "Kubernetes кластер" as k8s_cluster {
    rectangle "HashiCorp Vault" as vault #f3e5f5
    rectangle "Vault Operator" as vault_operator #e8f5e8
    rectangle "Etcd кластер" as etcd #fff3e0
}

apps --> vault : Запросы секретов
operators --> vault : Управление
operators --> vault_operator : Конфигурация
monitoring --> vault : Метрики
vault --> etcd : Хранение данных

note left of vault
Централизованное управление
секретами для Kubernetes
end note

@enduml
```

### **Уровень 2: Контейнеры (Containers)**

```plantuml
@startuml
' C4 Container Level
skinparam rectangle {
    roundCorner 10
}

rectangle "Kubernetes кластер" as k8s_cluster {
    rectangle "Vault Server Pod" as vault_pod {
        rectangle "Vault Server" as vault_server #f3e5f5
        rectangle "Vault Agent Injector" as agent_injector #e8f5e8
        rectangle "Vault Sidecar" as vault_sidecar #e8f5e8
    }
    
    rectangle "Vault Operator Pod" as operator_pod {
        rectangle "Vault Operator" as vault_operator #e8f5e8
        rectangle "Vault CRDs" as crds #fff3e0
    }
    
    rectangle "Etcd StatefulSet" as etcd_statefulset {
        rectangle "Etcd Node 1" as etcd1 #fff3e0
        rectangle "Etcd Node 2" as etcd2 #fff3e0
        rectangle "Etcd Node 3" as etcd3 #fff3e0
    }
    
    rectangle "Application Pods" as app_pods {
        rectangle "Application Container" as app_container #e1f5fe
        rectangle "Vault Agent Sidecar" as app_sidecar #e8f5e8
    }
}

' Внешние зависимости
rectangle "External Applications" as external_apps #e1f5fe
rectangle "Monitoring Stack" as monitoring_stack #e1f5fe

' Связи
external_apps --> app_container : API Requests
app_container --> app_sidecar : Local API
app_sidecar --> vault_server : Secret Requests
vault_server --> etcd1 : Data Storage
vault_server --> etcd2 : Data Storage
vault_server --> etcd3 : Data Storage
vault_operator --> vault_server : Configuration
vault_operator --> crds : Manage CRDs
monitoring_stack --> vault_server : Metrics Collection

@enduml
```

### **Уровень 3: Компоненты (Components)**

```plantuml
@startuml
' C4 Component Level - Vault Server
skinparam rectangle {
    roundCorner 10
}

rectangle "Vault Server" as vault_server {
    rectangle "API Layer" as api_layer #f3e5f5
    rectangle "Core" as core #e8f5e8
    rectangle "Auth Methods" as auth_methods #e1f5fe
    rectangle "Secret Engines" as secret_engines #fff3e0
    rectangle "Audit Devices" as audit_devices #ffebee
    rectangle "Storage Backend" as storage_backend #f1f8e9
    rectangle "Auto Unseal" as auto_unseal #e0f2f1
}

' Компоненты Auth Methods
rectangle "Auth Methods" as auth_section {
    rectangle "Kubernetes Auth" as k8s_auth #e1f5fe
    rectangle "AppRole Auth" as approle_auth #e1f5fe
    rectangle "Token Auth" as token_auth #e1f5fe
    rectangle "LDAP Auth" as ldap_auth #e1f5fe
}

' Компоненты Secret Engines
rectangle "Secret Engines" as secret_section {
    rectangle "KV Engine" as kv_engine #fff3e0
    rectangle "Database Engine" as db_engine #fff3e0
    rectangle "PKI Engine" as pki_engine #fff3e0
    rectangle "Transit Engine" as transit_engine #fff3e0
}

' Связи между компонентами
api_layer --> core : Requests
core --> auth_methods : Authentication
core --> secret_engines : Secret Operations
core --> audit_devices : Audit Logging
core --> storage_backend : Data Storage
core --> auto_unseal : Key Management

auth_methods --> k8s_auth : Kubernetes Integration
auth_methods --> approle_auth : Application Auth
auth_methods --> token_auth : Token Management
auth_methods --> ldap_auth : LDAP Integration

secret_engines --> kv_engine : Key-Value Storage
secret_engines --> db_engine : Dynamic Database Credentials
secret_engines --> pki_engine : Certificate Management
secret_engines --> transit_engine : Encryption Services

storage_backend --> etcd_cluster : External Storage

note top of core
Ядро Vault координирует
все операции и обеспечивает
безопасность
end note

note right of k8s_auth
Аутентификация через
ServiceAccount Kubernetes
end note

note right of kv_engine
Хранение статических
секретов с версионированием
end note

@enduml
```

## 🏗️ **Архитектурные особенности реализации**

### **1. Типовая конфигурация Vault в Kubernetes:**

```yaml
# Vault Helm Chart values.yaml
server:
  image:
    repository: "hashicorp/vault"
    tag: "1.15.0"
  
  authDelegator:
    enabled: true
  
  ha:
    enabled: true
    replicas: 3
    raft:
      enabled: true
      setNodeId: true
  
  # Kubernetes Auth Method
  authMethods:
    kubernetes:
      enabled: true
      config:
        kubernetes_host: "https://kubernetes.default.svc"
  
  # Audit Backend
  audit:
    - type: file
      path: "/vault/logs/audit.log"
    - type: syslog
      tag: "vault"

# Storage Backend
storage:
  etcd:
    address: "https://etcd-client:2379"
    ha_enabled: "true"
```

### **2. Интеграция с приложениями:**

```yaml
# Application Pod с Vault Agent Sidecar
apiVersion: v1
kind: Pod
metadata:
  name: my-app
  annotations:
    vault.hashicorp.com/agent-inject: "true"
    vault.hashicorp.com/role: "my-app-role"
    vault.hashicorp.com/agent-inject-secret-database-config: "database/creds/my-app"
spec:
  containers:
  - name: my-app
    image: my-app:latest
    volumeMounts:
    - name: vault-secrets
      mountPath: "/vault/secrets"
      readOnly: true
```

### **3. Политики безопасности:**

```hcl
# Пример Vault Policy
path "secret/data/my-app/*" {
  capabilities = ["read"]
}

path "database/creds/my-app" {
  capabilities = ["read"]
}

path "pki/issue/my-app" {
  capabilities = ["create", "update"]
}
```

Эта архитектура соответствует лучшим практикам использования HashiCorp Vault в Kubernetes и охватывает ключевые аспекты.