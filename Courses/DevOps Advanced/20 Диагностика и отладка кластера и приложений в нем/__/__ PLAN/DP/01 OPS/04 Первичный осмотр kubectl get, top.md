### **04 Первичный осмотр: kubectl get, top**

**1. Визуальный ряд:**
*   Скриншот терминала с выводом команд:
    ```bash
    kubectl get pods -A
    kubectl get nodes
    kubectl top nodes
    kubectl top pods
    ```
*   Сравнительная таблица: `kubectl get` vs `kubectl top`
*   Схема: Иерархия ресурсов Kubernetes (Node -> Pod -> Container)
*   Иконки: 👀 (глаза) для get, 📊 (график) для top

**2. Демо-сценарий:**
*   Live-демонстрация в терминале:
    ```bash
    # Быстрый обзор состояния кластера
    kubectl get nodes -o wide
    kubectl get pods --all-namespaces | head -10
    
    # Мониторинг ресурсов
    kubectl top nodes
    kubectl top pods -A --sort-by=cpu | head -5
    kubectl top pods -A --sort-by=memory | head -5
    
    # Использование custom columns
    kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName
    ```

**3. Ключевые тезисы:**
*   "`kubectl get` — ваш 'радар' для быстрого понимания состояния кластера"
*   "Ключ `-o wide` покажет дополнительную информацию об объектах"
*   "`kubectl top` показывает текущее потребление ресурсов (требует metrics-server)"
*   "Сортировка по CPU/memory помогает быстро найти 'прожорливые' поды"
*   "Используйте `-A` для просмотра всех namespace'ов"

**4. Описание для выступающего:**
Показать, как быстро получить общую картину состояния кластера. Акцентировать внимание на ключах форматирования вывода (`-o wide`, `-o json`, `-o yaml`). Объяснить разницу между requests/limits и реальным потреблением ресурсов.

**5. Связь с прошлыми материалами:**
*   Из старой презентации: "kubectl get events —watch - посмотреть за event-объектами"
*   Из чата: "kubectl get events --sort-by=.metadata.creationTimestamp" - важность сортировки событий
*   Из субтитров: "Базовый цикл диагностики инцидента" - начало диагностики

**6. Типовые вопросы и ответы (Q&A):**
*   **В:** Почему `kubectl top` не работает?
    *   **О:** Нужен установленный metrics-server. Проверить: `kubectl get apiservices | grep metrics`
*   **В:** Как посмотреть потребление ресурсов за период?
    *   **О:** `kubectl top` показывает текущее состояние. Для истории нужны системы мониторинга (Prometheus)
*   **В:** Чем отличается `get pods` от `get pods -A`?
    *   **О:** `-A` показывает поды во всех namespace'ах, без флага — только в default

**7. Типовые примеры/сценарии:**
*   **Сценарий:** Поиск проблемного пода при высокой нагрузке на кластер
*   **Действия:**
    1. `kubectl top nodes` - найти перегруженную ноду
    2. `kubectl get pods -o wide --field-selector spec.nodeName=<node-name>` - поды на ноде
    3. `kubectl top pods` - найти проблемный под
*   **Пояснение:** Последовательность команд для быстрой диагностики проблем с ресурсами
