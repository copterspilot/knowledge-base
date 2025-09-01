Отлично! На основе анализа всех предоставленных материалов, вот **справочник по всем типовым ошибкам в Kubernetes**, включая их причины, диагностику и решения.

---

## **Справочник по типовым ошибкам в Kubernetes**

Этот справочник охватывает наиболее распространенные проблемы, с которыми сталкиваются инженеры, и предоставляет четкий алгоритм действий для их решения.

---

### **1. `ImagePullBackOff`**

*   **Что это:** Контейнер не может быть запущен, потому что Kubernetes не может загрузить Docker-образ.
*   **Причины:**
    *   Неверное имя образа или тег (опечатка).
    *   Отсутствие доступа к реестру образов (registry).
    *   Неверные учетные данные в `imagePullSecret`.
    *   Образ не существует в реестре.
*   **Диагностика:**
    ```bash
    # 1. Проверить статус пода
    kubectl get pods

    # 2. Получить детальную информацию (ключевой шаг!)
    kubectl describe pod <имя_пода>
    # В секции `Events` ищите сообщения вроде:
    # "Failed to pull image 'registry.com/image:tag': rpc error: code = Unknown desc = failed to pull and unpack image: failed to resolve reference: pull access denied for registry.com/image, repository does not exist or may require authorization"

    # 3. Проверить события кластера
    kubectl get events --sort-by=.metadata.creationTimestamp
    ```
*   **Решение:**
    *   Проверьте имя образа и тег в манифесте.
    *   Убедитесь, что реестр доступен.
    *   Проверьте правильность `imagePullSecret` и его привязку к сервисной учетной записи.
    *   Убедитесь, что у сервисной учетной записи есть права на чтение образа.

---

### **2. `CrashLoopBackOff`**

*   **Что это:** Контейнер запускается, немедленно падает, и Kubernetes входит в цикл перезапуска с экспоненциально возрастающей задержкой.
*   **Причины:**
    *   Ошибка в коде приложения (например, `panic`, `Connection refused`).
    *   Неправильная конфигурация (неверные переменные окружения, отсутствующий конфигурационный файл).
    *   Недостаток ресурсов (памяти).
    *   Ошибка монтирования тома.
*   **Диагностика:**
    ```bash
    # 1. Проверить статус пода
    kubectl get pods

    # 2. Получить детальную информацию
    kubectl describe pod <имя_пода>
    # Ищите события: `FailedMount`, `OOMKilled`, `FailedScheduling`.

    # 3. Просмотреть логи (ключевой шаг!)
    # Логи текущего (возможно, неработающего) контейнера
    kubectl logs <имя_пода>
    # Логи УПАВШЕГО контейнера (самое важное!)
    kubectl logs <имя_пода> --previous

    # 4. Интерактивная отладка (если можно)
    # Если контейнер с sh/bash
    kubectl exec -it <имя_пода> -- sh
    # Если distroless образ
    kubectl debug -it <имя_пода> --image=nicolaka/netshoot -- sh
    ```
*   **Решение:**
    *   Проанализировать логи из `--previous` для поиска фатальной ошибки.
    *   Проверить `describe` на наличие `OOMKilled` (увеличить `resources.limits.memory`) или `FailedMount` (исправить PVC/Secret).
    *   Проверить переменные окружения, ConfigMap и Secret.

---

### **3. `Pending`**

*   **Что это:** Под создан, но не запланирован на узел.
*   **Причины:**
    *   Недостаток ресурсов (CPU, память) на всех узлах.
    *   Неудовлетворенные требования к узлам (nodeSelector, nodeAffinity).
    *   Проблемы с привязкой тома (PersistentVolumeClaim не может быть привязан).
    *   Недостаток прав (например, для создания тома).
*   **Диагностика:**
    ```bash
    # 1. Проверить статус пода
    kubectl get pods

    # 2. Получить детальную информацию (ключевой шаг!)
    kubectl describe pod <имя_пода>
    # В секции `Events` ищите сообщения вроде:
    # "0/3 nodes are available: 3 Insufficient cpu."
    # "0/3 nodes are available: 1 node(s) didn't match node selector, 2 node(s) had volume node affinity conflict."
    # "waiting for a volume to be created, either by external provisioner or manually created by system administrator"

    # 3. Проверить состояние узлов
    kubectl get nodes

    # 4. Проверить состояние PVC
    kubectl get pvc
    kubectl describe pvc <имя_pvc>
    ```
*   **Решение:**
    *   Увеличьте количество узлов или их ресурсы.
    *   Проверьте и исправьте `nodeSelector`/`nodeAffinity`.
    *   Убедитесь, что StorageClass существует и работает, и что есть доступные PV.

---

### **4. `ErrImagePull` / `ErrImageNeverPull`**

*   **Что это:** Похожи на `ImagePullBackOff`, но указывают на более фундаментальную проблему с извлечением образа.
*   **Причины:**
    *   `ErrImagePull`: Образ не найден, проблемы с сетью, недостаточно прав.
    *   `ErrImageNeverPull`: Указано `imagePullPolicy: Never`, но образ не существует локально на узле.
