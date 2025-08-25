# **Defense in Depth Network Security in Google Cloud**  

Реализация защиты в Google Cloud основана на принципе **"Defense in Depth"**, где каждый слой обеспечивает дополнительный уровень безопасности. Рассмотрим каждый слой, примеры нарушений и чек-лист для проверки.  

---

## **1. Micro-segment Access to Applications and Services**  
### **🔹 IAM (API-based Access)**  
**Реализация:**  
- Использование **Google Cloud IAM** с принципом наименьших привилегий (PoLP).  
- Роли (`roles/`) вместо прямых разрешений (`*`).  
- **Conditional IAM** (например, ограничение доступа по IP или времени).  

**Пример нарушения:**  
- **Нарушение:** Слишком широкая роль `roles/editor` для сервисного аккаунта.  
- **Последствия:** В 2020 году злоумышленник получил доступ к данным GCP через сервисный аккаунт CI/CD.  
- **Решение:** Использовать `roles/viewer` или кастомные роли.  

**Чек-лист:**  
✅ Применены **организационные политики** (`gcloud org-policies`).  
✅ Все сервисные аккаунты имеют **минимальные права**.  
✅ Включен **Audit Logging** (Cloud Audit Logs).  

---

### **🔹 Istio Security (Micro-segmentation based on HTTP/S ID)**  
**Реализация:**  
- **Authorization Policies** (например, `ALLOW from namespace:prod`).  
- **mTLS** для сервис-сервисного взаимодействия.  

**Пример нарушения:**  
- **Нарушение:** Отсутствие политик Istio → Lateral Movement между неймспейсами.  
- **Последствия:** В 2021 году атакующий перемещался между подами, используя уязвимый веб-сервис.  
- **Решение:** Включить **`STRICT` mTLS** и **AuthorizationPolicy**.  

**Чек-лист:**  
✅ Включен **mTLS в режиме `STRICT`**.  
✅ Настроены **AuthorizationPolicy** для каждого сервиса.  

---

### **🔹 Firewalls per Service Account (Micro-segmentation for VM-based Apps)**  
**Реализация:**  
- **Firewall Rules** с привязкой к **Service Account**, а не IP.  
- Пример: `gcloud compute firewall-rules create allow-internal --service-account=backend-sa@...`.  

**Пример нарушения:**  
- **Нарушение:** Открытый `0.0.0.0/0` для SSH в продакшене.  
- **Последствия:** В 2019 году ботнет сканировал открытые порты и заражал VM.  
- **Решение:** Использовать **Identity-Aware Proxy (IAP)** для SSH.  

**Чек-лист:**  
✅ Нет правил с `0.0.0.0/0` (кроме Cloud Load Balancer).  
✅ Firewall Rules используют **теги или Service Accounts**.  

---

### **🔹 GKE Network Policies (Micro-segmentation within GKE Cluster)**  
**Реализация:**  
- **NetworkPolicy** (например, разрешить трафик только между `frontend` и `backend`).  
- **Calico/Cilium** для расширенного контроля.  

**Пример нарушения:**  
- **Нарушение:** Отсутствие NetworkPolicy → компрометация одного пода привела к доступу ко всем.  
- **Последствия:** Майнинг криптовалюты в продакшн-кластере (2022).  
- **Решение:** Включить **`network-policy`** в GKE.  

**Чек-лист:**  
✅ Включен **Network Policy Enforcement**.  
✅ Протестированы **изоляционные сценарии** (например, `deny-all` по умолчанию).  

---

## **2. Secure Your VPC for Private Deployments**  
### **🔹 VPC Service Controls (Data Exfiltration Protection)**  
**Реализация:**  
- Ограничение доступа к **GCS, BigQuery, Cloud SQL** только из доверенных VPC.  

**Пример нарушения:**  
- **Нарушение:** Отсутствие VPC SC → утечка данных в публичный бакет.  
- **Последствия:** Утечка PII-данных (2021, финансовая компания).  
- **Решение:** Включить **VPC Service Controls**.  

**Чек-лист:**  
✅ Настроены **периметры VPC SC** для sensitive data.  
✅ Проверены **исключения (Access Levels)**.  

---

### **🔹 VPC-level Firewalls (Secure VPC Boundaries)**  
**Реализация:**  
- **Hierarchical Firewall Policies** (на уровне организации).  
- **Deny by default** для входящего трафика.  

**Пример нарушения:**  
- **Нарушение:** Разрешен `0.0.0.0/0` для RDP → ransomware-атака.  
- **Решение:** Использовать **теги и Service Accounts**.  

**Чек-лист:**  
✅ Применена политика **"deny all ingress"** по умолчанию.  

---

### **🔹 Outbound NAT Proxy (Egress without Public IP)**  
**Реализация:**  
- **Cloud NAT** для исходящего трафика без публичных IP.  

**Пример нарушения:**  
- **Нарушение:** VM с публичным IP → DDoS-атака с GCP.  
- **Решение:** **Только Private IP + Cloud NAT**.  

**Чек-лист:**  
✅ Все VM используют **только приватные IP**.  

---

### **🔹 VPN & Interconnect (Private On-Prem Access)**  
**Реализация:**  
- **HA VPN** или **Partner Interconnect**.  
- **Tunnel Encryption (IPsec/IKEv2)**.  

**Чек-лист:**  
✅ Настроен **мониторинг VPN-туннелей**.  

---

## **3. Secure Internet-Facing Apps**  
### **🔹 Global LB (Anycast + DDoS Protection)**  
**Реализация:**  
- **Cloud Load Balancer + Cloud Armor**.  

**Пример нарушения:**  
- **Нарушение:** Прямой доступ к VM → DDoS (2020, 500 Gbps).  
- **Решение:** **Only LB → Backend**.  

**Чек-лист:**  
✅ Включен **Cloud Armor** (WAF + Rate Limiting).  

---

### **🔹 TLS Everywhere**  
**Реализация:**  
- **Google-managed certs** (или Let's Encrypt).  
- **Strict Transport Security (HSTS)**.  

**Чек-лист:**  
✅ Все сервисы используют **TLS 1.2+**.  

---

### **🔹 IAP (Identity-Aware Proxy)**  
**Реализация:**  
- **SSH/RDP только через IAP**.  

**Пример нарушения:**  
- **Нарушение:** Прямой SSH → компрометация (2021).  
- **Решение:** **Только IAP + 2FA**.  

**Чек-лист:**  
✅ Все VM **заблокированы для прямого SSH/RDP**.  

---

## **Итоговый чек-лист безопасности GCP**  
| Уровень                  | Проверка                                                                 |
|--------------------------|--------------------------------------------------------------------------|
| **IAM**                  | Минимальные права, Conditional Access, Audit Logs                        |
| **Istio**                | mTLS=STRICT, AuthorizationPolicy                                        |
| **GKE**                  | NetworkPolicy, PodSecurityPolicy                                         |
| **VPC**                  | Service Controls, Private Google Access                                  |
| **Firewall**             | Deny-all ingress, Service Account-based rules                           |
| **Cloud Armor**          | WAF, Rate Limiting, DDoS Protection                                     |
| **TLS**                  | Google-managed certs, HSTS                                              |
| **IAP**                  | SSH/RDP только через IAP                                                |

**Вывод:** Defense in Depth в GCP требует настройки **всех слоев** — от IAM до VPC SC. Нарушение на любом уровне ведет к компрометации всей системы.