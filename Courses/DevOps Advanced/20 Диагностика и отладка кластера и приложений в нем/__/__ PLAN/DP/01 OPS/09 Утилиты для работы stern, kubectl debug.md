**1. Визуальный ряд:**
*   Скриншоты терминала с работой stern и kubectl debug:
    ```bash
    stern "app.*" -n namespace
    kubectl debug node/node-name -it --image=nicolaka/netshoot
    ```
*   Сравнительная таблица: stern vs kubectl logs
*   Схема работы ephemeral containers в kubectl debug
*   Логотипы инструментов: Stern, Netshoot, Busybox

**2. Демо-сценарий:**
*   Практическое использование инструментов:
    ```bash
    # Мониторинг логов нескольких подов одновременно
    stern "nginx.*" --since 10m --tail 10
    
    # Отладка проблемного пода с ephemeral container
    kubectl debug pod/my-app -it --image=busybox --target=my-app
    
    # Отладка узла кластера
    kubectl debug node/my-node -it --image=nicolaka/netshoot
    
    # Использование netshoot для сетевой диагностики
    kubectl debug pod/my-app -it --image=nicolaka/netshoot
    ```

**3. Ключевые тезисы:**
*   "Stern — это 'tail -f' для Kubernetes: агрегирует логи по маске имен подов"
*   "kubectl debug — современный способ отладки через ephemeral containers"
*   "Ephemeral containers не перезапускают основной под и не меняют его спецификацию"
*   "Netshoot — швейцарский нож для сетевой диагностики в контейнерах"
*   "Используйте --target для отладки конкретного контейнера в поде"

**4. Описание для выступающего:**
Показать преимущества stern перед ручным агрегированием логов. Объяснить архитектуру ephemeral containers и их отличие от обычных контейнеров. Важно показать практические сценарии использования netshoot для диагностики сети.

**5. Связь с прошлыми материалами:**
*   Из чата: "Если контейнеров несколько, логи какого по дефолту показываются?" → stern решает эту проблему
*   Из субтитров: "kubectl debug" → развитие темы отладки
*   Общая связь: Естественное развитие инструментария диагностики

**6. Типовые вопросы и ответы (Q&A):**
*   **В:** Чем stern лучше чем kubectl logs?
    *   **О:** Автоматически агрегирует логи по маске, не нужно указывать конкретные поды
*   **В:** Нужны ли особые права для kubectl debug?
    *   **О:** Да, обычно нужны права на создание pods/ephemeralcontainers
*   **В:** Можно ли использовать свои образы для отладки?
    *   **О:** Да, любые образы с нужными утилитами (busybox, alpine, debian)

**7. Типовые примеры/сценарии:**
*   **Сценарий:** Диагностика сетевых проблем между микросервисами
*   **Действия:**
    1. `kubectl debug pod/my-app --image=nicolaka/netshoot`
    2. `curl -v http://other-service:port`
    3. `dig other-service.namespace.svc.cluster.local`
    4. `tcpdump -i any -n port 80`
*   **Пояснение:** Netshoot содержит все необходимые утилиты для полной сетевой диагностики

**8. Предложения по дополнительному контенту слайда:**
*   Добавить cheatsheet по常用 командам netshoot
*   Сравнение производительности: stern vs kubectl logs с агрегацией
*   Пример настройки autocomplete для stern
*   Информация о безопасности ephemeral containers
*   Ссылки на репозитории инструментов и примеры использования