### **Статья: Практическое руководство по Pod Security Standards (PSS)**

#### **Введение**

Представьте, что вы развертываете приложение, и в его манифесте случайно или намеренно установлено `securityContext.privileged: true`. Этот под получает почти полный контроль над хостом, что создает огромную уязвимость. Раньше для предотвращения таких сценариев использовались **Pod Security Policies (PSP)**. Однако, как указано в `Lapaz Raul - Learning Kubernetes Security, 2nd Edition - 2025`, PSP были удалены в Kubernetes v1.25 из-за своей сложности. На их место пришли **Pod Security Standards (PSS)** — современная, встроенная и упрощенная система безопасности. Эта статья проведет вас от основ к практическому применению PSS.

#### **Часть 1: Что такое Pod Security Standards (PSS)?**

Pod Security Standards (PSS) — это набор **рекомендаций по безопасности**, определенных рабочей группой по безопасности Kubernetes. Они описывают, какие настройки в спецификации Pod (`PodSpec`) являются безопасными, а какие — нет.

*   **Проблема с PSP:** PSP были мощными, но чрезвычайно сложными в настройке и отладке. Их удаление оставило потребность в простом и надежном механизме.
*   **Решение: PSS и Pod Security Admission (PSA):** PSS — это стандарты, а **Pod Security Admission (PSA)** — это встроенный в Kubernetes **admission controller**, который применяет эти стандарты. PSA стал стандартом де-факто для базовой безопасности подов.
*   **Ключевое отличие:** PSA работает на уровне **namespace**, а не на уровне всего кластера, что упрощает его настройку и управление.

#### **Часть 2: Три уровня безопасности PSS**

PSS определяет три кумулятивных уровня строгости:

1.  **`Privileged` (привилегированный):**
    *   **Описание:** Никаких ограничений. Разрешено все.
    *   **Использование:** Только для системных namespace'ов (`kube-system`). **Не рекомендуется** для рабочих нагрузок.
    *   **Источник:** `Lapaz Raul - Learning Kubernetes Security, 2nd Edition - 2025`

2.  **`Baseline` (базовый):**
    *   **Описание:** Запрещает явно опасные практики, но позволяет большинству приложений работать без изменений.
    *   **Примеры запрещенных действий:** Запуск от `root` (если `runAsNonRoot: false`), использование привилегированных контейнеров (`privileged: true`), монтирование чувствительных директорий хоста (например, `/proc`).
    *   **Использование:** Отличный стартовый уровень для большинства production-приложений.
    *   **Источник:** `Burns B., Villalba E., Strebel D., Evenson L. - Kubernetes Best Practices - 2024`

3.  **`Restricted` (ограниченный):**
    *   **Описание:** Самый строгий уровень, соответствующий рекомендациям CIS (Center for Internet Security).
    *   **Примеры требований:** Обязательное `runAsNonRoot: true`, обязательное `readOnlyRootFilesystem: true`, строгие ограничения на `capabilities`.
    *   **Использование:** Рекомендуется для максимально безопасной среды. Может потребовать изменений в манифестах существующих приложений.
    *   **Источник:** `Lapaz Raul - Learning Kubernetes Security, 2nd Edition - 2025`

#### **Часть 3: Практическое применение PSS с Pod Security Admission (PSA)**

PSA включен по умолчанию в большинстве дистрибутивов Kubernetes, начиная с версии 1.25. Управляется он с помощью **меток namespace**.

##### **Шаг 1: Проверка состояния PSA**

Убедитесь, что PSA включен. Попробуйте применить метку к namespace. Если вы получаете ошибку `unknown label`, значит, PSA не включен (требуется настройка API-сервера).

##### **Шаг 2: Настройка политик для namespace**

Примените политики к namespace с помощью `kubectl label`.

*   **Настройка принудительного применения (`enforce`):**
    ```bash
    # Применить уровень 'baseline' к namespace 'production'
    kubectl label --overwrite ns production \
      pod-security.kubernetes.io/enforce=baseline \
      pod-security.kubernetes.io/enforce-version=v1.25
    ```
    Теперь любой под, который нарушает правила `baseline`, будет **заблокирован** от создания.

