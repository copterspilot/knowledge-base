**1. Визуальный ряд**
*   Скриншот терминала: `kubectl describe pod my-app-pod-7d5b8c9c4-xl2v9` с выделенной секцией `Events`.
*   Скриншот терминала: `kubectl logs my-app-pod-7d5b8c9c4-xl2v9 --previous` (логи упавшего контейнера).
*   Скриншот терминала: `kubectl logs -l app=my-app --tail=10` (агрегированные логи всех подов с меткой).
*   Иконки: документ (describe), лог (logs), часы (watch).

**2. Демо-сценарий (если уместен)**
*   Показать в реальном времени команду `kubectl describe pod <имя проблемного пода>` и продемонстрировать, как в секции `Events` видна причина `CrashLoopBackOff` (например, `Back-off restarting failed container`).

**3. Ключевые тезисы**
*   `kubectl describe` — ключевой инструмент для получения полной информации о состоянии объекта.
*   `kubectl logs` — основной способ анализа вывода приложения.
*   Секция `Events` в `describe` и флаг `--previous` в `logs` незаменимы для диагностики проблем.

**4. Описание (для выступающего)**
*   **Цель слайда:** Представить два самых важных инструмента для детальной диагностики проблем с подами.
*   **Ключевое сообщение:** Когда что-то идет не так, `describe` и `logs` — это ваши первые и самые мощные союзники.
*   **Основные тезисы:**
    *   **`kubectl describe`:** Команда выводит детальную информацию о "живом" объекте: его текущую конфигурацию, состояние (`Conditions`), и, самое важное, **связанные события (`Events`)**. События — это журнал действий и ошибок, связанных с объектом (например, `FailedScheduling`, `FailedMount`, `Back-off restarting failed container`), которые являются ключом к пониманию проблемы.
    *   **`kubectl logs`:** Команда показывает `stdout` и `stderr` контейнеров в поде. Ключевые флаги:
        *   `--follow` (`-f`): Следить за появлением новых строк в логах (аналог `tail -f`).
        *   `--previous` (`-p`): Показать логи предыдущего экземпляра контейнера. Это критически важно при диагностики `CrashLoopBackOff`, когда нужно увидеть, почему упал предыдущий контейнер.
        *   `--selector`: Агрегировать логи всех подов с определенными метками в один поток.
*   **Связь с источниками:** Тезисы основаны на PDF 2019 года (слайды 4.3, 4.7), субтитрах (00:03:03 - 00:04:33, 00:06:43 - 00:08:31) и чате (вопрос о логах).

**5. Связь с прошлыми материалами**
*   **Из PDF 2019, слайд 4.3:** "kubectl describe- выводит полную информацию о запрошенном объекте: текущую конфигурацию связанные события(events) текущее состояние(PodStatus, IP-адреса и подобное)".
*   **Из субтитров 2019:** "Qtel Describe. Describe — это самый тоже могучий инструмент при отладке..." (00:03:03). "И очень полезный ключ, это ключ превиос, который позволяет посмотреть логи старого контейнера, если контейнер рестартовал." (00:08:10).
*   **Из чата 2019:** "Павел Козлов: Если контейнеров несколько, логи какого по дефолту показываются? ... Никита Вельгин: Вроде с никакого, попросит выбрать ... Error from server (BadRequest): a container name must be specified for pod ..." (Демонстрирует необходимость указывать контейнер при нескольких контейнерах).

**6. Типовые вопросы и ответы (Q&A)**
*   **В:** Где искать причину, если под в статусе `CrashLoopBackOff`?
    *   **О:** Сначала выполните `kubectl describe pod <имя>`. В секции `Events` вы увидите последовательность событий. Затем выполните `kubectl logs <имя> --previous`, чтобы посмотреть логи упавшего контейнера.
*   **В:** Как посмотреть логи всех контейнеров в поде?
    *   **О:** Используйте флаг `--all-containers=true`. Команда: `kubectl logs <имя_пода> --all-containers=true`.
*   **В:** Можно ли посмотреть логи только за последние 5 минут?
    *   **О:** Да, используйте флаг `--since=5m`. Например: `kubectl logs <имя_пода> --since=5m`.

**7. Примеры использования / Типовые сценарии**
*   **Диагностика `CrashLoopBackOff`:** Использовать `describe` для просмотра `Events` и `logs --previous` для анализа причин падения.
*   **Анализ проблем с монтированием томов:** Использовать `describe` для просмотра событий типа `FailedMount`, `FailedAttachVolume`.
*   **Мониторинг приложения в реальном времени:** Использовать `kubectl logs -f <имя_пода>` для просмотра логов по мере их появления.

**8. Предложения по дополнительному контенту слайда**
*   Добавить скриншот `describe` с явно выделенной секцией `Events`.
*   Подчеркнуть, что `Events` — это "хроника жизни" объекта.

---

Вот пример вывода команды `kubectl describe pod`, демонстрирующий полную информацию о поде.

```
Name:             my-app-5c678cf57d-4lk9z
Namespace:        default
Priority:         0
Node:             node-1/10.1.2.3
Start Time:       Fri, 23 Aug 2019 11:36:32 +0300
Labels:           app=my-app
                  pod-template-hash=5c678cf57d
Annotations:      <none>
Status:           Running
IP:               10.244.1.12
Controlled By:    ReplicaSet/my-app-5c678cf57d
Containers:
  my-app:
    Container ID:   docker://a1b2c3d4e5f6789012345678901234567890123456789012345678901234
    Image:          my-registry/my-app:v1.2
    Image ID:       docker-pullable://my-registry/my-app@sha256:abcdef1234567890
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 23 Aug 2019 11:36:37 +0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  128Mi
    Requests:
      cpu:        100m
      memory:     64Mi
    Liveness:     http-get http://:8080/health delay=30s timeout=1s period=10s #success=1 #failure=3
    Readiness:    http-get http://:8080/ready delay=5s timeout=1s period=5s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sflrb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-sflrb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-sflrb
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  2m    default-scheduler  Successfully assigned default/my-app-5c678cf57d-4lk9z to node-1
  Normal   Pulling    90s   kubelet            Pulling image "my-registry/my-app:v1.2"
  Normal   Pulled     85s   kubelet            Successfully pulled image "my-registry/my-app:v1.2"
  Normal   Created    84s   kubelet            Created container my-app
  Normal   Started    83s   kubelet            Started container my-app
```

---

### **Пояснение к выводу:**

Команда `kubectl describe` предоставляет детальную информацию о состоянии объекта Kubernetes. Вывод разделен на секции:

*   **Общая информация:** Имя, namespace, нода, статус, IP-адрес.
*   **Containers:** Подробности о каждом контейнере (образ, состояние, ресурсы, пробы).
*   **Conditions:** Состояние жизненного цикла пода (например, `Ready: True`).
*   **Volumes:** Список томов, смонтированных в под.
*   **Events:** **Ключевая секция для диагностики.** Показывает хронологию событий, связанных с подом (например, `FailedScheduling`, `FailedMount`, `Back-off restarting failed container`).

---

### **Как использовать в диагностике:**

Согласно материалам, `describe` — это "самый могучий инструмент при отладке" (субтитры 00:03:10). Для диагностики проблемы с подом всегда нужно смотреть секцию `Events`. Например, если под в статусе `Pending`, события покажут `FailedScheduling` и причину (например, `Insufficient cpu`).

---
