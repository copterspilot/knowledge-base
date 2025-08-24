Вот расширенные схемы OAuth 2.1 Auth Flow + Refresh для различных типов клиентов:

## 🌐 **Web Application (Traditional Server-Side)**

```mermaid
sequenceDiagram
    participant U as User
    participant WA as Web App (Server)
    participant AS as Authorization Server
    participant RS as Resource Server
    participant DB as Secure Storage (Server-side)

    U->>WA: 1. Navigate to app
    WA->>U: 2. Display login button
    U->>WA: 3. Click login
    WA->>AS: 4. Redirect to Auth Server (Authorization Code + PKCE)
    AS->>U: 5. Login form
    U->>AS: 6. Enter credentials
    AS->>U: 7. Consent screen
    U->>AS: 8. Approve consent
    AS->>WA: 9. Redirect back with Authorization Code
    WA->>AS: 10. Exchange Code for Tokens (Backend-to-Backend)
    AS->>DB: 11. Store Refresh Token securely
    AS->>WA: 12. Return Access Token + Refresh Token
    WA->>RS: 13. Access API with Access Token
    RS->>AS: 14. Validate Token
    AS->>RS: 15. Token Valid
    RS->>WA: 16. Return Resource
    WA->>U: 17. Display Resource
    
    %% Refresh Flow
    WA->>DB: 18. Retrieve Refresh Token
    WA->>AS: 19. Request new Access Token
    AS->>DB: 20. Validate Refresh Token
    AS->>DB: 21. Rotate Refresh Token
    AS->>WA: 22. Return new Access Token + Refresh Token
```

**Особенности:**
- ✅ **PKCE** для защиты от code interception
- ✅ **Backend storage** токенов (безопасно)
- ✅ **Secure session cookies** (HttpOnly, SameSite)
- ✅ **Token rotation** при refresh

---

## 🖥️ **Single Page Application (SPA)**

```mermaid
sequenceDiagram
    participant U as User
    participant B as Browser (SPA)
    participant AS as Authorization Server
    participant RS as Resource Server
    participant ST as Secure Token Storage

    U->>B: 1. Load SPA
    B->>AS: 2. Silent Auth Check (iframe/cookie)
    AS->>B: 3. Redirect to Login (if no session)
    B->>AS: 4. Authorization Request (Authorization Code + PKCE)
    AS->>U: 5. Login + Consent
    U->>AS: 6. Authenticate + Consent
    AS->>B: 7. Redirect with Authorization Code
    B->>AS: 8. Exchange Code for Tokens
    AS->>B: 9. Return Access Token + Refresh Token
    
    %% Token Storage Options
    B->>ST: 10a. Store in Memory (Secure)
    B->>ST: 10b. Store in Secure Cookie (HttpOnly)
    
    B->>RS: 11. Access API with Access Token
    RS->>AS: 12. Validate Token
    AS->>RS: 13. Token Valid
    RS->>B: 14. Return Resource
    B->>U: 15. Display Resource
    
    %% Token Management
    loop Token Refresh
        B->>ST: 16. Check token expiry
        ST->>B: 17. Token expired
        B->>AS: 18. Silent Refresh (iframe)
        AS->>B: 19. New Access Token
    end
```

**Особенности:**
- ✅ **PKCE обязательна** (публичный клиент)
- ⚠️ **Ограниченное хранение токенов** (no HttpOnly cookies)
- ✅ **Silent refresh** через iframe
- ⚠️ **Внимание к XSS/CSRF**

---

## 📱 **Mobile Application (Native)**

