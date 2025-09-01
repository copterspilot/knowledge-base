### **Статья 4: RBAC: Управление доступом на основе ролей (на практике)**

#### **Основные компоненты RBAC**

RBAC (Role-Based Access Control) в Kubernetes строится на четырех ключевых объектах, которые работают в паре:

1.  **`Role` и `ClusterRole`**: Определяют **набор разрешений** (что можно делать).
    *   `Role`: Разрешения действуют в пределах одного **namespace**.
    *   `ClusterRole`: Разрешения действуют на **весь кластер** (например, на `nodes`, `persistentvolumes`).

2.  **`RoleBinding` и `ClusterRoleBinding`**: Связывают **субъект** (кто) с **ролью** (что может делать).
    *   `RoleBinding`: Привязывает субъекта к `Role` в одном namespace.
    *   `ClusterRoleBinding`: Привязывает субъекта к `ClusterRole` на уровне всего кластера.

#### **Субъекты (Subjects)**

Разрешения можно привязать к трем типам субъектов:
*   **Пользователь (User):** Обычно это человек (например, `jane@example.com`).
*   **Группа (Group):** Коллекция пользователей (например, `dev-team`).
*   **Сервисный аккаунт (ServiceAccount):** Специальный аккаунт для приложений, работающих в кластере (например, `default`, `my-app-sa`).

#### **Практический пример: Настройка доступа для разработчика**

Представим, что вы хотите дать разработчику `alice` права на просмотр (`get`) и перечисление (`list`) подов в namespace `dev`.

1.  **Создайте `Role` в namespace `dev`:**
    ```yaml
    # role.yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      namespace: dev
      name: pod-reader
    rules:
    - apiGroups: [""] # "" обозначает core API group
      resources: ["pods"]
      verbs: ["get", "list"]
    ```
    *   `rules`: Определяет, к каким ресурсам и с какими действиями (`verbs`) есть доступ.

2.  **Создайте `RoleBinding`, связывающее пользователя `alice` с ролью `pod-reader`:**
    ```yaml
    # rolebinding.yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: read-pods
      namespace: dev
    subjects:
    - kind: User
      name: alice
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: Role
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    ```
    *   `subjects`: Указывает, кто получает доступ.
    *   `roleRef`: Ссылается на созданную `Role`.

3.  **Примените манифесты:**
    ```bash
    kubectl apply -f role.yaml
    kubectl apply -f rolebinding.yaml
    ```

#### **Путь вперед**

В этом примере мы использовали `Role` и `RoleBinding` в одном namespace. На практике часто используют `ClusterRole`, чтобы определить набор разрешений один раз, а затем привязывать его к разным `RoleBinding` в разных namespace'ах. Это упрощает управление.

**Запомните:** Всегда начинайте с минимальных привилегий. Давайте субъекту только то, что ему **необходимо** для работы.

---

Готов к следующему шагу. Переходим к **Статье 5: `kubectl who-can`: Визуализация прав доступа**?