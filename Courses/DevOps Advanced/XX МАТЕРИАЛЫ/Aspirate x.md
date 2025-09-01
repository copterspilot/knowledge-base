Спасибо за уточнение! Страница `https://deepwiki.com/prom3theu5/aspirational-manifests/5.1-kubernetes-manifests` является зеркалом официальной документации Aspirate по генерации манифестов Kubernetes.

На основе **всех предоставленных материалов**, включая эту ссылку и официальные ресурсы, вот **объединенная и актуальная инструкция по работе с .NET Aspire и Aspirate**.

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

#### **Шаг 2: Инициализация Aspirate (`init`)**

Настройте Aspirate для вашего проекта.

```bash
# Выполните в директории App Host проекта
cd ./src/MyApp.AppHost

# Запустите инициализацию
aspirate init
```

Команда задаст несколько вопросов:
*   **Container Registry:** Адрес вашего реестра (например, `docker.io`, `ghcr.io`, `my-registry.com`).
*   **Container Repository Prefix:** Префикс для имен репозиториев (например, `mycompany`).
*   **Default Container Image Tag:** Тег по умолчанию (например, `latest`).
*   **Output Path:** Путь для генерируемых манифестов (по умолчанию `./aspirate-output`).
*   **Output Format:** Формат вывода (`kustomize` (по умолчанию) или `compose`).

Ответы будут сохранены в файле `aspirate.config.json`.

---

#### **Шаг 3: Сборка образов (`build`)**

Соберите Docker-образы для всех проектов.

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

Создайте артефакты развертывания.

```bash
# Сгенерировать манифесты Kubernetes (Kustomize) - по умолчанию
aspirate generate

# Сгенерировать манифесты для Docker Compose
aspirate generate --output-format compose

# Сгенерировать манифесты и сразу применить их
aspirate generate --apply
```

---

#### **Шаг 5: Применение манифестов в кластере (`apply`)**

Разверните приложение в Kubernetes.

```bash
# Применить сгенерированные манифесты
aspirate apply

# Применить манифесты из другого пути
aspirate apply --output-path ./custom-output

# Применить манифесты и сразу запустить
aspirate apply --run
```

---

#### **Шаг 6: Запуск напрямую в кластере (`run`)**

Объединяет `build`, `generate` и `apply`.

```bash
# Полный цикл: сборка, генерация, применение
aspirate run

# Использовать существующий manifest.json
aspirate run --aspire-manifest ./manifest.json

# Запустить в неинтерактивном режиме (для CI/CD)
aspirate run --non-interactive
```

---

#### **Шаг 7: Удаление приложения из кластера (`destroy`)**

```bash
# Удалить все ресурсы
aspirate destroy

# Удалить ресурсы и пространство имен
aspirate destroy --delete-namespace
```

---

#### **Шаг 8: Остановка локального запуска (`stop`)**

```bash
# Остановить локально запущенное приложение
aspirate stop
```

---

### **Часто задаваемые вопросы (FAQ)**

*   **Q: Где хранится конфигурация Aspirate?**
    *   **A:** В файле `aspirate.config.json`, созданном командой `init`.
*   **Q: Поддерживает ли Aspirate Ingress-ресурсы?**
    *   **A:** Пока нет, но это активно разрабатывается.
*   **Q: Как интегрировать с GitOps?**
    *   **A:** Сгенерируйте манифесты с помощью `aspirate generate`, закоммитьте их в Git и используйте Argo CD или Flux.
*   **Q: Можно ли использовать Aspirate без установки?**
    *   **A:** Да, через Dev Container в VS Code.

---

✅ **Инструкция завершена.**

Этот гайд объединяет информацию из всех доступных источников, включая вашу ссылку, и предоставляет полное руководство по Aspirate.