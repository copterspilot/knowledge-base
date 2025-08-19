
### 🎤 Сценарий выступления (спикерский скрипт)

**0–5 мин: Введение**

- Приветствие. Меня зовут [Имя], сегодня мы рассмотрим ключевые аспекты безопасности Kubernetes.
- Цель: показать, как защищать кластер от основных угроз.

**5–10 мин: Обзор угроз**

- Кластер Kubernetes — это сложная и многокомпонентная система.
- Основные векторы атак: RBAC, привилегии подов, secrets, network policies, supply chain.

**10–20 мин: Демо 1 (RBAC)**

- Открываем YAML 01 и 02.
- Создаем намеспейс, роль, привязку.
- Показываем, что пользователь ограничен в правах.
- Команда: `kubectl auth can-i create pods --as demo-user`

**20–30 мин: Демо 2 (Pod Security Admission)**

- Показ манифеста 03: runAsRoot, privileged.
- Кластер отказывает в принятии, если namespace с лейблами PSA.

**30–40 мин: Демо 3 (Sealed Secrets)**

- YAML 04: демонстрация зашифрованного секрета.
- `kubeseal` CLI для создания.
- Показываем, что админ может деплоить, не зная значения.

**40–50 мин: Демо 4 (Trivy, kube-bench)**

- YAML 05: команды анализа манифестов и кластера.
- `trivy config .`, `trivy k8s cluster`, `kube-bench`

**50–55 мин: Вопросы и ответы**

- Приглашение к вопросам.
- Ответы с краткими пояснениями и ресурсами.

**55–60 мин: Резюме и литература**

- Резюме по каждому этапу.
- Напоминание про лучшие практики.
- Показ списка рекомендуемой литературы.

---

### 💬 Частые вопросы и ответы

**1. Base64 в Kubernetes Secret это шифрование?**  
Скорее, скрытие данных. Base64 легко дешифруется.

**2. Kubernetes Pod Security Policies ещё актуальны?**  
Нет. PSP устарели в 1.21 и удалены в 1.25+. Используйте Pod Security Admission или Kyverno.

**3. Что лучше для контроля RBAC?**  
Текущее покрытие: RBAC Lookup, rakkess, Pluto.

**4. Trivy можно использовать в CI/CD?**  
Да, он имеет CLI/интеграции для GitHub Actions, GitLab CI, Jenkins.

**5. Kubernetes сам шифрует secrets?**  
Если не включено encryption at rest в kube-apiserver, то secrets хранятся в etcd в base64.

---

### 📃 One-pager: Команды из демо

```bash
# 1. Создание namespace
kubectl create ns demo-ns

# 2. Применение RBAC
kubectl apply -f 01-rbac-limited-role.yaml
kubectl apply -f 02-rolebinding.yaml
kubectl auth can-i list pods --as demo-user -n demo-ns

# 3. Проверка PSA
kubectl label ns restricted-ns   pod-security.kubernetes.io/enforce=restricted   pod-security.kubernetes.io/enforce-version=latest
kubectl apply -f 03-insecure-pod.yaml -n restricted-ns

# 4. Sealed Secret (после установки controller и CLI)
kubectl create secret generic mysecret   --dry-run=client --from-literal=password=supersecret -o json > tmp.json
kubeseal -o yaml < tmp.json > 04-sealed-secret-template.yaml
kubectl apply -f 04-sealed-secret-template.yaml

# 5. Анализ манифестов и кластера
trivy config .
trivy k8s cluster
kube-bench --version
```

---

### ✅ Чеклист безопасности Kubernetes-кластера

- [ ] Ограничения RBAC и отсутствие cluster-admin по умолчанию
- [ ] Внедрён Pod Security Admission или Kyverno (restricted policies)
- [ ] Секреты шифруются (Sealed Secrets, Vault, encryption at rest)
- [ ] Сканирование манифестов и образов через Trivy и kube-bench
- [ ] Открытые ingress и service ports контролируются через network policies и auth
- [ ] Контейнеры работают с runAsNonRoot, ограничены по capabilities и host access
- [ ] Внедрено CI/CD-сканирование перед деплоем
- [ ] Включён audit logging в kube-apiserver
- [ ] etcd зашифрован, доступ ограничен
- [ ] Регулярное обновление образов и кластерных компонентов
