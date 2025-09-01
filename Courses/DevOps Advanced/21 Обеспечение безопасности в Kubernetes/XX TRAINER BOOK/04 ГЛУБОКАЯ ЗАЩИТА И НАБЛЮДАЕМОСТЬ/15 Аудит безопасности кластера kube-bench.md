```markdown
## [15] Аудит безопасности кластера: `kube-bench`

**Содержимое слайда (для PowerPoint):**
-   **Заголовок:** Аудит безопасности кластера: `kube-bench`
-   **Основной текст / список:**
    *   **Что такое `kube-bench`:** Инструмент от Aqua Security для проверки соответствия кластера рекомендациям CIS (Center for Internet Security).
    *   **Что проверяет:** Конфигурацию компонентов Kubernetes (kubelet, kube-apiserver, etcd и др.) на соответствие best practices.
    *   **Результат:** Отчет с проверками `PASS`, `FAIL`, `WARN`.
    *   **Пример:**
        _bash
        # Запуск аудита на worker node
        kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml
        
        # Просмотр результатов
        kubectl logs -l job-name=kube-bench
        _
    *   **Ключевое преимущество:** Автоматизация рутинного аудита конфигурации.
-   **Дополнительная информация (внизу):** "Проверьте свою конфигурацию. Не полагайтесь на настройки по умолчанию."
```

---

### **Статья 15: Аудит безопасности кластера: `kube-bench`**

#### **Введение**

Когда вы разворачиваете кластер Kubernetes, компоненты (такие как `kubelet`, `kube-apiserver`, `etcd`) настраиваются со значениями по умолчанию. Эти значения не всегда являются самыми безопасными. `kube-bench` — это инструмент, который автоматически проверяет, соответствует ли ваша конфигурация рекомендациям CIS (Center for Internet Security). Он работает как "чек-лист безопасности", выявляя потенциально опасные настройки. Как указано в `Lapaz Raul - Learning Kubernetes Security, 2nd Edition`, регулярный аудит с помощью `kube-bench` — это критически важная практика для любого администратора кластера.

#### **Что такое CIS Benchmarks?**

CIS (Center for Internet Security) — это некоммерческая организация, которая разрабатывает подробные, проверенные на практике руководства по безопасности для различных технологий, включая Kubernetes. **CIS Benchmarks** — это документы, содержащие сотни конкретных рекомендаций по настройке компонентов Kubernetes для минимизации рисков.

*   **Пример рекомендации:** "Убедитесь, что файл `kubelet.conf` имеет права доступа 644 или более строгие."

#### **Что делает `kube-bench`?**

`kube-bench` — это автоматизированный инструмент, написанный на Go, который выполняет проверки, описанные в CIS Benchmarks.

*   **Как это работает:** `kube-bench` запускается на узле кластера (worker node или control plane node) и проверяет:
    *   Наличие и содержимое конфигурационных файлов.
    *   Параметры командной строки компонентов.
    *   Права доступа к файлам.
    *   Настройки аудита и шифрования.
*   **Формат вывода:** Для каждой проверки инструмент выводит статус:
    *   `PASS`: Конфигурация соответствует рекомендации.
    *   `FAIL`: Конфигурация нарушает рекомендацию. Требует исправления.
    *   `WARN`: Проверка не может быть выполнена (например, отсутствует компонент).
    *   `INFO`: Информационное сообщение.

#### **Практический пример**

1.  **Запуск `kube-bench` на worker node:**
    ```bash
    # Применить Job, который запустит kube-bench на всех worker nodes
    kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml
    ```
    Эта команда создает Job, который разворачивает Pod с `kube-bench` на каждом worker node.

2.  **Просмотр результатов:**
    ```bash
    # Найти Pod с kube-bench
    kubectl get pods -l job-name=kube-bench
    
    # Посмотреть логи (результаты аудита)
    kubectl logs kube-bench-xxxxx
    ```
    Вывод будет содержать длинный список проверок. Вам нужно сосредоточиться на строках с `[FAIL]`.

3.  **Пример вывода:**
    ```
    [INFO] 2.2 Configuration Files
    [FAIL] 2.2.1 Ensure that the kubelet.conf file permissions are set to 644 or more restrictive (Scored)
    [WARN] 2.2.2 Ensure that the kubelet.conf file ownership is set to root:root (Scored)
    ...
    == Summary total ==
    4 checks PASS
    3 checks FAIL
    2 checks WARN
    0 checks INFO
    ```

#### **Заключение**

`kube-bench` — это must-have инструмент для любого администратора Kubernetes. Он превращает сложный, ручной процесс аудита в простую, автоматизированную проверку. Регулярно запускайте `kube-bench`, особенно после обновления кластера, чтобы убедиться, что ваша конфигурация остается безопасной. В следующей статье мы рассмотрим, как защитить ваши секреты.

#### **Ссылки**
*   `Lapaz Raul - Learning Kubernetes Security, 2nd Edition - 2025` — Глава о аудите безопасности.
*   `Burns B., Beda J., Hightower K., and Evenson L. - Kubernetes. Up and Running, 3rd Edition - 2022` — Глава о безопасности.
*   Официальная документация `kube-bench`: `https://github.com/aquasecurity/kube-bench`