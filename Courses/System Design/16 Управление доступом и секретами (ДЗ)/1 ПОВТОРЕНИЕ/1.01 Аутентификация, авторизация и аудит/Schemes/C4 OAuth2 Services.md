```plantuml
@startuml
!define C4_CONTEXT() 
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

!define C4_CONTAINER()
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

!define C4_COMPONENT()
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

' Контекстная диаграмма
C4_CONTEXT()
title System Context diagram for OAuth 2.1 Authentication

Person(user, "End User", "Human user accessing protected resources")
System_Boundary(b1, "External Systems") {
    System(ext_system, "External System", "Third-party application requesting access")
}
System_Boundary(b2, "OAuth 2.1 System") {
    System(client_app, "Client Application", "Application requesting access to user resources")
    System(auth_server, "Authorization Server", "Issues and validates access tokens")
    System(resource_server, "Resource Server", "Hosts protected resources")
}

Rel(user, client_app, "Uses")
Rel(client_app, ext_system, "Integrates with")
Rel(ext_system, auth_server, "Requests authorization")
Rel(client_app, auth_server, "1. Authorization Request,\n7. Token Request")
Rel(auth_server, client_app, "2. Redirect with Authorization Code,\n8. Returns Access/Refresh Tokens")
Rel(client_app, resource_server, "9. Access Protected Resources\n(with Access Token)")
Rel(resource_server, auth_server, "10. Validate Access Token")
Rel(auth_server, resource_server, "11. Token Validation Response")

note right of auth_server
    OAuth 2.1 Key Features:
    - PKCE Support
    - No Implicit Flow
    - Token Rotation
    - Short-lived Tokens
end note

@enduml

@startuml
' Контейнерная диаграмма
C4_CONTAINER()
title Container diagram for OAuth 2.1 System

Person(user, "End User", "Human user accessing protected resources")

System_Boundary(c1, "Client System") {
    Container(client_web, "Web Application", "JavaScript, HTML", "Provides user interface")
    Container(client_backend, "Application Backend", "Java, Spring Boot", "Handles OAuth flow logic")
}

System_Boundary(c2, "OAuth 2.1 Platform") {
    Container(auth_service, "Authorization Service", "Java, Spring Security", "Handles authentication and authorization")
    Container(token_service, "Token Service", "Java, Spring Security", "Issues and validates tokens")
    Container(resource_service, "Resource Service", "Java, Spring Boot", "Provides protected APIs")
    Container(db, "Database", "PostgreSQL", "Stores user credentials, client info, tokens")
}

Rel(user, client_web, "Uses web interface")
Rel(client_web, client_backend, "API calls")
Rel(client_backend, auth_service, "1. Authorization Request (with PKCE)")
note right on link
    GET /oauth2/authorize?
    response_type=code&
    client_id=CLIENT_ID&
    redirect_uri=REDIRECT_URI&
    scope=read&
    code_challenge=CODE_CHALLENGE&
    code_challenge_method=S256
end note

Rel(auth_service, client_backend, "2. Redirect with Authorization Code")
note right on link
    HTTP 302 Redirect
    Location: REDIRECT_URI?
    code=AUTHORIZATION_CODE
end note

Rel(client_backend, token_service, "3. Token Request (with code_verifier)")
note right on link
    POST /oauth2/token
    grant_type=authorization_code&
    code=AUTHORIZATION_CODE&
    redirect_uri=REDIRECT_URI&
    client_id=CLIENT_ID&
    code_verifier=CODE_VERIFIER
end note

Rel(token_service, auth_service, "Validates authorization code")
Rel(token_service, db, "Stores/Retrieves token data")
Rel(token_service, client_backend, "4. Returns Access/Refresh Tokens")
note right on link
    {
      "access_token": "ACCESS_TOKEN",
      "token_type": "Bearer",
      "expires_in": 3600,
      "refresh_token": "REFRESH_TOKEN"
    }
end note

Rel(client_backend, resource_service, "5. Access Protected Resources")
note right on link
    GET /api/protected-resource
    Authorization: Bearer ACCESS_TOKEN
end note

Rel(resource_service, token_service, "6. Validate Access Token")
Rel(token_service, db, "Checks token validity")
Rel(token_service, resource_service, "Token validation response")
Rel(resource_service, client_backend, "Returns protected data")

note right of auth_service
    OAuth 2.1 Security:
    - PKCE Implementation
    - Authorization Code Storage
    - Consent Management
end note

note right of token_service
    Token Management:
    - Access Token Generation
    - Refresh Token Handling
    - Token Validation
    - Token Revocation
end note

@enduml

@startuml
' Компонентная диаграмма для Authorization Service
C4_COMPONENT()
title Component diagram for Authorization Service

Container_Boundary(auth_boundary, "Authorization Service") {
    Component(auth_controller, "Authorization Controller", "Spring MVC", "Handles authorization requests")
    Component(pkce_validator, "PKCE Validator", "Java Service", "Validates PKCE parameters")
    Component(consent_manager, "Consent Manager", "Java Service", "Manages user consent")
    Component(code_generator, "Authorization Code Generator", "Java Service", "Generates secure codes")
    Component(code_store, "Authorization Code Store", "Java Repository", "Stores codes temporarily")
    Component(client_validator, "Client Validator", "Java Service", "Validates client applications")
}

Container_Boundary(token_boundary, "Token Service") {
    Component(token_controller, "Token Controller", "Spring MVC", "Handles token requests")
    Component(code_verifier, "Code Verifier", "Java Service", "Verifies authorization codes")
    Component(token_generator, "Token Generator", "Java Service", "Generates access/refresh tokens")
    Component(token_store, "Token Store", "Java Repository", "Stores token information")
    Component(token_validator, "Token Validator", "Java Service", "Validates access tokens")
}

Container(db, "Database", "PostgreSQL", "Persistence layer")

Rel(auth_controller, client_validator, "Validates client")
Rel(auth_controller, pkce_validator, "Validates PKCE")
Rel(auth_controller, consent_manager, "Manages user consent")
Rel(auth_controller, code_generator, "Generates auth code")
Rel(code_generator, code_store, "Stores auth code")
Rel(code_store, db, "Persists codes")

Rel(token_controller, code_verifier, "Verifies auth code")
Rel(code_verifier, code_store, "Retrieves stored code")
Rel(token_controller, token_generator, "Generates tokens")
Rel(token_generator, token_store, "Stores tokens")
Rel(token_store, db, "Persists tokens")
Rel(token_controller, token_validator, "Validates tokens")

note right of pkce_validator
    PKCE Flow:
    1. Validate code_challenge
    2. Validate code_challenge_method
    3. Store for later verification
end note

note right of code_verifier
    Code Verification:
    1. Retrieve stored code_challenge
    2. Verify code_verifier matches
    3. Generate tokens if valid
end note

@enduml
```

Эта C4-диаграмма включает три уровня:

1. **Контекстная диаграмма** - показывает систему в целом и взаимодействие с внешними участниками
2. **Контейнерная диаграмма** - детализирует внутреннюю архитектуру системы и потоки данных
3. **Компонентная диаграмма** - показывает внутреннюю структуру Authorization и Token сервисов

Ключевые особенности OAuth 2.1 отражены в заметках:
- Использование PKCE для защиты
- Отсутствие Implicit Flow
- Ротация токенов
- Валидация через отдельные сервисы