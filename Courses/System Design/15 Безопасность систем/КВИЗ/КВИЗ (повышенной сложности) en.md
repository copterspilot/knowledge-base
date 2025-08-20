**1. В контексте Zero Trust Architecture, какая модель сегментации сети наиболее эффективно ограничивает движение трафика между микросервисами?**  
A) Flat network with VLANs  
B) Traditional perimeter-based segmentation  
C) Micro-segmentation using service mesh with mTLS **(✓)**  
D) Single subnet with internal firewalls  

✅ **Обоснование:**  
В **Zero Trust** традиционные периметры теряют смысл. **Micro-segmentation через service mesh** (например, Istio, Linkerd) с **mTLS** обеспечивает **контроль на уровне соединений между сервисами**, что соответствует принципу "never trust, always verify".

---

**2. Какой из следующих аспектов threat modeling наиболее критичен при проектировании распределённой системы с высокой степенью доверия к данным?**  
A) STRIDE threat enumeration  
B) DFD (Data Flow Diagram) creation  
C) Risk prioritization and impact analysis **(✓)**  
D) Attack tree construction  

✅ **Обоснование:**  
Хотя все перечисленные — важные части threat modeling, **оценка рисков и анализа воздействия (risk prioritization)** — ключ к **рациональному распределению ресурсов** на защиту. Это особенно важно в системах с высокими требованиями к безопасности.

---

**3. Какой из следующих принципов архитектуры лучше всего поддерживает реализацию принципа "least privilege" в облачной среде?**  
A) Monolithic architecture  
B) Serverless functions with fine-grained IAM policies **(✓)**  
C) Shared hosting model  
D) Single-tenant bare-metal deployment  

✅ **Обоснование:**  
**Serverless-функции** с **точными политиками IAM** позволяют выдавать минимально необходимые права **на уровне функции**, что идеально соответствует принципу **least privilege**. Это сложнее реализовать в монолитах или shared-средах.

---

**4. Какой из следующих аспектов является основным вызовом при реализации Zero Trust в legacy-системах?**  
A) Lack of modern encryption algorithms  
B) Absence of cloud-native tools  
C) Inability to implement fine-grained access controls at scale **(✓)**  
D) High cost of new hardware  

✅ **Обоснование:**  
**Legacy-системы** часто не поддерживают современные протоколы (mTLS, fine-grained auth), и их архитектура не позволяет легко внедрить **контроль доступа на уровне запроса**. Это делает масштабирование Zero Trust крайне сложным.

---

**5. Какой из следующих паттернов архитектуры обеспечивает наилучшую защиту от атак типа "data exfiltration"?**  
A) Monolith with centralized logging  
B) Microservices with sidecar proxies and egress traffic control **(✓)**  
C) SOA with ESB  
D) Peer-to-peer network  

✅ **Обоснование:**  
**Sidecar proxy** (например, Envoy в Istio) позволяет **контролировать весь входящий и исходящий трафик** микросервиса, включая **egress**. Это позволяет **фильтровать и логировать** подозрительные попытки утечки данных.

---

**6. Какой из следующих аспектов является критически важным при проектировании системы с высокими требованиями к доступности и безопасности?**  
A) Fail-open security mechanisms  
B) Redundant security layers with fail-safe defaults **(✓)**  
C) Centralized authentication only  
D) Single-factor authentication  

✅ **Обоснование:**  
**Fail-safe defaults** гарантируют, что система **останется в безопасном состоянии** даже при сбое. При этом **избыточные (redundant) уровни защиты** обеспечивают **высокую доступность** и защиту от единичных точек отказа.

---

**7. Что является основным архитектурным вызовом при интеграции сторонних SaaS-решений в Zero Trust-среду?**  
A) Lack of encryption in transit  
B) Inability to enforce consistent identity and access policies across trust boundaries **(✓)**  
C) High latency of API calls  
D) Use of proprietary protocols  

✅ **Обоснование:**  
Внешние SaaS-сервисы находятся **вне зоны доверия**. Интеграция их в Zero Trust требует **единых политик идентификации и доступа**, что сложно реализовать из-за разрозненности систем и отсутствия контроля над их инфраструктурой.

---

**8. Какой из следующих подходов наиболее эффективно защищает систему от атак на уровне приложения (L7)?**  
A) Network firewalls only  
B) WAF + API gateway with schema validation and rate limiting **(✓)**  
C) IDS/IPS at network level  
D) Strong perimeter firewall  

✅ **Обоснование:**  
Атаки уровня приложения (OWASP Top 10) требуют **защиты на уровне логики приложения**. **WAF + API Gateway** с **валидацией схем и рейт-лимитингом** обеспечивают **глубокую защиту** от инъекций, переполнений и злоупотреблений.

---

**9. Какой из следующих аспектов является наиболее сложным при реализации "defense in depth" в гибридной облачной архитектуре?**  
A) Consistent policy enforcement across on-premises and cloud environments **(✓)**  
B) Choosing the right cloud provider  
C) Encrypting data in transit  
D) Implementing a SIEM system  

✅ **Обоснование:**  
В **гибридной среде** сложно обеспечить **единый подход к политикам безопасности** — между локальной инфраструктурой и облаком. Это требует унификации инструментов, политик и управления доступом.

---

**10. Какой из следующих подходов к управлению секретами наиболее безопасен в динамической среде контейнеров (Kubernetes)?**  
A) Storing secrets in ConfigMaps  
B) Injecting secrets via init containers  
C) Using external secret management system with auto-rotation and just-in-time access **(✓)**  
D) Mounting secrets as environment variables  

✅ **Обоснование:**  
**Внешние системы управления секретами** (например, HashiCorp Vault, AWS Secrets Manager) с **автоматической ротацией и JIT-доступом** обеспечивают **наибольшую безопасность** в динамичной среде, где контейнеры часто создаются и уничтожаются.

---
