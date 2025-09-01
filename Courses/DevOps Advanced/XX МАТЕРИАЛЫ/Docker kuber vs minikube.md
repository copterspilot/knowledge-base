На основе глубокого анализа всех предоставленных материалов, вот **комплексная инструкция по локальной разработке с Docker, Kubernetes и Minikube**, включающая руководства, таблицу сравнения и всю актуальную информацию.

---

### **Инструкция: Локальная разработка с Docker, Docker Desktop (Kubernetes) и Minikube**

---

#### **Часть 1: Руководства по установке и запуску**

**1. Docker Desktop (с встроенным Kubernetes)**

Docker Desktop — это удобная платформа, которая включает в себя Docker Engine и одноузловой кластер Kubernetes "из коробки".

*   **Установка:**
    1.  Скачайте и установите Docker Desktop с официального сайта: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
    2.  Запустите Docker Desktop.

*   **Включение Kubernetes:**
    1.  Откройте настройки Docker Desktop (иконка шестеренки).
    2.  Перейдите на вкладку **Kubernetes**.
    3.  Поставьте галочку **Enable Kubernetes**.
    4.  Нажмите **Apply & Restart**.
    5.  Дождитесь завершения загрузки компонентов Kubernetes.

*   **Проверка работы:**
    ```bash
    # Проверить, что контекст переключился на kubernetes.docker.internal
    kubectl config current-context

    # Получить список узлов
    kubectl get nodes

    # Получить список подов в системном namespace
    kubectl get pods -n kube-system
    ```
    *Ожидаемый результат: один узел в статусе `Ready`.*

*   **Загрузка образов:**
    *   **Ключевое преимущество:** Docker Desktop **делит образы** с локальным Docker-демоном.
    *   Если вы собрали образ с помощью `docker build`, он **автоматически доступен** для кластера Kubernetes.
    ```bash
    # Собрать образ
    docker build -t my-app:latest .

    # Применить манифест, использующий my-app:latest
    kubectl apply -f deployment.yaml
    ```

---

**2. Minikube**

Minikube — это специализированный инструмент для запуска одноузлового (или многоузлового) кластера Kubernetes на вашей машине.

