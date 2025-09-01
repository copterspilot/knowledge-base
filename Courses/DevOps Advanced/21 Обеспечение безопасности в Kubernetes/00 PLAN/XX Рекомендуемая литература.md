**Книги по теме "Обеспечение безопасности в Kubernetes"**
Таблица отсортирована по **релевантности** (от самых полезных к менее), с оценкой по ключевым параметрам.

---

## 🔐 **Сравнительная таблица: Книги по теме "Обеспечение безопасности в Kubernetes"**

| Ранг | Источник                                                                     | Год  | Релевантность теме | Глубина технических деталей | Практическая применимость | Подход                        | Уникальные преимущества                                                                                                                                                                            |
| ---- | ---------------------------------------------------------------------------- | ---- | ------------------ | --------------------------- | ------------------------- | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | **Lapaz Raul — Learning Kubernetes Security, 2nd Ed.**                       | 2025 | ⭐⭐⭐⭐⭐         | ⭐⭐⭐⭐⭐                  | ⭐⭐⭐⭐☆                 | Практический, hands-on        | **Самая релевантная книга.** Охватывает уязвимости, побег из контейнеров, security boundaries, Pod Security, аудит, compliance. Есть практические сценарии. Упоминает **Falco, Tetragon, Cilium**. |
| 2    | **Aversa Luigi — Security for Containers and Kubernetes**                    | 2023 | ⭐⭐⭐⭐☆          | ⭐⭐⭐⭐☆                   | ⭐⭐⭐⭐☆                 | Стратегический, глубокий      | Фокус на **SBOM, CycloneDX, Helm Chart Security, supply chain**. Отлично подходит для **security-инженеров и DevSecOps**.                                                                          |
| 3    | **Gkatziouras et al. — Kubernetes Secrets Handbook**                         | 2024 | ⭐⭐⭐⭐☆          | ⭐⭐⭐⭐☆                   | ⭐⭐⭐☆☆                  | Специализированный (Secrets)  | **Лучший источник по управлению Secrets**. Охватывает **аудит, соответствие требованиям, disaster recovery, интеграцию с Vault**.                                                                  |
| 4    | **Creane & Gupta — Kubernetes Security Observability Applications**          | 2022 | ⭐⭐⭐⭐☆          | ⭐⭐⭐⭐☆                   | ⭐⭐⭐☆☆                  | Стратегический, холистический | От авторов Tigera (создатели Calico). Фокус на **интеграции безопасности и наблюдаемости**, **многоплатформенность**, **распределение ответственности**. Подходит для **архитекторов**.            |
| 5    | **Stencel & Berton — Kubernetes Recipes**                                    | 2025 | ⭐⭐⭐☆☆           | ⭐⭐⭐☆☆                    | ⭐⭐⭐⭐☆                 | Рецепты, готовые решения      | Включает рецепты по **безопасности: service mesh (Istio), access control, managing secrets**. Хорошо подходит для **практических лабораторных работ**.                                             |
| 6    | **Boorshtein & Surovich — Kubernetes: An Enterprise Guide**                  | 2024 | ⭐⭐⭐☆☆           | ⭐⭐⭐☆☆                    | ⭐⭐⭐☆☆                  | Enterprise-подход             | Уделяет внимание **security best practices**, **Pod Security Standards**, **RBAC**, **admission controllers**. Подходит для **production-эксплуатации**.                                           |
| 7    | **Denniss William — Kubernetes for Developers**                              | 2024 | ⭐⭐⭐☆☆           | ⭐⭐☆☆☆                     | ⭐⭐☆☆☆                   | Введение, практика            | Кратко охватывает **обновления, DaemonSets, non-root, admission controllers, RBAC**. Подходит как **введение для разработчиков**.                                                                  |
| 8    | **Dubey S., Kulkarni M. — Hands-On Kubernetes, Service Mesh and Zero-Trust** | 2023 | ⭐⭐☆☆☆            | ⭐⭐⭐☆☆                    | ⭐⭐☆☆☆                   | Zero-Trust, Service Mesh      | Фокус на **zero-trust architecture**, **Istio**, **mTLS**. Полезно, если безопасность рассматривается через призму **service mesh**.                                                               |
| 9    | **Sanchez & Valenzuela — KCNA Study Guide**                                  | 2024 | ⭐⭐☆☆☆            | ⭐⭐☆☆☆                     | ⭐⭐☆☆☆                   | Подготовка к сертификации     | Упоминает **container image security**, но поверхностно. Рекомендует другие книги (например, *Learn Kubernetes Security*). Подходит как **введение**.                                              |
| 10   | **Poulton Nigel — The Kubernetes Book**                                      | 2024 | ⭐⭐☆☆☆            | ⭐⭐☆☆☆                     | ⭐⭐☆☆☆                   | Обзорный                      | Упоминает безопасность и наблюдаемость, но говорит, что "требует отдельной книги". Подходит как **обзор**.                                                                                         |
| 11   | **Kannaiah Govardhana — Kubernetes Anti-Patterns**                           | 2024 | ⭐☆☆☆☆             | ⭐⭐☆☆☆                     | ⭐⭐☆☆☆                   | Анализ ошибок                 | Безопасность затрагивается косвенно: **неправильные политики, открытые порты, отсутствие аудита**. Полезно для **анализа уязвимых конфигураций**.                                                  |
| 12   | **Ibryam Bilgin — Kubernetes Patterns**                                      | 2023 | ⭐☆☆☆☆             | ⭐⭐☆☆☆                     | ⭐☆☆☆☆                    | Паттерны проектирования       | Паттерны **sidecar, adapter, ambassador** могут использоваться для **безопасной архитектуры**, но не фокусируются на security напрямую.                                                            |
| 13   | **Burns B. et al. — Kubernetes Best Practices**                              | 2024 | ⭐☆☆☆☆             | ⭐⭐☆☆☆                     | ⭐☆☆☆☆                    | Рекомендации                  | Упоминает безопасность, но говорит, что это "требует отдельной книги". Кратко — о **admission controllers, RBAC**.                                                                                 |
| 14   | **Sayfan Gigi — Mastering Kubernetes**                                       | 2023 | ⭐☆☆☆☆             | ⭐⭐☆☆☆                     | ⭐☆☆☆☆                    | Экспертный обзор              | Упоминает Istio, безопасность, но без глубокой детализации.                                                                                                                                        |

