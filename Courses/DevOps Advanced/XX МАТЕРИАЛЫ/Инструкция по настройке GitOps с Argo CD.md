На основе анализа всех предоставленных книг и материалов (включая `Vinto Natale, Bueno Alex Soto - GitOps Cookbook`, `Boorshtein Marc, Surovich Scott - Kubernetes An Enterprise Guide`, `Salatino M. - Platform Engineering on Kubernetes`), вот **подробная инструкция по настройке GitOps с Argo CD**.

---

### **Инструкция по настройке GitOps с Argo CD**

**Цель:** Настроить автоматизированное управление состоянием кластера Kubernetes с помощью Argo CD, где Git-репозиторий является "единственным источником истины".

---

#### **1. Установка Argo CD в кластер Kubernetes**

Первый шаг — развернуть сам Argo CD как контроллер в вашем кластере.

```bash
# 1. Создать namespace для Argo CD
kubectl create namespace argocd

# 2. Переключиться на этот namespace
kubectl config set-context --current --namespace=argocd

# 3. Применить манифест установки (используется последняя стабильная версия)
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

> **Примечание:** В production рекомендуется использовать [Helm Chart](https://argo-cd.readthedocs.io/en/stable/operator-manual/helm/) или [Kustomize](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/) для более гибкой настройки.

---

#### **2. Доступ к веб-интерфейсу Argo CD**

Для настройки мы будем использовать веб-интерфейс.

```bash
# 1. Пробросить порт веб-интерфейса на локальную машину
kubectl port-forward svc/argocd-server -n argocd 8080:443

# 2. Открыть браузер
# Перейдите по адресу: https://localhost:8080
```

*   **Логин:** `admin`
*   **Пароль:** Получите начальный пароль из секрета:
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

---

#### **3. Подготовка Git-репозитория (Источник истины)**

Создайте репозиторий, который будет содержать манифесты Kubernetes.

*   **Рекомендованная структура:**
    ```
    your-gitops-repo/
    ├── apps/
    │   ├── dev/
    │   │   └── my-app.yaml
    │   ├── staging/
    │   │   └── my-app.yaml
    │   └── prod/
    │       └── my-app.yaml
    └── cluster-config/
        └── namespaces.yaml
    ```
    *   Разделение по средам (dev, staging, prod) позволяет использовать разные политики синхронизации.
    *   Используйте `Kustomize` или `Helm` для шаблонизации манифестов.

---

#### **4. Создание Argo CD Application**

Application — это CRD (Custom Resource Definition), который описывает, что, откуда и куда нужно деплоить.

**Способ 1: Через веб-интерфейс**

1.  Войдите в интерфейс Argo CD.
2.  Нажмите **"NEW APP"**.
3.  Заполните форму:
    *   **Application Name:** `my-app-dev`
    *   **Project:** `default`
    *   **SYNC POLICY:** `Automatic` (для автоматической синхронизации)
    *   **REPO URL:** `https://github.com/ваш-пользователь/your-gitops-repo.git`
    *   **REVISION:** `HEAD` (или конкретный бранч, например, `main`)
    *   **PATH:** `apps/dev` (путь к манифестам в репозитории)
    *   **CLUSTER URL:** `https://kubernetes.default.svc` (для локального кластера)
    *   **NAMESPACE:** `dev` (namespace, в который будет деплоиться приложение)

**Способ 2: Через манифест (рекомендуется для GitOps)**

Создайте файл `my-app-dev.yaml` в вашем GitOps-репозитории:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app-dev
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/ваш-пользователь/your-gitops-repo.git'
    targetRevision: main
    path: apps/dev
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: dev
  syncPolicy:
    automated:
      prune: true # Удалять ресурсы, удаленные из Git
      selfHeal: true # Автоматически исправлять конфигурацию, измененную вручную
```

Примените этот манифест в кластер:
```bash
kubectl apply -f my-app-dev.yaml -n argocd
```

---

#### **5. Настройка автоматической синхронизации**

Теперь Argo CD будет постоянно следить за вашим репозиторием.

*   **Как это работает:**
    1.  Вы вносите изменения в манифесты в Git (например, обновляете тег образа).
    2.  Argo CD (каждые 3 минуты по умолчанию) обнаруживает изменения.
    3.  Argo CD применяет изменения к кластеру Kubernetes.
    4.  Состояние кластера всегда соответствует состоянию в Git.

*   **Опции синхронизации:**
    *   **`prune: true`**: Удаляет ресурсы из кластера, если они были удалены из Git.
    *   **`selfHeal: true`**: Автоматически возвращает кластер в желаемое состояние, если кто-то вручную изменил конфигурацию (`kubectl edit`).

---

#### **6. Интеграция с CI/CD пайплайном**

GitOps не заменяет CI, а дополняет его. CI отвечает за сборку, тестирование и публикацию образов.

*   **Процесс:**
    1.  **CI:** Собирает приложение, тестирует, сканирует на уязвимости (`Trivy`), пушит образ в реестр (Harbor, Docker Hub).
    2.  **CI (финальный шаг):** Обновляет тег образа в манифесте в GitOps-репозитории.
        ```bash
        # Пример: Обновить тег в манифесте
        yq e '.spec.template.spec.containers[0].image = "my-registry/my-app:v1.2.3"' apps/dev/my-app.yaml
        git add apps/dev/my-app.yaml
        git commit -m "Deploy my-app v1.2.3"
        git push origin main
        ```
    3.  **Argo CD:** Обнаруживает изменение в Git и автоматически деплоит новую версию.

---

#### **7. Расширенные практики**

*   **Управление секретами:** Не храните секреты в открытом виде в Git.
    *   **Решение:** Используйте [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) или интегрируйте Argo CD с [HashiCorp Vault](https://argo-cd.readthedocs.io/en/stable/user-guide/secrets/).
*   **Многоуровневая доставка (Progressive Delivery):** Для продакшена используйте стратегии типа canary или blue/green.
    *   **Решение:** Интегрируйте [Argo Rollouts](https://argoproj.github.io/argo-rollouts/).
*   **Управление несколькими кластерами:** Argo CD может управлять множеством кластеров.
    *   **Решение:** Добавьте дополнительные кластеры в Argo CD UI (`Settings` -> `Clusters`).

---

#### **8. Проверка работоспособности**

*   Перейдите в веб-интерфейс Argo CD.
*   Вы увидите список ваших приложений.
*   Состояние приложения должно быть `Synced` (синхронизировано) и `Healthy` (здоровое).
*   Внесите тестовое изменение в манифест в Git и убедитесь, что Argo CD автоматически обновил приложение в кластере.

---

✅ **Инструкция завершена.**

Следуя этим шагам, вы создадите надежную и автоматизированную систему доставки, основанную на принципах GitOps, что является стандартом для современных DevOps-организаций.