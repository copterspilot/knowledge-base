```plantuml
@startuml
!define C4_CONTEXT() 
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

C4_CONTEXT()
title OAuth 2.1 Flow - Context Diagram

Person(resource_owner, "Resource Owner", "User who owns the protected resources\nand can grant access permissions")

System_Boundary(b1, "Client Side") {
    System(application, "Application", "Third-party application requesting access\nto resource owner's data (Web/Mobile/Desktop)")
    System(application_server, "Application Server", "Backend server of the client application\nHandles OAuth flow and business logic")
}

System_Boundary(b2, "Authorization Server") {
    System(authorization_server, "Authorization Server", "Authenticates resource owner and issues\naccess tokens using OAuth 2.1 protocol")
}

System_Boundary(b3, "Resource Server") {
    System(resource_server, "Resource Server", "Hosts protected resources owned by\nthe resource owner. Validates access tokens.")
}

Rel(resource_owner, application, "1. Uses the application")
Rel(application, application_server, "2. Requests protected resources")
Rel(application_server, authorization_server, "3. Authorization Request (OAuth 2.1)\n4. Token Request")
Rel(authorization_server, application_server, "5. Authorization Code\n6. Access/Refresh Tokens")
Rel(application_server, resource_server, "7. Access Protected Resources\n(with Access Token)")
Rel(resource_server, authorization_server, "8. Validate Access Token")

note right of authorization_server
    OAuth 2.1 Key Features:
    - PKCE Required
    - No Implicit Flow
    - Short-lived Access Tokens
    - Refresh Token Rotation
    - Enhanced Security
end note

note right of application_server
    OAuth 2.1 Flow Steps:
    1. Redirect to Authorization Server
    2. User authenticates and consents
    3. Receive Authorization Code
    4. Exchange Code for Tokens
    5. Access Protected Resources
    6. Handle Token Refresh
end note

@enduml
```

Эта диаграмма показывает:

- **Все пять указанных участников** системы OAuth 2.1
- **Поток взаимодействия** между участниками в хронологическом порядке
- **Заметки** с ключевыми особенностями OAuth 2.1 и шагами процесса
- **Группировку** участников по логическим границам системы
- **Соблюдение стандартов C4 Model** для контекстной диаграммы

Диаграмма отражает современный подход к OAuth 2.1 с акцентом на безопасность и разделение ответственности между компонентами.