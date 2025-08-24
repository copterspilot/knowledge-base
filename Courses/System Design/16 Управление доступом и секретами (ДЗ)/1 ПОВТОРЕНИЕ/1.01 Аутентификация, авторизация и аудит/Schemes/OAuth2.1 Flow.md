Вот две схемы OAuth 2.1 Authentication Flow в формате Mermaid:

## Схема 1: OAuth 2.1 Flow с основными участниками

```mermaid
sequenceDiagram
    participant U as User
    participant A as Application
    participant AS as Authentication Server
    participant RS as Resource Server

    U->>A: 1. Запрос доступа к ресурсу
    A->>U: 2. Перенаправление на Authentication Server
    U->>AS: 3. Аутентификация (логин/пароль)
    AS->>U: 4. Запрос согласия на предоставление доступа
    U->>AS: 5. Подтверждение согласия
    AS->>A: 6. Передача Authorization Code
    A->>AS: 7. Запрос Access Token (с Authorization Code)
    AS->>A: 8. Выдача Access Token (+ Refresh Token)
    A->>RS: 9. Запрос ресурса с Access Token
    RS->>AS: 10. Проверка токена
    AS->>RS: 11. Подтверждение валидности токена
    RS->>A: 12. Предоставление запрашиваемого ресурса
    A->>U: 13. Отображение ресурса пользователю
```

## Схема 2: OAuth 2.1 Flow с расширенными участниками

```mermaid
sequenceDiagram
    participant U as User
    participant IDP as Identity Provider
    participant IAM as IAM System
    participant R as Resource
    participant S as SIEM/SOAR

    U->>IDP: 1. Инициация аутентификации
    IDP->>U: 2. Предоставление формы входа
    U->>IDP: 3. Ввод учетных данных
    IDP->>IAM: 4. Проверка учетных данных
    IAM->>IDP: 5. Подтверждение аутентификации
    IDP->>U: 6. Запрос согласия на доступ
    U->>IDP: 7. Подтверждение согласия
    IDP->>IAM: 8. Создание токенов доступа
    IAM->>IDP: 9. Передача Authorization Code
    IDP->>R: 10. Передача Authorization Code
    R->>IAM: 11. Запрос Access Token
    IAM->>R: 12. Выдача Access Token
    R->>S: 13. Логирование события доступа
    R->>R: 14. Проверка прав доступа через IAM
    R->>U: 15. Предоставление доступа к ресурсу
    S->>S: 16. Анализ логов и мониторинг угроз
```

Эти схемы демонстрируют поток аутентификации OAuth 2.1 с учетом различных уровней абстракции участников процесса. Первая схема показывает базовый поток, а вторая - более детализированное взаимодействие с системами безопасности и управления идентификацией.

Вот общая схема взаимодействия участников OAuth 2.0:

```mermaid
sequenceDiagram
    participant U as User (Resource Owner)
    participant C as Client Application
    participant AS as Authorization Server
    participant RS as Resource Server

    U->>C: 1. Доступ к защищенному ресурсу
    C->>U: 2. Перенаправление на Authorization Server
    U->>AS: 3. Аутентификация и предоставление согласия
    AS->>U: 4. Форма согласия на предоставление доступа
    U->>AS: 5. Подтверждение согласия
    AS->>C: 6. Передача Authorization Code
    C->>AS: 7. Запрос Access Token (с Authorization Code)
    AS->>C: 8. Выдача Access Token (+ Refresh Token)
    C->>RS: 9. Запрос ресурса с Access Token
    RS->>AS: 10. Проверка валидности токена
    AS->>RS: 11. Подтверждение валидности токена
    RS->>C: 12. Предоставление запрашиваемого ресурса
    C->>U: 13. Отображение ресурса пользователю
```

**Участники:**
- **User (Resource Owner)**: Владелец данных/ресурсов
- **Client Application**: Приложение, запрашивающее доступ
- **Authorization Server**: Сервер, выдающий токены доступа
- **Resource Server**: Сервер, хранящий защищенные ресурсы

Эта схема демонстрирует стандартный **Authorization Code Flow** OAuth 2.0, который обеспечивает безопасную аутентификацию и делегирование доступа.
