```plantuml
@startuml
!define C4_CONTEXT() 
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

C4_CONTEXT()
title OAuth 2.1 Authentication Context

Person(user, "End User", "Human user wanting to access protected resources")

System_Boundary(b1, "Client System") {
    System(client_app, "Client Application", "Web app, mobile app, or desktop application\nrequesting access to user's resources")
}

System_Boundary(b2, "OAuth 2.1 Authorization Server") {
    System(auth_server, "Authorization Server", "Authenticates users and issues access tokens\nImplements OAuth 2.1 security features")
}

System_Boundary(b3, "Protected Resources") {
    System(resource_server, "Resource Server", "API or service hosting protected user data\nValidates access tokens for authorization")
}

Rel(user, client_app, "1. Uses application")
Rel(client_app, auth_server, "2. Authorization Request\n(with PKCE)\n3. Token Request")
Rel(auth_server, client_app, "4. Authorization Code\n5. Access/Refresh Tokens")
Rel(client_app, resource_server, "6. Access Protected Resources\n(with Access Token)")
Rel(resource_server, auth_server, "7. Validate Access Token")

note right of client_app
    OAuth 2.1 Flow Steps:
    1. Client redirects user to Authorization Server
    2. User authenticates and grants consent
    3. Authorization Server returns authorization code
    4. Client exchanges code for access token
    5. Client accesses protected resources
    6. Resource Server validates token with Authorization Server
end note

@enduml
```

Эта контекстная диаграмма показывает:

- **Четыре основных компонента**: Пользователь, Клиентское приложение, Сервер авторизации и Сервер ресурсов
- **Основные потоки данных** между компонентами
- **Заметки** с ключевыми особенностями OAuth 2.1 и шагами процесса аутентификации
- **Границы систем** для лучшего понимания архитектуры

Диаграмма следует стандартам C4 Model и показывает систему на самом высоком уровне абстракции.