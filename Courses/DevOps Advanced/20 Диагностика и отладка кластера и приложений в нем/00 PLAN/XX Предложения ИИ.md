
## ✅ **Что я могу сделать дальше?**

- **Создать лабораторную работу** на основе:
	- 🧪 **CKAD Study Guide** или **Kubernetes Cookbook**
	- 🧪 **Kubernetes Recipes** или  **Kubernetes на практике**
	- 🧪 **Kubernetes Anti-Patterns** или **Kannaiah Govardhana — Anti-Patterns** (например, "Исправьте 5 ошибок в YAML").
	- 🧪 **Разработать лабораторную работу** по отладке сети на основе книги Стронга.

- **Для лабораторных:**  
	- 📄 Создай кейсы на основе **anti-patterns** из книги Kannaiah Govardhana.

- 🎯 **Составить презентацию**:
	- "Отладка в Kubernetes: от анти-паттернов до сети" на основе этих книг
	- "Отладка в Kubernetes: от анти-паттернов до GitOps"

- 📚 **Собрать библиографию для студентов** (PDF/Markdown) с краткими аннотациями.
- 📊 **Создать матрицу покрытия тем курса по источникам** — чтобы ты видел, какая книга что покрывает.

- Извлечь из этого фрагмента **практическое упражнение по сетевой диагностике** (например, "Как использовать tcpdump в Kubernetes").
- Подготовить **лабораторную работу по проверке сетевой доступности** на основе примера с Azure и Linkerd.

---

### 🔹 **Практические сценарии для занятия (рекомендую включить)**
Подготовь лабораторные кейсы:
1. **Pod в состоянии `CrashLoopBackOff`** — как найти причину (логи, exit code, readiness probe).
2. **Сервис не доступен** — проверка endpoints, сетевых политик, DNS.
3. **Нет связи между Pod'ами** — диагностика CNI, NetworkPolicy, firewall.
4. **Node NotReady** — проверка kubelet, системных ресурсов, диска.
5. **ConfigMap/Secret не подключается** — проверка имени, монтирования, синтаксиса.
6. **Проблема с PersistentVolume** — доступ, права, reclaim policy.

---

### 🔹 **Чек-листы и шаблоны (можно раздать студентам)**
- [Kubernetes Debugging Checklist (GitHub Gist)](https://gist.github.com/brendan-ward/44acb16382920d57a91e69183a848b15)
- [Kubernetes Troubleshooting Flowchart](https://github.com/FairwindsOps/polaris) — можно использовать Polaris для аудита

---

## 🔍 **Дополнительные онлайн-ресурсы (из предыдущей подборки)**

| Источник                                                                                                                    | Релевантность | Уникальные преимущества                                               |
| --------------------------------------------------------------------------------------------------------------------------- | ------------- | --------------------------------------------------------------------- |
| **[Kubernetes Official Docs — Debugging](https://kubernetes.io/docs/tasks/debug/)**                                         | ⭐⭐⭐⭐⭐    | Официальный, структурированный, обязательный к изучению               |
| **[Learnk8s.io — Debugging Guides](https://learnk8s.io/)**                                                                  | ⭐⭐⭐⭐⭐    | Пошаговые, наглядные, идеальны для демонстрации                       |
| **[Sysdig Blog — Kubernetes Troubleshooting](https://sysdig.com/blog/)**                                                    | ⭐⭐⭐⭐☆     | Глубокие технические статьи по безопасности, сети, производительности |
| **[Ahmet Alp Balkan — kubectl Tips](https://ahmet.im/blog/kubectl-tips/)**                                                  | ⭐⭐⭐⭐☆     | Лайфхаки по `kubectl` — отличная шпаргалка                            |
| **[Red Hat — Debugging Kubernetes Apps](https://www.redhat.com/en/topics/containers/how-to-debug-kubernetes-applications)** | ⭐⭐⭐☆☆      | Простой обзор для новичков                                            |

---

### 🔹 **Инструменты для диагностики (обязательно показать в действии)**
1. **`kubectl` команды:**
   - `kubectl describe pod <pod>` — события
   - `kubectl logs <pod> [-c container] [--previous]`
   - `kubectl exec -it <pod> -- sh`
   - `kubectl get events --sort-by=.metadata.creationTimestamp`
   - `kubectl top pod/node`
1. **`kubectl debug` (ephemeral containers)**  
   - Добавление временных контейнеров для отладки без пересоздания Pod'а.  
   → [Документация](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/)
2. **`stern`** — агрегированные логи нескольких Pod'ов  
   → https://github.com/stern/stern
3. **`k9s`** — интерактивный CLI для просмотра ресурсов и быстрой диагностики  
   → https://k9scli.io/
4. **`kubectl trace`** — запуск bpftrace в Pod'ах для low-level диагностики  
   → https://github.com/iovisor/kubectl-trace
5. **`Lens` или `Octant`** — GUI-инструменты для визуализации кластера и диагностики.
6. **`kube-ps1`, `kubectx`, `kubens`** — удобство при работе.

---

### 🔹 **Дополнительно: продвинутые темы (для продвинутого уровня)**
- Использование **eBPF** и **Cilium** для сетевой диагностики.
- **OpenTelemetry**, **Distributed Tracing** (Jaeger, Tempo) — когда проблема в логике приложения.
- **Kubernetes Events Exporter** + **Alertmanager** — мониторинг событий.
- **Velero** — диагностика проблем с бэкапами и восстановлением.

---