*   **Настройка предупреждения (`warn`):**
    ```bash
    # Выдать предупреждение, если под нарушает более строгий уровень 'restricted'
    kubectl label --overwrite ns production \
      pod-security.kubernetes.io/warn=restricted \
      pod-security.kubernetes.io/warn-version=v1.25
    ```
    При попытке создать под, нарушающий `restricted`, пользователь получит предупреждение, но под будет создан.

*   **Настройка аудита (`audit`):**
    ```bash
    # Записать в логи, если под нарушает уровень 'restricted'
    kubectl label --overwrite ns production \
      pod-security.kubernetes.io/audit=restricted \
      pod-security.kubernetes.io/audit-version=v1.25
    ```
    Используется для мониторинга и сбора статистики без блокировки.

##### **Шаг 3: Тестирование политик**

1.  **Создайте манифест пода, нарушающий политику:**
    ```yaml
    # privileged-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: privileged-pod
    spec:
      containers:
      - name: nginx
        image: nginx:1.25-alpine
        securityContext:
          privileged: true # Это нарушает как 'baseline', так и 'restricted'
    ```
2.  **Попробуйте применить его:**
    ```bash
    kubectl apply -f privileged-pod.yaml -n production
    ```
    Если политика `enforce=baseline` включена, команда завершится с ошибкой: `pods "privileged-pod" is forbidden: violates PodSecurity "baseline:latest": privileged (container "nginx" must not set securityContext.privileged=true)`.

#### **Часть 4: Альтернативы: `Kyverno` и `OPA/Gatekeeper`**

Хотя PSA — это отличный встроенный инструмент, он имеет ограниченную гибкость.

*   **`Kyverno`:**
    *   **Преимущества:** Политики пишутся на YAML, что делает их понятными. Отлично интегрируется с GitOps.
    *   **Пример:** Вы можете создать политику, которая **автоматически добавляет** `readOnlyRootFilesystem: true` к подам, которые его не имеют.
*   **`OPA/Gatekeeper`:**
    *   **Преимущества:** Использует язык Rego, что позволяет создавать очень сложные, условные политики.
    *   **Недостатки:** Требует изучения языка Rego.

#### **Чек-лист: Внедрение PSS**

| Этап                             | Действие                                                                                   |
|:-------------------------------- |:------------------------------------------------------------------------------------------ |
| **1. Оценка**                    | Проведите аудит своих манифестов на предмет использования `privileged`, `runAsRoot` и т.д. |
| **2. Подготовка**                | Начните с применения `warn` и `audit` для уровня `restricted` в тестовых namespace'ах.     |
| **3. Принудительное применение** | После исправления манифестов включите `enforce=baseline` для production.                   |
| **4. Повышение уровня**          | Постепенно переходите к `enforce=restricted` для новых приложений.                         |
| **5. Автоматизация**             | Интегрируйте проверки в CI/CD и используйте `Kyverno` для дополнительных правил.           |

#### **Заключение**

Pod Security Standards и Pod Security Admission — это не просто замена PSP, а **эволюция подхода к безопасности**. Они делают базовую защиту простой, встроенной и доступной для всех. PSA — это стандарт де-факто для базовой безопасности подов. Начните с применения уровня `baseline` и постепенно переходите к `restricted`. В следующей статье мы рассмотрим, как использовать `Kyverno` для создания еще более сложных политик.

#### **Ссылки**
*   `Lapaz Raul - Learning Kubernetes Security, 2nd Edition - 2025` — Глава о PSS.
*   `Burns B., Villalba E., Strebel D., Evenson L. - Kubernetes Best Practices - 2024` — Глава о безопасности.
*   `Ibryam Bilgin, Huss Roland - Kubernetes Patterns, 2nd Edition - 2023` — Глава о безопасности.
*   Официальная документация Kubernetes: `https://kubernetes.io/docs/concepts/security/pod-security-standards/`