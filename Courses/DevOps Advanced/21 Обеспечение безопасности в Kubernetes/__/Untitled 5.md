Отлично! Учитывая, что:
- аудитория — **DevOps-специалисты** (понимают жаргон),
- цель — **практическая применимость**,
- можно сделать **до 50 слайдов**,
- важны: **пошаговые инструкции, чек-листы, best practices, реальные примеры**,

— я подготовил **подробный, насыщенный практикой план презентации из 45–50 слайдов**, разбитый на **логические блоки**, с акцентом на **реальные кейсы, сравнения, инструкции и шпаргалки**.

---

## 🎯 **Тема:**  
**«Обеспечение безопасности в Kubernetes: от теории к практике»**  
**Аудитория:** DevOps-инженеры, SRE, платформенные инженеры  
**Цель:** Научить настраивать RBAC, NetworkPolicies, PodSecurity, сканирование образов  
**Формат:** пошагово, с чек-листами, примерами из реальных книг и практики

---

# 📚 Структура презентации (всего: 48 слайдов)

---

## 🔹 Блок 1: Введение (4 слайда)

| № | Тема |
|---|------|
| 1 | **Титульный слайд**  
Тема, ваше имя, дата |
| 2 | **О чём сегодня?**  
4 темы: RBAC, NetworkPolicies, PodSecurity, сканирование |
| 3 | **Почему безопасность — это не опция?**  
Пример: компрометация через `latest` + CVE + отсутствие NetworkPolicy |
| 4 | **Что вы получите?**  
Чек-листы, манифесты, best practices, шаблоны для CI |

---

## 🔹 Блок 2: RBAC — контроль доступа (12 слайдов)

| № | Тема |
|---|------|
| 5 | **RBAC — что это и зачем?**  
Аналогия: пропускная система в офисе |
| 6 | **Сравнение моделей авторизации**  
- RBAC vs ABAC vs Webhook vs Node Authorizer  
- Когда что использовать |
| 7 | **Основные объекты RBAC**  
`Role`, `ClusterRole`, `RoleBinding`, `ClusterRoleBinding` — таблица |
| 8 | **ServiceAccount — кто такие "пользователи" в Kubernetes?**  
(Из книги Бернса: "ServiceAccount — для программ, User — для людей") |
| 9 | **Пример: дать доступ к Pod'ам в namespace `dev`**  
Манифест `Role` + `RoleBinding` |
| 10 | **Как проверить права?**  
`kubectl auth can-i get pods --as=dev-user` |
| 11 | **Антипаттерн: ClusterRoleBinding для всей команды**  
(Из *Anti-Patterns*): `name: dev-team` → `cluster-admin` |
| 12 | **Best Practice: namespace-scoped access**  
Ограничьте доступ по namespace |
| 13 | **Best Practice: least privilege**  
Не давайте `*/*` — только нужные ресурсы и глаголы |
| 14 | **Пример: запретить доступ к Secrets**  
(Из *Kubernetes Cookbook*): `rules: - resources: ["secrets"] verbs: []` |
| 15 | **Чек-лист: безопасный RBAC**  
- Используйте Role, а не ClusterRole, если можно  
- Проверяйте через `auth can-i`  
- Аудитуйте раз в месяц |
| 16 | **RBAC в CI/CD: как дать pipeline'у права?**  
Создайте `ServiceAccount` + `RoleBinding` для `namespace: ci-cd` |

---

## 🔹 Блок 3: NetworkPolicies — сетевая изоляция (10 слайдов)

| № | Тема |
|---|------|
| 17 | **По умолчанию: всё открыто**  
(Из *Бернса*): "По умолчанию любой под может взаимодействовать с любым" |
| 18 | **NetworkPolicy — это firewall для подов**  
Аналогия: межсетевой экран между микросервисами |
| 19 | **Какие CNI поддерживают?**  
Calico, Cilium, Weave, Kube-ovn — таблица |
| 20 | **Структура NetworkPolicy**  
`podSelector`, `ingress`, `egress`, `from`, `ports` |
| 21 | **Пример: только frontend → backend**  
Манифест с `matchLabels: app: frontend` |
| 22 | **Пример: запретить весь egress, кроме DNS**  
Для подов с `app: payment` |
| 23 | **Best Practice: deny-by-default**  
Включите "запрещено всё, разрешено — по списку" |
| 24 | **Антипаттерн: нет NetworkPolicies в проде**  
(Из *Anti-Patterns*): Lateral movement после компрометации |
| 25 | **Чек-лист: безопасные NetworkPolicies**  
- Всегда применяйте `default-deny`  
- Изолируйте sensitive workloads (payment, db)  
- Тестируйте через `curl` между подами |
| 26 | **Демо: как проверить, работает ли политика?**  
`kubectl exec pod-a -- curl pod-b` → до и после |

