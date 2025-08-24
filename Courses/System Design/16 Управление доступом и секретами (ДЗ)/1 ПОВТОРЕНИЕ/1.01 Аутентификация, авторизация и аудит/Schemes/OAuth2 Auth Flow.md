## Схема 1: OAuth 2.1 Flow с основными участниками

```plantuml
@startuml
actor "User" as U
participant "Application" as A
participant "Authentication Server" as AS
participant "Resource Server" as RS

U -> A: 1. Запрос доступа к ресурсу
A -> U: 2. Перенаправление на Authentication Server
U -> AS: 3. Аутентификация (логин/пароль)
AS -> U: 4. Запрос согласия на предоставление доступа
U -> AS: 5. Подтверждение согласия
AS -> A: 6. Передача Authorization Code
A -> AS: 7. Запрос Access Token (с Authorization Code)
AS -> A: 8. Выдача Access Token (+ Refresh Token)
A -> RS: 9. Запрос ресурса с Access Token
RS -> AS: 10. Проверка токена
AS -> RS: 11. Подтверждение валидности токена
RS -> A: 12. Предоставление запрашиваемого ресурса
A -> U: 13. Отображение ресурса пользователю
@enduml
```

## Схема 2: OAuth 2.1 Flow с расширенными участниками

```plantuml
@startuml
actor "User" as U
participant "Identity Provider" as IDP
participant "IAM System" as IAM
participant "Resource" as R
participant "SIEM/SOAR" as S

U -> IDP: 1. Инициация аутентификации
IDP -> U: 2. Предоставление формы входа
U -> IDP: 3. Ввод учетных данных
IDP -> IAM: 4. Проверка учетных данных
IAM -> IDP: 5. Подтверждение аутентификации
IDP -> U: 6. Запрос согласия на доступ
U -> IDP: 7. Подтверждение согласия
IDP -> IAM: 8. Создание токенов доступа
IAM -> IDP: 9. Передача Authorization Code
IDP -> R: 10. Передача Authorization Code
R -> IAM: 11. Запрос Access Token
IAM -> R: 12. Выдача Access Token
R -> S: 13. Логирование события доступа
R -> R: 14. Проверка прав доступа через IAM
R -> U: 15. Предоставление доступа к ресурсу
S -> S: 16. Анализ логов и мониторинг угроз
@enduml
```

## Схема 3: OAuth 2.1 Flow с учетом требований безопасности

```plantuml
@startuml
actor "End User" as EU
actor "Admin" as ADM
participant "Client Application" as CA
participant "Authorization Server" as AUTH
participant "Identity Provider" as IDP
participant "Identity Management (IAM)" as IAM
participant "Resource Server" as RS
participant "API Gateway" as GW
participant "Security Monitoring" as SEC
database "Audit Log" as AUDIT

EU -> CA: 1. Запрос доступа к ресурсу
CA -> AUTH: 2. Перенаправление на авторизацию
EU -> IDP: 3. Аутентификация (MFA)
IDP -> IAM: 4. Верификация учетных данных
IAM -> IDP: 5. Подтверждение идентичности
IDP -> EU: 6. Запрос согласия OAuth
EU -> IDP: 7. Предоставление согласия
IDP -> AUTH: 8. Генерация Authorization Code
AUTH -> CA: 9. Передача кода авторизации
CA -> AUTH: 10. Запрос Access Token
AUTH -> IAM: 11. Проверка прав доступа
IAM -> AUTH: 12. Подтверждение разрешений
AUTH -> CA: 13. Выдача Access Token
CA -> GW: 14. Запрос к защищенному API
GW -> AUTH: 15. Валидация токена
AUTH -> GW: 16. Подтверждение валидности
GW -> RS: 17. Передача запроса
RS -> RS: 18. Обработка запроса
RS -> GW: 19. Ответ с данными
GW -> SEC: 20. Логирование события безопасности
SEC -> AUDIT: 21. Запись в журнал аудита
GW -> CA: 22. Передача результата
CA -> EU: 23. Отображение ресурса
ADM -> AUDIT: 24. Аудит и мониторинг
@enduml
```

**Преимущества:**
1. **API Gateway** - централизованная точка контроля доступа
2. **Admin** - для управления и аудита системы
3. **Audit Log** - отдельное хранилище для логов безопасности
4. **Security Monitoring** - специализированная система мониторинга
5. **MFA** - многофакторная аутентификация для повышения безопасности

Этот набор лучше отражает современные требования к безопасности и соответствует принципам Zero Trust Architecture.