```markdown
## [6] RBAC: Управление доступом на основе ролей (на практике)

**Содержимое слайда (для PowerPoint):**
-   **Заголовок:** RBAC: Управление доступом на основе ролей (на практике)
-   **Основной текст / список:**
    *   **Основные компоненты:**
        *   **Пользователи и сервисные аккаунты:** Кто запрашивает доступ.
        *   **Роли и Кластерные роли:** Что можно делать (get, list, create, delete).
        *   **Привязки и Кластерные привязки:** Кто может что делать.
    *   **Пример: Доступ разработчика к `production`**
        *   **Создать Роль:** Разрешить `get`, `list`, `watch` для `pods` и `services`.
        *   **Создать Привязку:** Связать Роль с сервисным аккаунтом `dev-sa`.
        *   **Проверить доступ:** `kubectl auth can-i get pods -n production --as=dev-sa`
    *   **Ключевой принцип:** Принцип минимальных привилегий (Least Privilege).
-   **Дополнительная информация (внизу):** "Никогда не давайте `cluster-admin` разработчикам."
```

---

### **Статья 6: RBAC: Управление доступом на основе ролей (на практике)**

#### **Введение**

RBAC (Role-Based Access Control) — это фундамент безопасности в Kubernetes. Он определяет, кто может делать что. Представьте, что у вас есть команда разработчиков, которым нужно просматривать логи в production, но нельзя их менять. RBAC позволяет вам создать для них роль с правами только на чтение. Как указано в `Burns B., Villalba E., Strebel D., Evenson L. - Kubernetes Best Practices`, правильная настройка RBAC — это первый шаг к защите вашего кластера.

#### **Основные компоненты RBAC**

1.  **Субъекты (Subjects):** Кто запрашивает доступ.
    *   **Пользователи:** Люди (например, `jane@example.com`).
    *   **Группы:** Коллекции пользователей (например, `dev-team`).
    *   **Сервисные аккаунты:** Учетные записи, используемые приложениями и подами внутри кластера (например, `default:frontend-sa`).

2.  **Роли (Roles) и Кластерные роли (ClusterRoles):** Что можно делать.
    *   **Роль:** Определяет набор прав (глаголов: `get`, `list`, `create`, `delete`) на определенные ресурсы (например, `pods`, `services`) в рамках одного namespace.
    *   **Кластерная роль:** Аналогична Роли, но применяется на уровне всего кластера (например, управление `nodes`, `persistentvolumes`).

3.  **Привязки (RoleBindings) и Кластерные привязки (ClusterRoleBindings):** Кто может что делать.
    *   **Привязка:** Связывает субъект (пользователя, группу, сервисный аккаунт) с Ролью в определенном namespace.
    *   **Кластерная привязка:** Связывает субъект с Кластерной ролью для всего кластера.

#### **Практический пример: Ограничение доступа к production**

Цель: Дать разработчику (через сервисный аккаунт) доступ только на чтение к подам и сервисам в namespace `production`.

1.  **Создать Роль:**
    ```yaml
    # role-reader.yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      namespace: production
      name: pod-reader
    rules:
    - apiGroups: [""] # "" указывает на core API group
      resources: ["pods", "services"]
      verbs: ["get", "list", "watch"]
    ```
    Эта роль позволяет получать, перечислять и отслеживать изменения подов и сервисов.

2.  **Создать Привязку:**
    ```yaml
    # rolebinding-reader.yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: read-pods
      namespace: production
    subjects:
    - kind: ServiceAccount
      name: dev-sa # Имя сервисного аккаунта
      namespace: default # Namespace, где находится сервисный аккаунт
    roleRef:
      kind: Role
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    ```
    Эта привязка связывает сервисный аккаунт `dev-sa` из namespace `default` с ролью `pod-reader` в namespace `production`.

3.  **Применить конфигурацию:**
    ```bash
    kubectl apply -f role-reader.yaml
    kubectl apply -f rolebinding-reader.yaml
    ```

4.  **Проверить доступ:**
    ```bash
    # Проверяем, может ли сервисный аккаунт 'dev-sa' читать поды
    kubectl auth can-i get pods -n production --as=system:serviceaccount:default:dev-sa
    # Ожидаемый ответ: yes
    
    # Проверяем, может ли он создать под
    kubectl auth can-i create pods -n production --as=system:serviceaccount:default:dev-sa
    # Ожидаемый ответ: no
    ```

#### **Ключевой принцип: Минимальные привилегии**

Всегда следуйте принципу **минимальных привилегий (Least Privilege)**. Предоставляйте субъекту только те права, которые ему *абсолютно необходимы* для выполнения его задачи. Никогда не давайте разработчикам права `cluster-admin` — это эквивалент "корня" в кластере и представляет огромный риск.

#### **Заключение**

RBAC — это мощный и гибкий механизм, который позволяет вам точно контролировать доступ к вашему кластеру. Начните с простых ролей для чтения и постепенно расширяйте их по мере необходимости. В следующей статье мы рассмотрим, как визуализировать эти права с помощью `kubectl who-can`.

#### **Ссылки**
*   `Burns B., Villalba E., Strebel D., Evenson L. - Kubernetes Best Practices - 2024` — Глава 8: Security.
*   `Muschko Benjamin - Certified Kubernetes Application Developer (CKAD) Study Guide, 2nd Edition - 2024` — Глава 5: RBAC.
*   Официальная документация Kubernetes: `https://kubernetes.io/docs/reference/access-authn-authz/rbac/`