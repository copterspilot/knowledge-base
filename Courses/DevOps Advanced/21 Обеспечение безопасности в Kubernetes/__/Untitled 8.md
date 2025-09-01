Вот **структура презентации** с учётом всех твоих требований:

- Каждый слайд имеет **номер** в заголовке.
- После названия — разделы:
  - **Визуальный вид слайда**
  - **Подробное описание** (с ссылками на книги, темы, контекст)
- Язык — технический, но понятный DevOps-инженерам.
- Акцент — на **практическую применимость**, **реальные примеры**, **best practices**.

---

### **Блок 1: Введение**

---

#### **Слайд 1: Введение в безопасность в Kubernetes**

**Визуальный вид слайда:**  
Титульный слайд: крупный заголовок, подзаголовок, твой логотип/имя, фон — стилизованный Kubernetes-кластер с щитом.

---

**Подробное описание:**  
Этот слайд задаёт тон всему занятию. Мы начинаем с акцента на то, что Kubernetes — мощная платформа, но её безопасность не настроена по умолчанию. Безопасность требует осознанных действий: от контроля доступа до проверки образов.  
Согласно *Kubernetes Best Practices* (Burns et al., 2024), безопасность должна быть встроена в процесс с самого начала, а не добавлена как опция.  
В *The Kubernetes Book* (Poulton, 2024) подчёркивается, что "security is not a feature — it’s a practice".  
Сегодня мы разберём ключевые компоненты: RBAC, NetworkPolicies, Pod Security и сканирование образов.  
Цель — не просто показать "как", но и объяснить "почему" и "что будет, если не сделать".

---

#### **Слайд 2: О чём сегодня?**

**Визуальный вид слайда:**  
4 иконки (замок, сеть, контейнер, лупа) + краткие подписи:  
1. RBAC — контроль доступа  
2. NetworkPolicies — изоляция трафика  
3. Pod Security — безопасные поды  
4. Сканирование — уязвимости в образах  

---

**Подробное описание:**  
Мы разделим занятие на четыре логические части.  
1. **RBAC** — как управлять правами пользователей и сервисных аккаунтов.  
2. **NetworkPolicies** — как изолировать поды и предотвратить lateral movement.  
3. **Pod Security** — как запрещать небезопасные практики (запуск от root, hostPath и др.).  
4. **Сканирование образов** — как находить CVE до деплоя.  
Как указано в *Learning Kubernetes Security* (Lapaz, 2025), эти четыре слоя составляют основу zero-trust подхода в Kubernetes.  
В *Kubernetes Anti-Patterns* (Govardhana, 2024) подчёркивается, что отсутствие хотя бы одного из них — частая причина инцидентов.  
Мы будем использовать **реальные манифесты**, **чек-листы** и **пошаговые инструкции**.

---

#### **Слайд 3: Почему безопасность — это не опция?**

**Визуальный вид слайда:**  
Фон — схема кластера с красной стрелкой от "зловредного пода" к "базе данных". Слева — текст: "Один уязвимый образ → компрометация всего кластера".

---

**Подробное описание:**  
Kubernetes по умолчанию открыт: любой под может общаться с любым, нет контроля доступа, образы не проверяются.  
Пример: если в образе есть CVE (например, Log4Shell), и он запущен с `runAsRoot`, злоумышленник может получить доступ к узлу и перемещаться по кластеру.  
Как описано в *Kubernetes Best Practices* (Burns et al., 2024), "the default security posture of Kubernetes is permissive, not secure".  
В *The Kubernetes Book* (Poulton, 2024) приводится кейс: компания потеряла данные из-за отсутствия NetworkPolicies.  
Согласно *Learning Kubernetes Security* (Lapaz, 2025), 70% атак в Kubernetes начинаются с компрометации пода через уязвимый образ.  
Безопасность — это не "опция для продвинутых", а **необходимое условие работы**.

---

#### **Слайд 4: Что вы получите?**

**Визуальный вид слайда:**  
Список с галочками:  
- Чек-листы для RBAC, NetworkPolicies, PSA  
- Готовые манифесты  
- Инструкции по сканированию  
- Шаблоны для CI/CD  

---

**Подробное описание:**  
После занятия вы сможете:  
- Настроить RBAC с принципом минимальных привилегий.  
- Применить NetworkPolicies для изоляции workloads.  
- Включить Pod Security Admission (PSA) в режиме `restricted`.  
- Интегрировать сканирование образов в CI/CD.  
Как указано в *CKA Study Companion* (Sachdeva, 2025), "security is not just about tools — it’s about processes and checks".  
Мы дадим вам **практические шаблоны**, которые можно сразу использовать в своей среде.  
В *Kubernetes Cookbook* (Naik et al., 2024) подчёркивается важность "recipes you can copy-paste and adapt".  
Вы получите **раздаточные материалы** — PDF с чек-листами и ссылками.

---

### **Блок 2: RBAC — контроль доступа**

---

#### **Слайд 5: RBAC — что это и зачем?**

**Визуальный вид слайда:**  
Схема: User → RoleBinding → Role → Rules (get, list pods). Простая стрелочная диаграмма.

---

