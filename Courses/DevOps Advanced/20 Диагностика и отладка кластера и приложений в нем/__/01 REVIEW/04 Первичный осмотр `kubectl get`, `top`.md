**1. Визуальный ряд**
*   Скриншот терминала: `kubectl get pods -l app=web --watch`
*   Скриншот терминала: `kubectl top node`
*   Скриншот терминала: `kubectl top pod --sort-by=memory`
*   Иконки: список (get), график (top), фильтр (selector), часы (watch).

**2. Демо-сценарий (если уместен)**
*   Показать в реальном времени команду `kubectl get events --watch` и продемонстрировать, как в логах появляются новые события.

**3. Ключевые тезисы**
*   `kubectl get` — основная команда для получения списка объектов.
*   `kubectl top` — ключевой инструмент для анализа потребления ресурсов.
*   Флаги `--selector`, `--sort-by`, `--watch` значительно расширяют возможности команд.

**4. Описание (для выступающего)**
*   **Цель слайда:** Представить базовые команды для первичного анализа состояния кластера.
*   **Ключевое сообщение:** `get` и `top` — это ваши первые шаги в любом диагностическом цикле.
*   **Основные тезисы:**
    *   **`kubectl get`:** Команда для получения списка ресурсов (pods, services, nodes, deployments и т.д.). Ключевой флаг `--selector` позволяет фильтровать объекты по меткам. Флаг `--watch` позволяет наблюдать за изменениями в реальном времени.
    *   **`kubectl top`:** Показывает потребление CPU и памяти нодами (`top node`) и подами (`top pod`). Флаг `--sort-by` позволяет отсортировать результаты по `cpu` или `memory`, что помогает быстро найти "тяжелые" поды.
    *   Обе команды просты в использовании и дают быстрое представление о состоянии кластера.
*   **Связь с источниками:** Тезисы основаны на PDF 2019 года (слайды 4.2, 4.7) и субтитрах (00:02:06 - 00:02:35, 00:08:45 - 00:09:15).

**5. Связь с прошлыми материалами**
*   **Из PDF 2019, слайд 4.7:** "get events--watch- посмотреть за event-объектами в кластере... get type/name-o yaml- получить YAML с объектом из Kubernetes".
*   **Из PDF 2019, слайд 4.2:** "$ kubectl top node NAME CPU(cores) CPU% MEMORY(bytes) MEMORY% ... $ kubectl top pod NAME CPU(cores) MEMORY(bytes)".
*   **Из субтитров 2019:** "Итак, из команд, которые имеют смысл знать для диагностики отладки, самая простая — это команда kubectl top... kubectl describe... kubectl get для дебага интересно несколько вещей, то есть kubectl events-watch".
*   **Из чата 2019:** "alex: а есть ли какой-то консольный top, но для контейнеров? ... Никита Вельгин: А есть kubectl top pods :)".

**6. Типовые вопросы и ответы (Q&A)**
*   **В:** Что делать, если `kubectl top` не работает?
    *   **О:** Убедитесь, что в кластере установлен `metrics-server`. Это основной источник данных для `top`. *(Из субтитров 2019: "единственный минус этой штуки, это то, что она требует наличия метрик-сервера")*.
*   **В:** Можно ли использовать `get` для получения манифеста объекта?
    *   **О:** Да, используйте флаг `-o yaml`. Это полезно для аудита или если вы потеряли исходный файл. *(Из PDF 2019, слайд 4.7: "get type/name-o yaml- получить YAML с объектом из Kubernetes")*.
*   **В:** Как посмотреть логи всех подов с определенной меткой?
    *   **О:** Используйте `kubectl logs -l app=my-app --tail=10`, где `app=my-app` — ваш селектор. *(Из субтитров 2019: "Селектор позволяет выбрать логи со всех подов, с определенными лейблами")*.

**7. Примеры использования / Типовые сценарии**
*   **Мониторинг:** Регулярно запускать `kubectl get pods -l app=web --watch` для отслеживания состояния подов.
*   **Поиск "тяжелых" подов:** Запустить `kubectl top pod --sort-by=memory`, чтобы найти поды, потребляющие больше всего памяти.
*   **Анализ событий:** Запустить `kubectl get events --watch` во время деплоя, чтобы отслеживать, что происходит.

**8. Предложения по дополнительному контенту слайда**
*   Добавить скриншот с выводом `kubectl get events --watch`.
*   Подчеркнуть, что `kubectl top` — это "дыхание" кластера.

---

Вот пример вывода команды `kubectl get events --watch`, которая отображает события в кластере в реальном времени.

