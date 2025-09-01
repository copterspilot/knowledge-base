**1. Визуальный ряд**

- Четко структурированный `cheatsheet` в виде таблицы или списка.
- Разделы: `kubectl`, `krew`, `kubectl debug`, `ksniff`, `telepresence`, `LitmusChaos`, `Trivy`, `kube-bench`.

**2. Демо-сценарий (если уместен)**

- Нет.

**3. Ключевые тезисы**

- Сводная таблица самых полезных команд и инструментов.
- Быстрый доступ к синтаксису команд.
- Незаменимый справочник для ежедневной работы.

**4. Описание (для выступающего)**

- **Цель слайда:** Предоставить слушателям готовую "шпаргалку" по основным командам и инструментам.
- **Ключевое сообщение:** Используйте этот `cheatsheet` как настольную книгу.
- **Содержание:**
    - `kubectl get pods --watch`
    - `kubectl describe pod <имя>`
    - `kubectl logs <имя> --previous`
    - `kubectl debug <имя> --image=nicolaka/netshoot -- sh`
    - `kubectl sniff <имя>`
    - `telepresence connect && telepresence intercept <deployment> --port 8080:80`

#### **Слайд 33: Чек-лист 1: Базовый цикл диагностики инцидента**

**1. Визуальный ряд**

- Пошаговый чек-лист с галочками:
    1. `kubectl get pods`
    2. `kubectl get nodes`
    3. `kubectl get events --watch`
    4. `kubectl describe pod <имя_пода>`
    5. `kubectl logs <имя_пода>`
    6. `kubectl logs <имя_пода> --previous`

**2. Демо-сценарий (если уместен)**

- Нет.

**3. Ключевые тезисы**

- Систематический подход решает 90% проблем.
- Начинайте с `get` и `events`.
- `describe` и `logs --previous` — ключ к корню проблемы.
