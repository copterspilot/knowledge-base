```plantuml
@startuml
title OAuth 2.1 Authentication Flow

actor "User" as user
participant "Client Application" as client
participant "Authorization Server" as auth
participant "Resource Server" as resource

user -> client : 1. Access application
client -> user : 2. Redirect to Authorization Server\n(Authorization Request)
user -> auth : 3. User authenticates
auth -> user : 4. Authorization Server prompts for consent
user -> auth : 5. User grants consent
auth -> client : 6. Authorization Code (via redirect)
client -> auth : 7. Exchange Code for Tokens\n(Token Request)
auth -> auth : 8. Validate authorization code
auth -> client : 9. Access Token + Refresh Token
client -> resource : 10. Access Resource\n(with Access Token)
resource -> auth : 11. Validate Access Token
auth -> resource : 12. Token validation response
resource -> client : 13. Return protected resource
client -> user : 14. Display resource to user

== Token Refresh Flow ==

client -> auth : 15. Request new Access Token\n(with Refresh Token)
auth -> auth : 16. Validate refresh token
auth -> client : 17. New Access Token + (optional new Refresh Token)

note right of auth
    OAuth 2.1 Key Security Features:
    - PKCE (Proof Key for Code Exchange)
    - No implicit grant flow
    - Short-lived access tokens
    - Token rotation
    - Refresh token rotation
end note

note right of client
    PKCE Implementation:
    - Generate code_verifier
    - Create code_challenge
    - Send code_challenge in auth request
    - Send code_verifier in token request
end note

@enduml
```

Эта схема включает:

1. **Основной поток авторизации** через Authorization Code Grant
2. **Поток обновления токенов**
3. **Заметки о ключевых особенностях OAuth 2.1**:
   - Использование PKCE для защиты от перехвата кодов авторизации
   - Отсутствие Implicit Grant (который был в OAuth 2.0)
   - Ротация токенов для повышения безопасности

Если нужно, могу создать более детализированную версию для конкретного типа приложения (web, mobile, SPA и т.д.).