```
LAST SEEN   TYPE      REASON              OBJECT                               MESSAGE
10s         Normal    Scheduled           pod/my-app-5c678cf57d-4lk9z          Successfully assigned default/my-app-5c678cf57d-4lk9z to node-1
8s          Normal    Pulling             pod/my-app-5c678cf57d-4lk9z          Pulling image "my-registry/my-app:v1.2"
5s          Normal    Pulled              pod/my-app-5c678cf57d-4lk9z          Successfully pulled image "my-registry/my-app:v1.2"
4s          Normal    Created             pod/my-app-5c678cf57d-4lk9z          Created container my-app
3s          Normal    Started             pod/my-app-5c678cf57d-4lk9z          Started container my-app
15s         Warning   FailedScheduling    pod/my-app-5c678cf57d-4lk9z          0/3 nodes are available: 3 Insufficient cpu.
10s         Normal    Scheduled           pod/my-app-5c678cf57d-4lk9z          Successfully assigned default/my-app-5c678cf57d-4lk9z to node-2
8s          Normal    Pulling             pod/my-app-5c678cf57d-4lk9z          Pulling image "my-registry/my-app:v1.2"
5s          Normal    Pulled              pod/my-app-5c678cf57d-4lk9z          Successfully pulled image "my-registry/my-app:v1.2"
4s          Normal    Created             pod/my-app-5c678cf57d-4lk9z          Created container my-app
3s          Normal    Started             pod/my-app-5c678cf57d-4lk9z          Started container my-app
2m          Normal    Killing             pod/my-app-5c678cf57d-4lk9z          Stopping container my-app
2m          Warning   Unhealthy           pod/my-app-5c678cf57d-4lk9z          Liveness probe failed: HTTP probe failed with statuscode: 500
2m10s       Normal    Created             pod/my-app-5c678cf57d-4lk9z          Created container my-app
2m11s       Normal    Started             pod/my-app-5c678cf57d-4lk9z          Started container my-app
2m12s       Warning   BackOff             pod/my-app-5c678cf57d-4lk9z          Back-off restarting failed container
```

---

### **Пояснение к выводу:**

*   **`LAST SEEN`**: Время, прошедшее с момента последнего появления события.
*   **`TYPE`**: Тип события (`Normal` или `Warning`).
*   **`REASON`**: Краткая причина события (например, `Scheduled`, `FailedScheduling`, `Unhealthy`).
*   **`OBJECT`**: Объект Kubernetes, к которому относится событие.
*   **`MESSAGE`**: Подробное сообщение, описывающее событие.

---

### **Как использовать в диагностике:**

Этот вывод показывает типичный сценарий диагностики:

1.  **Первоначальная ошибка:** Под не может быть запланирован из-за нехватки CPU (`FailedScheduling`).
2.  **Решение проблемы:** Администратор освобождает ресурсы, и под успешно запланирован на другую ноду.
3.  **Новая проблема:** Под запускается, но его `liveness probe` падает с ошибкой 500 (`Unhealthy`), что приводит к его остановке (`Killing`).
4.  **Цикл рестартов:** Под пытается перезапуститься, но снова сталкивается с той же проблемой (`BackOff restarting failed container`), что указывает на `CrashLoopBackOff`.

Этот поток событий позволяет быстро определить, что проблема не в планировании, а в самом приложении.

---

### **Пример вывода команды `kubectl get deployment/my-app -o yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "3"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"my-app"},"name":"my-app","namespace":"default"},"spec":{"replicas":3,"selector":{"matchLabels":{"app":"my-app"}},"template":{"metadata":{"labels":{"app":"my-app"}},"spec":{"containers":[{"image":"my-registry/my-app:v1.3","name":"my-app","ports":[{"containerPort":8080}],"resources":{"limits":{"cpu":"200m","memory":"128Mi"},"requests":{"cpu":"100m","memory":"64Mi"}}}]}}}}
  creationTimestamp: "2023-10-27T12:00:00Z"
  generation: 5
  labels:
    app: my-app
  name: my-app
  namespace: default
  resourceVersion: "987654"
  uid: a1b2c3d4-e5f6-7890-1234-567890123456
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: my-app
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: my-app
    spec:
      containers:
      - image: my-registry/my-app:v1.3
        imagePullPolicy: IfNotPresent
        name: my-app
        ports:
        - containerPort: 8080
          protocol: TCP
        resources:
          limits:
            cpu: 200m
            memory: 128Mi
          requests:
            cpu: 100m
            memory: 64Mi
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 3
  conditions:
  - lastTransitionTime: "2023-10-27T12:00:05Z"
    lastUpdateTime: "2023-10-27T12:00:05Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2023-10-27T12:00:00Z"
    lastUpdateTime: "2023-10-27T12:00:05Z"
    message: ReplicaSet "my-app-7d5b8c9c4" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 5
  readyReplicas: 3
  replicas: 3
  updatedReplicas: 3
```

---

### **Пояснение к выводу:**

*   **`metadata`:** Содержит служебные поля, управляемые Kubernetes:
    *   `creationTimestamp`, `generation`, `resourceVersion`, `uid` — автоматически генерируются и обновляются API-сервером.
    *   `annotations` — могут содержать служебные аннотации, такие как `kubectl.kubernetes.io/last-applied-configuration`, которая хранит конфигурацию, примененную последним `kubectl apply`.
*   **`spec`:** Это единственная секция, которую обычно редактируют вручную. Она определяет желаемое состояние объекта.
*   **`status`:** Содержит текущее состояние объекта, которое обновляется контроллерами. Поля в `status` нельзя изменить напрямую через `apply` или `edit`.

---

### **Как использовать**

1.  **Диагностика:** Для получения полной картины объекта, включая все его текущие настройки и состояние.
2.  **Обучение:** Чтобы понять, как Kubernetes интерпретирует ваш манифест и какие поля добавляет по умолчанию.
3.  **Поиск различий:** Сравнение вывода этой команды с вашим исходным манифестом может показать, где конфигурация "разошлась" с "истиной в коде".
4.  **Восстановление:** Если исходный манифест утерян, `kubectl get -o yaml` может служить его источником (хотя он будет "грязным" из-за служебных полей).

---

### **Связь с материалами**

Эта команда была упомянута в старой презентации (PDF, слайд 4.8: "get type/name-o yaml- получить YAML с объектом из Kubernetes") и остается одним из самых фундаментальных инструментов для работы с Kubernetes.