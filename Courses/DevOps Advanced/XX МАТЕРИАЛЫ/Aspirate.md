Отлично! На основе анализа официальной документации Aspirate и предоставленных материалов, вот **единая, полная инструкция по работе с .NET Aspire и Aspirate**.

---

### **Полная инструкция по работе с .NET Aspire и Aspirate**

Этот гайд охватывает весь цикл: от установки инструментов до развертывания приложения в Kubernetes и его удаления.

---

#### **Шаг 1: Установка Aspirate**

Aspirate — это глобальный инструмент .NET.

```bash
# Установить Aspirate как глобальный инструмент (предварительная версия)
dotnet tool install -g aspirate --prerelease

# Обновить Aspirate
dotnet tool update -g aspirate --prerelease

# Проверить версию
aspirate --version
```

> **Альтернатива: Dev Container (VS Code)**
> Вы можете установить Aspirate в среде разработки VS Code с помощью `devcontainer.json`:
> ```json
> {
>   "features": {
>     "ghcr.io/prom3theu5/aspirational-manifests/aspirate:latest": {}
>   }
> }
> ```

---

#### **Шаг 2: Инициализация Aspirate**

Настройте Aspirate для вашего проекта.

```bash
# Выполните в директории App Host проекта
cd ./src/MyApp.AppHost

# Запустите инициализацию
aspirate init
```

Команда `init` задаст несколько вопросов:
*   **Container Registry:** Адрес вашего реестра (например, `docker.io`, `ghcr.io`, `my-registry.com`).
*   **Container Repository Prefix:** Префикс для имен репозиториев (например, `mycompany`).
*   **Default Container Image Tag:** Тег по умолчанию (например, `latest`).
*   **Output Path:** Путь для генерируемых манифестов (по умолчанию `./aspirate-output`).
*   **Output Format:** Формат вывода (`kustomize` (по умолчанию) или `compose`).

Ответы будут сохранены в файле `aspirate.config.json`.

---

#### **Шаг 3: Сборка образов (`build`)**

Соберите Docker-образы для всех проектов, описанных в вашем App Host.

```bash
# Собрать и запушить все образы
aspirate build

# Собрать и запушить только определенные проекты
aspirate build --projects apiservice,webfrontend

# Пропустить сборку (если образы уже есть)
aspirate build --skip-build

# Указать путь к существующему manifest.json
aspirate build --aspire-manifest ./path/to/manifest.json
```

---

#### **Шаг 4: Генерация манифестов (`generate`)**

Создайте артефакты развертывания на основе модели приложения.

```bash
# Сгенерировать манифесты Kubernetes (Kustomize) - по умолчанию
aspirate generate

# Сгенерировать манифесты для Docker Compose
aspirate generate --output-format compose

# Сгенерировать манифесты и сразу применить их (см. шаг 5)
aspirate generate --apply
```

**Что генерируется:**
*   `kustomization.yaml`
*   `deployment.yaml`, `service.yaml` для каждого проекта.
*   Манифесты для интеграций (Redis, PostgreSQL и т.д.).
*   (Пока не поддерживается) Ingress-ресурсы.

---

#### **Шаг 5: Применение манифестов в кластере (`apply`)**

Разверните ваше приложение в Kubernetes.

```bash
# Применить сгенерированные манифесты к кластеру
aspirate apply

# Применить манифесты из другого пути
aspirate apply --output-path ./custom-output

# Применить манифесты и сразу запустить (см. шаг 6)
aspirate apply --run
```

---

#### **Шаг 6: Запуск напрямую в кластере (`run`)**

Команда `run` объединяет `build`, `generate` и `apply` в один шаг.

```bash
# Собрать, сгенерировать и применить манифесты
aspirate run

# Использовать существующий manifest.json
aspirate run --aspire-manifest ./manifest.json

# Запустить в неинтерактивном режиме (для CI/CD)
aspirate run --non-interactive
```

---

#### **Шаг 7: Удаление приложения из кластера (`destroy`)**

Удалите все ресурсы, развернутые с помощью Aspirate.

```bash
# Удалить все ресурсы из кластера
aspirate destroy

# Удалить ресурсы и пространство имен
aspirate destroy --delete-namespace
```

---

#### **Шаг 8: Остановка локального запуска (`stop`)**

Если вы использовали `aspirate run` и хотите остановить локальный процесс.

```bash
# Остановить локально запущенное приложение
aspirate stop
```

---

#### **Шаг 9: Работа в CI/CD (Неинтерактивный режим)**

Для автоматизации в пайплайнах используйте флаг `--non-interactive`.

```yaml
# Пример для GitLab CI
deploy:
  script:
    - aspirate run --non-interactive
```

---

#### **Шаг 10: Удаление Aspirate**

Если инструмент больше не нужен.

```bash
# Удалить глобальный инструмент Aspirate
dotnet tool uninstall -g aspirate
```

---

### **Часто задаваемые вопросы (FAQ)**

*   **Q: Поддерживает ли Aspirate Ingress-ресурсы?**
    *   **A:** Пока нет, но это активно разрабатывается ([GitHub Issue #85](https://github.com/prom3theu5/aspirational-manifests/issues/85)).
*   **Q: Можно ли использовать Aspirate без установки?**
    *   **A:** Да, можно использовать его как Dev Container в VS Code.
*   **Q: Как интегрировать с GitOps?**
    *   **A:** Сгенерируйте манифесты с помощью `aspirate generate`, закоммитьте их в Git-репозиторий, и используйте Argo CD или Flux для автоматической синхронизации.
*   **Q: Поддерживает ли Aspirate другие платформы, кроме Kubernetes?**
    *   **A:** Да, формат `compose` позволяет генерировать `docker-compose.yml` для локального запуска.

---

✅ **Инструкция завершена.**

Следуя этим шагам, вы сможете эффективно использовать .NET Aspire и Aspirate для полного цикла разработки, сборки и развертывания облачных приложений.