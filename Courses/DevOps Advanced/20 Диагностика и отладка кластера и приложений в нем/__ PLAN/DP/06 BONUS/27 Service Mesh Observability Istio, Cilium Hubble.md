### **27 Service Mesh Observability: Istio, Cilium Hubble**

**1. Визуальный ряд:**
*   Сравнительная архитектура service mesh:
    *   **Istio:** Sidecar-прокси (Envoy) + Control Plane
    *   **Cilium Hubble:** eBPF-based + Native Kubernetes Integration
*   Таблица observability-возможностей:
    
    | Возможность      | Istio                    | Cilium Hubble                 |
    | ---------------- | ------------------------ | ----------------------------- |
    | **Метрики**      | Prometheus, rich metrics | Basic metrics, eBPF-efficient |
    | **Трассировка**  | Full distributed tracing | Логическая трассировка        |
    | **Логи**         | Access logs, Envoy logs  | Flow logs, DNS queries        |
    | **Визуализация** | Kiali, Grafana           | Hubble UI, CLI                |
    | **Требования**   | Sidecar injection        | eBPF-enabled kernel           |

*   Скриншот Kiali с картой сервисов
*   Пример вывода `hubble observe` для потоков трафика

**2. Демо-сценарий:**
*   Настройка и просмотр observability в Istio:
    ```bash
    # Включение observability в Istio
    kubectl apply -f samples/addons/
    
    # Просмотр метрик через Prometheus
    kubectl port-forward -n istio-system svc/prometheus 9090
    
    # Визуализация через Kiali
    kubectl port-forward -n istio-system svc/kiali 20001
    ```
*   Работа с Cilium Hubble:
    ```bash
    # Наблюдение за потоком трафика
    hubble observe --from-pod details/v1 --to-pod productpage/v1 --protocol http
    
    # Просмотр дашборда Hubble UI
    kubectl port-forward -n kube-system svc/hubble-ui 12000
    ```

**3. Ключевые тезисы:**
*   "Service Mesh — это observability как first-class citizen"
*   "Istio: богатейший набор возможностей через sidecar-прокси"
*   "Cilium Hubble: эффективность и глубина за счет eBPF"
*   "Автоматическое обнаружение сервисов и зависимостей"
*   "Без модификации приложения — вся магия на уровне инфраструктуры"

**4. Описание для выступающего:**
Показать как service mesh меняет подход к observability. Сравнить традиционный (Istio) и modern (Cilium) подходы. Важно сделать акцент на практической пользе для разработчиков и операторов.

**5. Связь с прошлыми материалами:**
*   Использование eBPF (слайд 25) в Cilium Hubble
*   Интеграция с инструментами мониторинга из предыдущих слайдов
*   Развитие темы сетевой диагностики

**6. Типовые вопросы и ответы (Q&A):**
*   **В:** Какой overhead у Istio sidecar?
    *   **О:** ~100MB RAM и 10ms latency на прокси, но богатая функциональность
*   **В:** Нужен ли service mesh только для observability?
    *   **О:** Нет, но это одно из ключевых преимуществ наряду с безопасностью и управлением трафиком
*   **В:** Можно ли использовать без eBPF?
    *   **О:** Cilium требует eBPF, Istio работает без него

**7. Типовые примеры/сценарии:**
*   **Сценарий:** Анализ проблем производительности
    *   Istio: метрики задержек между сервисами + трассировка
    *   Hubble: наблюдение за сетевыми потоками в реальном времени
    *   Быстрое выявление аномалий и узких мест

**8. Предложения по дополнительному контенту слайда:**
*   Добавить QR-код с Istio vs Cilium comparison
*   Пример настройки custom dashboards
*   Информация о security implications
*   Ссылки на best practices
*   Примеры использования для canary analysis