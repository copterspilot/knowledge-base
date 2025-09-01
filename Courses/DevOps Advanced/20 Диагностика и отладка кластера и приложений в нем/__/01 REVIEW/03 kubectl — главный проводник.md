**Название слайда**
3: `kubectl` — главный проводник

**1. Визуальный ряд**
*   Крупный логотип `kubectl`.
*   Скриншот терминала: `kubectl --help`, с выделением секции `Basic Commands (Beginner)`.
*   Логотип `krew`.
*   Скриншот терминала: `kubectl krew` (для демонстрации наличия плагина).
*   Иконки: шестеренка (администратор), магнит (поиск).

**2. Демо-сценарий (если уместен)**
*   Показать в реальном времени команду `kubectl version` и `kubectl api-resources`.

**3. Ключевые тезисы**
*   `kubectl` — основной инструмент для взаимодействия с кластером Kubernetes.
*   Поддержка плагинов через `krew` расширяет его функциональность.
*   `krew` — официальный менеджер плагинов для `kubectl`, аналог `brew` или `apt`.

**4. Описание (для выступающего)**
*   **Цель слайда:** Представить `kubectl` как центральный инструмент для всех операций с кластером и ввести понятие плагинов.
*   **Ключевое сообщение:** Мастерство в `kubectl` — основа работы с Kubernetes.
*   **Основные тезисы:**
    *   `kubectl` — это CLI-клиент, который взаимодействует с `kube-apiserver` вашего кластера.
    *   Он позволяет управлять ресурсами: создавать, просматривать, обновлять, удалять (`create`, `get`, `apply`, `delete`).
    *   `kubectl` поддерживает установку плагинов, которые расширяют его возможности.
    *   `krew` — это официальный способ установки и управления этими плагинами. Он упрощает поиск (`krew search`), установку (`krew install`) и обновление плагинов.
    *   Плагины интегрируются в `kubectl`, и их можно вызывать как `kubectl <plugin-name>`.
*   **Связь с источниками:** Тезисы основаны на PDF 2019 года (слайд 4.1) и субтитрах (00:01:11 - 00:01:45).

**5. Связь с прошлыми материалами**
*   **Из PDF 2019, слайд 4.1:** "Главный инструмент администратора- это kubectl. Помимо базовых команд, поддерживает установку плагинов... самый простой способ установки(и, вдобавок, официальный)- krew Это как brew, только для kubectl".
*   **Из субтитров 2019:** "Итак, первая часть. Главный инструмент администратора, как известно, это kubectl... Обычно плагины можно подбрасывать просто как бинарнички какие-то, но есть хороший красивый способ установки, называется CRU, он, в общем, почти официальный... позволяет делать сёрч, апдейт и всякие такие штуки."

**6. Типовые вопросы и ответы (Q&A)**
*   **В:** Нужно ли устанавливать `krew`?
    *   **О:** Да, если вы хотите использовать плагины. Это стандартная практика в 2025 году. Установка проста: `curl -fsSL https://raw.githubusercontent.com/kubernetes-sigs/krew/master/install.sh | bash`.
*   **В:** Где взять плагины?
    *   **О:** Через `kubectl krew search`. Официальный реестр `krew` содержит сотни проверенных плагинов.
*   **В:** Безопасны ли плагины?
    *   **О:** Плагины запускаются с теми же правами, что и `kubectl`. Устанавливайте только проверенные плагины из официального реестра `krew`.

**7. Примеры использования / Типовые сценарии**
*   Начало работы с кластером: проверка версии `kubectl` и доступных ресурсов.
*   Установка плагина `stern` для улучшенного просмотра логов: `kubectl krew install stern`.

**8. Предложения по дополнительному контенту слайда**
*   Добавить ссылку на официальный репозиторий `krew` на GitHub.
*   Подчеркнуть, что `krew` является официальным проектом Kubernetes SIG CLI.

---

Вот пример вывода команды `kubectl --help` с выделением секции `Basic Commands (Beginner)`.

```sh
kubectl --help
Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/
Basic Commands (Beginner):
  create         Create a resource from a file or from stdin
  expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run            Run a particular image on the cluster
  set            Set specific features on objects
. . . . .
Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

---

#### Пример вывода команды `kubectl krew`, которая показывает доступные операции и справку по менеджеру плагинов.

```sh
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```


**Add the Krew binary to your system’s PATH:**
```sh
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

```sh
krew is a tool that makes it easy to find and install kubectl plugins.

Find more information at: https://krew.sigs.k8s.io/
Please star krew on GitHub: https://github.com/kubernetes-sigs/krew

Usage:
  kubectl krew [command]

Available Commands:
  help        Help about any command
  info        Show information about a plugin
  install     Install kubectl plugins
  list        List installed plugins
  search      Search for kubectl plugins
  uninstall   Uninstall plugins
  update      Update the local copy of the plugin index
  upgrade     Upgrade installed plugins to newer versions
  version     Show krew version and diagnostics

Flags:
  -h, --help   help for kubectl-krew

Use "kubectl krew [command] --help" for more information about a command.
```

---

#### Примеры использования команд `krew`:

```sh
kubectl krew
You can invoke krew through kubectl: "kubectl krew [command]..."

Available Commands:
  help        Help about any command
  index       Manage custom plugin indexes
  info        Show information about an available plugin
  install     Install kubectl plugins
  list        List installed kubectl plugins
  search      Discover kubectl plugins
  uninstall   Uninstall plugins
  update      Update the local copy of the plugin index
  upgrade     Upgrade installed plugins to newer versions
  version     Show krew version and diagnostics

```

1.  **Поиск плагина:**
    ```sh
    kubectl krew search
    ```

2.  **Установка плагина:**
    ```sh
    kubectl krew install debug
    ```

3.  **Просмотр установленных плагинов:**
    ```sh
    kubectl krew list
    ```
    *Пример вывода:*
    ```sh
    PLUGIN       VERSION
    debug        v0.1.1
    stern        v0.16.0
    ksniff       v1.8.1
    ```

4.  **Обновление индекса плагинов:**
    ```sh
    kubectl krew update
    ```

5.  **Обновление всех установленных плагинов:**
    ```sh
    kubectl krew upgrade
    ```

---