---

## 🔹 Блок 4: Pod Security — от PSP к PSA (10 слайдов)

| № | Тема |
|---|------|
| 27 | **PSP умер. Да здравствует PSA.**  
(Из *Learning Kubernetes Security*): PSP удалён в v1.25 |
| 28 | **PSA — что это и как включить?**  
`--enable-admission-plugins=PodSecurity` |
| 29 | **Три уровня PSA: privileged, baseline, restricted**  
Таблица: что разрешено/запрещено |
| 30 | **Пример: включить restricted в namespace**  
```yaml
labels:
  pod-security.kubernetes.io/enforce: restricted
```
 |
| 31 | **Что запрещает `restricted`?**  
- `runAsNonRoot: true`  
- `readOnlyRootFilesystem: true`  
- `allowPrivilegeEscalation: false` |
| 32 | **Пример: под с `runAsUser: 0` — будет заблокирован**  
Вывод ошибки из `kubectl apply` |
| 33 | **Best Practice: начните с `audit` и `warn`**  
Сначала мониторьте, потом включайте `enforce` |
| 34 | **Антипаттерн: привилегированные контейнеры без необходимости**  
(Из *Уильям Д.*): "Не запускайте от root, если не нужно" |
| 35 | **Чек-лист: безопасные поды**  
- PSA: `restricted` в prod  
- `securityContext` в манифестах  
- Сканируйте на `allowPrivilegeEscalation` |
| 36 | **Дополнительно: seccomp, AppArmor, SELinux**  
(Из *Kubernetes Secrets Handbook*): защита на уровне ОС |

---

## 🔹 Блок 5: Сканирование образов (8 слайдов)

| № | Тема |
|---|------|
| 37 | **Образы — это бомбы замедленного действия**  
(Из *The Kubernetes Book*): "Image scanning for known vulnerabilities" |
| 38 | **Инструменты: Trivy, Grype, Snyk, Clair**  
Сравнение: open source vs enterprise |
| 39 | **Пример: сканируем `nginx:1.25`**  
```bash
trivy image nginx:1.25
``` |
| 40 | **Как читать вывод Trivy?**  
CVE, severity, package, fix version |
| 41 | **Best Practice: сканирование в CI**  
(Из *Poulton*): "Promotion and quarantining of images based on scan results" |
| 42 | **Пример: остановить деплой при HIGH/Critical**  
GitHub Actions: `if: failure()` |
| 43 | **SBOM — Software Bill of Materials**  
(Из *Learning Kubernetes Security*): `trivy sbom .` |
| 44 | **Чек-лист: безопасные образы**  
- Не используйте `latest`  
- Сканируйте в CI  
- Используйте approved base images  
- Подписывайте образы (Cosign) |

---

## 🔹 Блок 6: Best Practices & Чек-листы (6 слайдов)

| № | Тема |
|---|------|
| 45 | **Чек-лист: безопасность в CI/CD**  
- Сканирование образов  
- Проверка манифестов (kube-linter)  
- Подпись образов (Cosign) |
| 46 | **Чек-лист: безопасность в кластере**  
- PSA: `restricted` в prod  
- NetworkPolicy: deny-by-default  
- RBAC: least privilege |
| 47 | **Best Practices из книг**  
- Из *Burns*: "RBAC — critical for stable environment"  
- Из *Poulton*: "Workload isolation"  
- Из *Уильям Д.*: "No latest tags" |
| 48 | **Где учиться дальше?**  
- CKS (Certified Kubernetes Security Specialist)  
- CIS Benchmark  
- [kubernetes.io/security](https://kubernetes.io/security/)  
- Репозиторий с чек-листами (ваш GitHub) |

---

## ✅ Что включено в презентацию

| Элемент | Где взято |
|--------|---------|
| **Реальные примеры манифестов** | *Kubernetes Cookbook*, *Burns*, *Уильям Д.* |
| **Антипаттерны** | *Kubernetes Anti-Patterns*, *Burns* |
| **Best Practices** | *The Kubernetes Book*, *Learning Kubernetes Security* |
| **Чек-листы** | Агрегированы из всех источников |
| **Сравнение моделей** | RBAC vs ABAC — из практики |
| **Пошаговые инструкции** | Для RBAC, PSA, NetworkPolicy, сканирования |

---

## 🛠 Что я могу сделать дальше?

Хочешь, я:
1. **Подготовлю первые 10 слайдов в текстовом виде** (с манифестами и пояснениями)?
2. **Создам шаблон презентации в Markdown** (для быстрого импорта)?
3. **Соберу все чек-листы в один PDF-файл** (раздаточный материал)?
4. **Подготовлю репозиторий с манифестами и CI-скриптами**?

Напиши — и я сразу подготовлю контент.