*   **Диагностика:**
    *   Идентична диагностике `ImagePullBackOff` (см. выше).
*   **Решение:**
    *   Для `ErrImagePull`: Те же действия, что и для `ImagePullBackOff`.
    *   Для `ErrImageNeverPull`: Измените `imagePullPolicy` на `IfNotPresent` или `Always`, либо убедитесь, что образ загружен на узел (`docker pull` или `crictl pull`).

---

### **5. `RunContainerError`**

*   **Что это:** Контейнер не может быть запущен из-за ошибки на уровне runtime.
*   **Причины:**
    *   Недостаток места на диске узла.
    *   Ошибка при монтировании тома (например, неправильные права, отсутствует директория).
    *   Проблемы с CRI (Container Runtime Interface).
*   **Диагностика:**
    ```bash
    # 1. Получить детальную информацию
    kubectl describe pod <имя_пода>
    # Ищите события вроде:
    # "Failed to create pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox..."
    # "MountVolume.SetUp failed for volume... : hostPath type check failed: /path/to/dir is not a directory"

    # 2. Проверить состояние узла
    kubectl describe node <имя_узла>
    # Ищите `Conditions` (особенно `DiskPressure`) и `Allocated resources`.
    ```
*   **Решение:**
    *   Очистите место на узле (удалите старые образы, логи).
    *   Проверьте конфигурацию тома (hostPath, PVC) и права доступа.
    *   Перезапустите kubelet на узле (в крайнем случае).

---

### **6. `FailedMount`**

*   **Что это:** Контейнер запускается, но не может смонтировать один из томов.
*   **Причины:**
    *   PersistentVolumeClaim не привязан к PersistentVolume.
    *   Неправильный путь монтирования.
    *   Отсутствие прав на чтение/запись.
    *   Проблемы с `hostPath` (директория не существует на узле).
*   **Диагностика:**
    ```bash
    # 1. Получить детальную информацию
    kubectl describe pod <имя_пода>
    # В секции `Events` ищите явное сообщение об ошибке монтирования, например:
    # "MountVolume.SetUp failed for volume "pvc-xyz" : failed to sync secret cache: timed out waiting for the condition"
    # "Unable to attach or mount volumes: unmounted volumes=[config-volume], unattached volumes=[config-volume default-token-abc]: timed out waiting for the condition"

    # 2. Проверить состояние PVC и PV
    kubectl get pvc
    kubectl get pv
    kubectl describe pvc <имя_pvc>
    kubectl describe pv <имя_pv>
    ```
*   **Решение:**
    *   Убедитесь, что PVC имеет статус `Bound`.
    *   Проверьте, что StorageClass существует и работает.
    *   Проверьте правильность `spec.volumes` и `spec.containers.volumeMounts` в манифесте.
    *   Для `hostPath`: убедитесь, что директория существует на узле и имеет правильные права.

---

### **7. `OOMKilled` (Out of Memory Killed)**

*   **Что это:** Контейнер был убит системой из-за превышения установленного лимита памяти.
*   **Причины:**
    *   Значение `resources.limits.memory` слишком низкое для приложения.
    *   Утечка памяти в приложении.
*   **Диагностика:**
    ```bash
    # 1. Получить детальную информацию
    kubectl describe pod <имя_пода>
    # В секции `Containers` ищите `Last State`:
    #   Last State:     Terminated
    #     Reason:       OOMKilled
    #     Exit Code:    137

    # 2. Проверить потребление памяти
    kubectl top pod <имя_пода>

    # 3. Просмотреть логи
    kubectl logs <имя_пода> --previous
    # Ищите сообщения об ошибках памяти (OutOfMemoryError, panic).
    ```
*   **Решение:**
    *   Увеличьте значение `resources.limits.memory` в манифесте.
    *   Проанализируйте приложение на наличие утечек памяти.
    *   Убедитесь, что `resources.requests.memory` и `limits.memory` разумно настроены.

---

### **8. `ContainerCreating` (зависший)**

*   **Что это:** Под застревает в состоянии `ContainerCreating` на долгое время.
*   **Причины:**
    *   Долгое извлечение большого образа.
    *   Проблемы с сетевым подключением к реестру.
    *   Ошибка при монтировании тома (часто не отображается сразу).
    *   Проблемы с CNI (например, нехватка IP-адресов).
*   **Диагностика:**
    ```bash
    # 1. Получить детальную информацию
    kubectl describe pod <имя_пода>
    # Это ключевой шаг! Секция `Events` покажет, на каком этапе зависло создание.

    # 2. Проверить состояние узла
    kubectl describe node <имя_узла>
    # Ищите проблемы с сетью, памятью, диском.
    ```
*   **Решение:**
    *   Подождите, если образ очень большой.
    *   Проверьте сетевую связность узла с реестром.
    *   Проверьте логи kubelet на узле (`journalctl -u kubelet`).

---