*   **Установка:**
    1.  Установите `kubectl` ([официальная документация](https://kubernetes.io/docs/tasks/tools/)).
    2.  Установите Minikube:
        *   **macOS:** `brew install minikube`
        *   **Linux:** Скачайте бинарный файл с [GitHub](https://github.com/kubernetes/minikube/releases/latest)
        *   **Windows:** `choco install minikube` (через Chocolatey)

*   **Запуск кластера:**
    ```bash
    # Запустить одноузловой кластер (используется драйвер по умолчанию)
    minikube start

    # Запустить многоузловой кластер (требуется поддержка драйвера)
    minikube start --nodes 3

    # Запустить с конкретным драйвером (например, docker)
    minikube start --driver=docker
    ```
    > **Примечание:** Minikube автоматически настраивает `kubectl` на использование контекста `minikube`.

*   **Проверка работы:**
    ```bash
    # Проверить контекст
    kubectl config current-context # Должно быть 'minikube'

    # Получить список узлов
    kubectl get nodes
    ```
    *Ожидаемый результат: один или несколько узлов в статусе `Ready`.*

*   **Загрузка образов:**
    *   **Ключевое отличие:** Minikube **не делит образы** с локальным Docker-демоном.
    *   Образы нужно **явно загружать** в среду Minikube.
    ```bash
    # Собрать образ (если используется драйвер docker)
    docker build -t my-app:latest .

    # Загрузить образ в кластер Minikube
    minikube image load my-app:latest

    # Применить манифест
    kubectl apply -f deployment.yaml
    ```
    *Альтернатива:* Собрать образ напрямую внутри среды Minikube, используя `minikube docker-env`.*

---

#### **Часть 2: Как переключаться между контекстами**

Если у вас установлены и Docker Desktop (Kubernetes), и Minikube, вы можете легко переключаться между ними.

```bash
# Получить список всех доступных контекстов
kubectl config get-contexts

# Вывод будет примерно таким:
# CURRENT   NAME                          CLUSTER              AUTHINFO             NAMESPACE
# *         docker-desktop                docker-desktop       docker-desktop       
#           minikube                      minikube             minikube             
#           my-prod-cluster               my-prod-cluster      my-prod-user
```

```bash
# Переключиться на Minikube
kubectl config use-context minikube

# Переключиться на Docker Desktop (Kubernetes)
kubectl config use-context docker-desktop

# Переключиться на production кластер
kubectl config use-context my-prod-cluster
```

*   **Совет:** Установите утилиту `kubectx` для быстрого переключения (`kubectx minikube`).

---

#### **Часть 3: Таблица сравнения: Docker Desktop (K8s) vs Minikube**

| Критерий                         | **Docker Desktop (встроенный Kubernetes)**                                                                         | **Minikube**                                                                                      |
|:-------------------------------- |:------------------------------------------------------------------------------------------------------------------ |:------------------------------------------------------------------------------------------------- |
| **Установка**                    | Устанавливается как единое приложение.                                                                             | Требует отдельной установки `kubectl` и `minikube`.                                               |
| **Архитектура**                  | Запускает компоненты Kubernetes внутри контейнеров на базе LinuxKit (macOS) или напрямую (Windows).                | Может использовать разные драйверы: `docker`, `hyperkit`, `hyperv`, `virtualbox`, `podman`.       |
| **Доступ к образам**             | **Автоматический.** Образы, собранные через `docker build`, доступны для кластера.                                 | **Требует загрузки.** Нужно использовать `minikube image load <имя_образа>`.                      |
| **Многоузловые кластеры**        | Нет. Только одноузловой кластер.                                                                                   | **Да.** Поддерживает многоузловые кластеры (`minikube start --nodes 3`).                          |
| **Поддержка подкачки (Swap)**    | Зависит от настроек Docker Desktop.                                                                                | По умолчанию **отключена** (не подходит для production-симуляции).                                |
| **Интеграция с Docker**          | Отличная. Единая платформа для разработки и тестирования.                                                          | Хорошая (с драйвером `docker`), но требует дополнительных шагов для обмена образами.              |
| **Обновление версии Kubernetes** | Зависит от версии Docker Desktop. Часто отстает от последней.                                                      | **Более актуальные версии.** Легко обновляется до последних версий Kubernetes.                    |
| **Цель использования**           | Быстрый старт, простая локальная разработка, интеграция с Docker.                                                  | Тестирование и обучение, особенно многоузловых сценариев, более гибкая настройка.                 |
| **Источник**                     | `Naik Sameer and others - Kubernetes Cookbook, 2nd Edition`, `Schenker G. N. - The Ultimate Docker Container Book` | `Lapaz Raul - Learning Kubernetes Security`, `Stencel Grzegorz, Berton Luca - Kubernetes Recipes` |

---

#### **Часть 4: Рекомендации и лучшие практики**

1.  **Для новичков и быстрого старта:** Используйте **Docker Desktop (Kubernetes)**. Его интеграция с Docker и автоматический обмен образами делают его самым простым и удобным вариантом.
2.  **Для обучения и тестирования продвинутых сценариев:** Используйте **Minikube**, особенно если вам нужно смоделировать многоузловой кластер или протестировать политики, зависящие от количества узлов.
3.  **Для работы с последними версиями Kubernetes:** **Minikube** чаще обновляется и позволяет быстрее протестировать новые функции.
4.  **Для разработки с несколькими кластерами:** Установите оба инструмента и используйте `kubectl config use-context` для переключения.
5.  **Альтернатива: `kind` (Kubernetes in Docker):** Для CI/CD пайплайнов и тестирования рекомендуется использовать `kind`, который запускает кластер Kubernetes внутри Docker-контейнеров. Он очень быстрый и легко интегрируется с CI/CD системами.
    ```bash
    kind create cluster --name my-cluster
    ```
6.  **Решение проблемы "у меня работает, а в кластере — нет":** Даже с локальным кластером могут быть различия (сетевые политики, ресурсы, переменные окружения). Используйте инструменты вроде `telepresence` для подключения локального процесса к кластеру.

---