**Подробное описание:**  
RBAC (Role-Based Access Control) — это механизм контроля доступа в Kubernetes. Он определяет, кто (Subject) может что (Verb) делать с каким ресурсом (Resource).  
Например: "пользователь `dev-user` может читать поды в namespace `dev`".  
Как указано в *Kubernetes Best Practices* (Burns et al., 2024), "RBAC is critical for a stable and secure environment".  
В *CKA Study Companion* (Sachdeva, 2025) приведена аналогия: "RBAC — это как пропускная система в офисе".  
Без RBAC любой пользователь с доступом к API может удалять поды, читать Secrets, менять конфигурации.  
Цель — дать **минимально необходимые права**.

---

#### **Слайд 6: Сравнение моделей авторизации: RBAC vs ABAC vs Webhook**

**Визуальный вид слайда:**  
Таблица 3x4:  
| Модель | Где хранятся правила | Гибкость | Когда использовать |  
|--------|----------------------|---------|---------------------|  
| RBAC | Kubernetes API (объекты) | Средняя | Основной способ |  
| ABAC | Файл на master-узле | Высокая | Устарела, не рекомендуется |  
| Webhook | Внешний сервис | Очень высокая | Интеграция с LDAP/SSO |

---

**Подробное описание:**  
Kubernetes поддерживает несколько моделей авторизации.  
- **RBAC** — наиболее распространённая. Правила хранятся в API-объектах (`Role`, `ClusterRole`).  
- **ABAC** — правила в JSON-файле на master. Устарела, не рекомендуется.  
- **Webhook** — запросы отправляются во внешний сервис (например, для интеграции с Okta).  
Как указано в *The Kubernetes Book* (Poulton, 2024), "RBAC is the de facto standard for most clusters".  
В *Kubernetes Best Practices* (Burns et al., 2024) говорится: "ABAC is deprecated; RBAC is the way forward".  
Для подавляющего большинства случаев — **используйте RBAC**.

---

#### **Слайд 7: Основные объекты RBAC**

**Визуальный вид слайда:**  
4 блока:  
1. **Role** — права в namespace  
2. **ClusterRole** — права везде  
3. **RoleBinding** — связывает Subject и Role  
4. **ClusterRoleBinding** — глобально  

---

**Подробное описание:**  
- `Role` — определяет права в одном namespace.  
- `ClusterRole` — права на уровне всего кластера (например, `nodes`, `persistentvolumes`).  
- `RoleBinding` — связывает пользователя/группу/ServiceAccount с `Role`.  
- `ClusterRoleBinding` — то же, но для `ClusterRole`.  
Как указано в *CKA Study Companion* (Sachdeva, 2025), "ClusterRoleBinding gives cluster-wide access — use with caution".  
В *Kubernetes Cookbook* (Naik et al., 2024) приведён пример: "don’t give `cluster-admin` to CI/CD pipelines".  
Важно: `RoleBinding` может ссылаться на `ClusterRole`, но действует только в своём namespace.

---

#### **Слайд 8: ServiceAccount — кто такие "пользователи" в Kubernetes?**

**Визуальный вид слайда:**  
Диаграмма:  
- Человек → User (редко используется)  
- CI/CD Pipeline → ServiceAccount (часто)  
- Controller → ServiceAccount  

---

**Подробное описание:**  
В Kubernetes "пользователи" — это не только люди.  
- `User` — редко используется (например, для админов через kubeconfig).  
- `ServiceAccount` — основной способ для приложений и систем.  
Каждый под по умолчанию использует `default` ServiceAccount.  
Как указано в *Kubernetes Best Practices* (Burns et al., 2024), "ServiceAccounts are the identity of your workloads".  
В *CKAD Study Guide* (Muschko, 2024) подчёркивается: "Always create dedicated ServiceAccounts for your apps".  
Пример: `ci-pipeline-sa`, `frontend-sa` — так проще управлять правами.

---

#### **Слайд 9: Пример: дать доступ к Pod'ам в namespace `dev`**

**Визуальный вид слайда:**  
Два YAML-блока:  
1. `Role` — разрешает `get`, `list` pods  
2. `RoleBinding` — связывает `dev-user` с этой ролью  

---

**Подробное описание:**  
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
Как указано в *Kubernetes Cookbook* (Naik et al., 2024), "start with verbs like get, list, watch — avoid create, delete".  
В *CKA Study Companion* (Sachdeva, 2025) рекомендуется тестировать через `kubectl auth can-i`.

---

#### **Слайд 10: Как проверить права?**

**Визуальный вид слайда:**  
Терминал:  
```bash
kubectl auth can-i get pods --as=dev-user -n dev
> yes
kubectl auth can-i delete pods --as=dev-user -n dev
> no
```

---

**Подробное описание:**  
Команда `kubectl auth can-i` — ваш лучший друг.  
Она показывает, может ли пользователь выполнить действие.  
Пример:  
```bash
kubectl auth can-i get secrets --as=system:serviceaccount:ci:ci-sa
```
Как указано в *CKA Study Companion* (Sachdeva, 2025), "this is the fastest way to audit RBAC policies".  
В *Kubernetes Cookbook* (Naik et al., 2024) рекомендуется добавлять эту проверку в CI/CD.

---

(Продолжение следует — ещё 38 слайдов)

---

Хочешь, я:
- Продолжу с **Слайда 11: Антипаттерн: ClusterRoleBinding для всей команды**?
- Подготовлю **весь пакет в Markdown** для импорта в Notion или Obsidian?
- Создам **PDF-чек-листы** на основе этих слайдов?

Напиши — и я продолжу.