```mermaid
sequenceDiagram
    participant U as User
    participant MA as Mobile App
    participant AS as Authorization Server
    participant RS as Resource Server
    participant KS as Keychain/Keystore

    U->>MA: 1. Open App
    MA->>AS: 2. Check for existing session
    AS->>MA: 3. No valid session
    MA->>AS: 4. Authorization Request (Custom URI Scheme)
    AS->>U: 5. System Browser (Secure)
    U->>AS: 6. Authenticate + Consent
    AS->>MA: 7. Redirect via Custom URI
    MA->>AS: 8. Exchange Code for Tokens
    AS->>MA: 9. Return Access Token + Refresh Token
    MA->>KS: 10. Secure Storage (Keychain/Keystore)
    
    MA->>RS: 11. Access API with Access Token
    RS->>AS: 12. Validate Token
    AS->>RS: 13. Token Valid
    RS->>MA: 14. Return Resource
    MA->>U: 15. Display Resource
    
    %% Background Refresh
    MA->>KS: 16. Retrieve Refresh Token
    MA->>AS: 17. Token Refresh Request
    AS->>KS: 18. Validate + Rotate Refresh Token
    AS->>MA: 19. New Access Token + Refresh Token
```

**Особенности:**
- ✅ **System Browser** для безопасности
- ✅ **Custom URI schemes** для redirect
- ✅ **Secure storage** (Keychain/Keystore)
- ✅ **Background refresh** возможен
- ✅ **AppAuth libraries** рекомендованы

---

## 🌐 **Progressive Web App (PWA)**

```mermaid
sequenceDiagram
    participant U as User
    participant PWA as PWA (Browser)
    participant AS as Authorization Server
    participant RS as Resource Server
    participant SW as Service Worker

    U->>PWA: 1. Install + Open PWA
    PWA->>AS: 2. Check Auth Status (Service Worker)
    AS->>PWA: 3. No session found
    PWA->>AS: 4. Authorization Code Flow + PKCE
    AS->>U: 5. Login + Consent (in browser)
    U->>AS: 6. Authenticate
    AS->>PWA: 7. Redirect with Authorization Code
    PWA->>AS: 8. Exchange Code for Tokens
    AS->>PWA: 9. Return Access Token + Refresh Token
    
    %% Storage Strategy
    PWA->>SW: 10a. Store in Service Worker (Memory)
    PWA->>SW: 10b. Secure Cookie (HttpOnly, SameSite)
    
    PWA->>RS: 11. API Request with Access Token
    RS->>AS: 12. Token Validation
    AS->>RS: 13. Token Valid
    RS->>PWA: 14. Return Data
    PWA->>U: 15. Update UI
    
    %% Offline + Sync
    SW->>SW: 16. Cache tokens for offline use
    SW->>PWA: 17. Background sync when online
```

**Особенности:**
- ✅ **Service Worker** для offline capabilities
- ✅ **Hybrid approach** (web + native-like features)
- ⚠️ **Storage limitations** как у SPA
- ✅ **Push notifications** для session management
- ✅ **Installable** с homescreen

---

## 🛡️ **Сравнительная таблица реализаций**

| Характеристика | **Web App** | **SPA** | **Mobile** | **PWA** |
|----------------|-------------|---------|------------|---------|
| **Хранение токенов** | Server-side DB | Browser memory/cookies | Keychain/Keystore | Service Worker |
| **PKCE** | Рекомендуется | Обязательна | Рекомендуется | Обязательна |
| **Redirect URI** | HTTPS URL | HTTPS URL | Custom Scheme | HTTPS URL |
| **Browser** | Server-side | In-app | System | Browser/PWA |
| **Refresh Token Storage** | Secure Server DB | Browser-limited | Secure OS Storage | Service Worker |
| **Offline Support** | Limited | No | Yes | Yes (Service Worker) |
| **Security Level** | Высокий | Средний | Высокий | Средний-Высокий |

---

## 🔐 **Рекомендации по безопасности:**

### **Для всех клиентов:**
- ✅ Использовать **Authorization Code Flow + PKCE**
- ✅ Реализовать **Token Rotation**
- ✅ Настроить **Session Management**
- ✅ Включить **Rate Limiting**
- ✅ Использовать **Short-lived Access Tokens**

### **Специфично:**
- **Web Apps**: Хранить токены на сервере
- **SPA/PWA**: Минимизировать хранение токенов, использовать silent refresh
- **Mobile**: Использовать системный браузер, безопасное хранение

Эти схемы демонстрируют современные подходы к реализации OAuth 2.1 для различных типов клиентов с учетом их специфики и ограничений.