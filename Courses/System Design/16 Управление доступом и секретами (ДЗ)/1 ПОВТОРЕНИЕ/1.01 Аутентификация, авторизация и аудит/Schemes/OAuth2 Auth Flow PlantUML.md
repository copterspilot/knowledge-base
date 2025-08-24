## 🌐 **Web Application (Traditional Server-Side)**

```plantuml
@startuml
actor "User" as U
participant "Web App\n(Server)" as WA
participant "Authorization\nServer" as AS
participant "Resource\nServer" as RS
database "Secure Storage\n(Server-side)" as DB

U -> WA : 1. Navigate to app
WA -> U : 2. Display login button
U -> WA : 3. Click login
WA -> AS : 4. Redirect to Auth Server\n(Authorization Code + PKCE)
AS -> U : 5. Login form
U -> AS : 6. Enter credentials
AS -> U : 7. Consent screen
U -> AS : 8. Approve consent
AS -> WA : 9. Redirect back with\nAuthorization Code
WA -> AS : 10. Exchange Code for Tokens\n(Backend-to-Backend)
AS -> DB : 11. Store Refresh Token securely
AS -> WA : 12. Return Access Token +\nRefresh Token
WA -> RS : 13. Access API with\nAccess Token
RS -> AS : 14. Validate Token
AS -> RS : 15. Token Valid
RS -> WA : 16. Return Resource
WA -> U : 17. Display Resource

 note right of WA
 Refresh Flow:
 end note
 WA -> DB : 18. Retrieve Refresh Token
 WA -> AS : 19. Request new Access Token
 AS -> DB : 20. Validate Refresh Token
 AS -> DB : 21. Rotate Refresh Token
 AS -> WA : 22. Return new Access Token +\nRefresh Token
 

@enduml
```

## 🖥️ **Single Page Application (SPA)**

```plantuml
@startuml
actor "User" as U
participant "Browser\n(SPA)" as B
participant "Authorization\nServer" as AS
participant "Resource\nServer" as RS
participant "Secure Token\nStorage" as ST

U -> B : 1. Load SPA
B -> AS : 2. Silent Auth Check\n(iframe/cookie)
AS -> B : 3. Redirect to Login\n(if no session)
B -> AS : 4. Authorization Request\n(Authorization Code + PKCE)
AS -> U : 5. Login + Consent
U -> AS : 6. Authenticate + Consent
AS -> B : 7. Redirect with\nAuthorization Code
B -> AS : 8. Exchange Code for Tokens
AS -> B : 9. Return Access Token +\nRefresh Token

B -> ST : 10a. Store in Memory\n(Secure)
B -> ST : 10b. Store in Secure Cookie\n(HttpOnly)

B -> RS : 11. Access API with\nAccess Token
RS -> AS : 12. Validate Token
AS -> RS : 13. Token Valid
RS -> B : 14. Return Resource
B -> U : 15. Display Resource

loop Token Refresh
 B -> ST : 16. Check token expiry
 ST -> B : 17. Token expired
 B -> AS : 18. Silent Refresh\n(iframe)
 AS -> B : 19. New Access Token
end

@enduml
```

## 📱 **Mobile Application (Native)**

```plantuml
@startuml
actor "User" as U
participant "Mobile App" as MA
participant "Authorization\nServer" as AS
participant "Resource\nServer" as RS
database "Keychain/\nKeystore" as KS

U -> MA : 1. Open App
MA -> AS : 2. Check for existing session
AS -> MA : 3. No valid session
MA -> AS : 4. Authorization Request\n(Custom URI Scheme)
AS -> U : 5. System Browser\n(Secure)
U -> AS : 6. Authenticate + Consent
AS -> MA : 7. Redirect via\nCustom URI
MA -> AS : 8. Exchange Code for Tokens
AS -> MA : 9. Return Access Token +\nRefresh Token
MA -> KS : 10. Secure Storage\n(Keychain/Keystore)

MA -> RS : 11. Access API with\nAccess Token
RS -> AS : 12. Validate Token
AS -> RS : 13. Token Valid
RS -> MA : 14. Return Resource
MA -> U : 15. Display Resource

MA -> KS : 16. Retrieve Refresh Token
MA -> AS : 17. Token Refresh Request
AS -> KS : 18. Validate + Rotate\nRefresh Token
AS -> MA : 19. New Access Token +\nRefresh Token

@enduml
```

## 🌐 **Progressive Web App (PWA)**

```plantuml
@startuml
actor "User" as U
participant "PWA\n(Browser)" as PWA
participant "Authorization\nServer" as AS
participant "Resource\nServer" as RS
participant "Service\nWorker" as SW

U -> PWA : 1. Install + Open PWA
PWA -> AS : 2. Check Auth Status\n(Service Worker)
AS -> PWA : 3. No session found
PWA -> AS : 4. Authorization Code Flow + PKCE
AS -> U : 5. Login + Consent\n(in browser)
U -> AS : 6. Authenticate
AS -> PWA : 7. Redirect with\nAuthorization Code
PWA -> AS : 8. Exchange Code for Tokens
AS -> PWA : 9. Return Access Token +\nRefresh Token

PWA -> SW : 10a. Store in Service Worker\n(Memory)
PWA -> SW : 10b. Secure Cookie\n(HttpOnly, SameSite)

PWA -> RS : 11. API Request with\nAccess Token
RS -> AS : 12. Token Validation
AS -> RS : 13. Token Valid
RS -> PWA : 14. Return Data
PWA -> U : 15. Update UI

SW -> SW : 16. Cache tokens for\noffline use
SW -> PWA : 17. Background sync\nwhen online

@enduml
```

## 🛡️ **Сравнительная таблица реализаций**

| Характеристика | Web App | SPA | Mobile | PWA |
|:---|:---|:---|:---|
| Хранение токенов | Server-side DB | Browser memory/cookies | Keychain/Keystore | Service Worker |
| PKCE | Рекомендуется | Обязательна | Рекомендуется | Обязательна |
| Redirect URI | HTTPS URL | HTTPS URL | Custom Scheme | HTTPS URL |
| Browser | Server-side | In-app | System | Browser/PWA |
| Refresh Token Storage | Secure Server DB | Browser-limited | Secure OS Storage | Service Worker |
| Offline Support | Limited | No | Yes | Yes (Service Worker) |
| Security Level | Высокий | Средний | Высокий | Средний-Высокий |

Сравнение реализаций OAuth 2.1 для разных типов клиентов

Эти диаграммы демонстрируют различные подходы к реализации OAuth 2.1 для различных типов клиентов с учетом их специфики и ограничений.