---

## 🔍 **Анализ по ключевым аспектам безопасности**

### 1. **Управление секретами (Secrets)**
- **Лучшие книги:**
  - `Gkatziouras et al. — Kubernetes Secrets Handbook` — **основной источник**.
  - `Lapaz Raul` — глубокое понимание интеграции с Vault, аудита.
  - `Aversa Luigi` — шифрование, SBOM, Helm-чарты.

### 2. **Наблюдаемость и безопасность (Security Observability)**
- **Лучшие книги:**
  - `Creane & Gupta` — **холистический подход**, связь безопасности и observability.
  - `Lapaz Raul` — использование **Falco, Tetragon** для обнаружения аномалий.
  - `Dubey & Kulkarni` — zero-trust + service mesh.

### 3. **Архитектура безопасности (Zero-Trust, Network Policies)**
- **Лучшие книги:**
  - `Dubey & Kulkarni` — **zero-trust**, Istio, mTLS.
  - `Lapaz Raul` — **network security**, Cilium.
  - `Aversa Luigi` — **CNI, network policies**.

### 4. **Политики безопасности (Pod Security, Admission Control)**
- **Лучшие книги:**
  - `Lapaz Raul` — **Pod Security Standards**, **admission controllers**.
  - `Boorshtein & Surovich` — **RBAC, Pod Security**.
  - `Denniss William` — **non-root, DaemonSets, RBAC**.

### 5. **Цепочка поставок (Supply Chain Security)**
- **Лучшие книги:**
  - `Aversa Luigi` — **SBOM, CycloneDX, Helm Chart Security**.
  - `Stencel & Berton` — **image scanning, secure deployments**.
  - `Creane & Gupta` — **security across teams**, **compliance**.

---

## 🎯 **Рекомендации по использованию в курсе**

| Цель                                 | Лучшие источники                       |
| ------------------------------------ | -------------------------------------- |
| **Основной учебник по безопасности** | Lapaz Raul                             |
| **Глубокое погружение в Secrets**    | Gkatziouras et al.                     |
| **Supply Chain Security**            | Aversa Luigi                           |
| **Security + Observability**         | Creane & Gupta                         |
| **Практические лаборатории**         | Stencel & Berton, Lapaz Raul           |
| **Введение для разработчиков**       | Denniss William, Boorshtein & Surovich |
| **Zero-Trust и Service Mesh**        | Dubey & Kulkarni                       |

---

## ✅ **Вывод**

Для курса **"Обеспечение безопасности в Kubernetes"** **наиболее релевантными** являются:

1. **Lapaz Raul — Learning Kubernetes Security** — **основной учебник**.
2. **Aversa Luigi — Security for Containers and Kubernetes** — **лучший выбор для DevSecOps**.
3. **Gkatziouras et al. — Kubernetes Secrets Handbook** — **специализированный гид по Secrets**.
4. **Creane & Gupta — Kubernetes Security Observability Applications** — **для архитекторов и SRE**.

