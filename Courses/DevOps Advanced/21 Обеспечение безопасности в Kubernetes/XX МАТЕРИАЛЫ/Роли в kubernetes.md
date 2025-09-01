### **Типы ролей в Kubernetes**

#### **1. `Role` (Роль)**

*   **Область действия:** Привязана к **одному конкретному namespace** (пространству имен).
*   **Назначение:** Используется для определения набора разрешений (permissions) на ресурсы, которые находятся только в рамках одного namespace.
*   **Пример использования:** Вы можете создать роль `pod-reader`, которая разрешает пользователям выполнять действия `get`, `list` и `watch` только для Pod'ов в namespace `development`. Эта же роль не даст никаких прав в namespace `production`.
*   **Ссылка из источников:**
    *   `Gkatziouras Emmanouil, Adams Rom, Xi Chen - Kubernetes Secrets Handbook - 2024`: "Roles are a set of permissions that take effect only on the namespace where the role resides."
    *   `Boorshtein Marc, Surovich Scott - Kubernetes An Enterprise Guide, 3rd Edition - 2024`: "Role is a way to tie together permissions into an object standardized to a specific schema... Roles have rules, which are a collection of resources and verbs."

#### **2. `ClusterRole` (Кластерная роль)**

*   **Область действия:** Действует на **весь кластер**, вне зависимости от namespace.
*   **Назначение:** Используется для двух основных целей:
    1.  **Доступ к ресурсам кластерного уровня:** Например, `nodes`, `persistentvolumes`, `CustomResourceDefinitions (CRD)`, `storageclasses`.
    2.  **Доступ к ресурсам во всех namespace'ах:** Вы можете использовать `ClusterRole` для определения прав, которые будут применяться ко всем namespace'ам (например, право читать секреты в любом namespace).
*   **Пример использования:** Встроенная роль `cluster-admin` предоставляет полный контроль над всем кластером. Роль `view` может быть привязана к нескольким namespace'ам через `RoleBinding`, обеспечивая единый доступ на чтение.
*   **Ссылка из источников:**
    *   `Burns B., Villalba E., Strebel D., Evenson L. - Kubernetes Best Practices - 2024`: "Kubernetes has two types of roles, role and clusterRole, the difference being that role is specific to a namespace, and clusterRole is a cluster-wide role across all namespaces."
    *   `Ibryam Bilgin, Huss Roland - Kubernetes Patterns, 2nd Edition - 2023`: "ClusterRoles in Kubernetes are similar to regular Roles but are applied cluster-wide rather than to a specific namespace."

#### **Важно: Роли сами по себе ничего не делают**

Создание `Role` или `ClusterRole` только **определяет набор разрешений**. Чтобы эти разрешения были применены к конкретному пользователю, группе или сервисному аккаунту, необходимо создать **привязку (Binding)**:

*   **`RoleBinding`** привязывает `Role` (или `ClusterRole`) к субъекту **в пределах одного namespace**.
*   **`ClusterRoleBinding`** привязывает `ClusterRole` к субъекту **на уровне всего кластера**.

Это позволяет, например, использовать одну и ту же `ClusterRole` (например, `view`) и привязывать ее к разным пользователям в разных namespace'ах с помощью `RoleBinding`.