# Backend: как всё работает (6 пунктов)

1. **Точка входа и конфигурация API**  
   Приложение стартует в `eUDrive.Api/Program.cs`: поднимаются HTTP/HTTPS endpoints, подключается строка БД через `DbSession`, включаются CORS, Swagger, JWT-аутентификация и авторизация.

2. **Маршрутизация запросов в контроллеры**  
   Клиентские запросы приходят в контроллеры `eUDrive.Api/Controller/*Controller.cs` (`Auth`, `User`, `Product`, `Order`, `Certificate`, `Category`, `Health`), где определены API-роуты и правила доступа (`[Authorize]`, роли и т.д.).

3. **Делегирование бизнес-логике**  
   Контроллеры не выполняют сложную доменную логику сами: они создают `BusinessLogic.BusinessLogic` и получают нужные action-интерфейсы (`IUserActions`, `IProductActions`, `IOrderActions`, `ICertificateActions`).

4. **Flow-слой как фасад use-case’ов**  
   `UserFlow`, `ProductFlow`, `OrderFlow`, `CertificateFlow` (в `eUDrive.BusinessLogic/Functions`) принимают вызовы контроллеров и проксируют их в конкретные `Execute...Action` методы core-слоя.

5. **Core-операции и доступ к данным**  
   В core-слое выполняются основные операции (валидация, CRUD, бизнес-правила, формирование ответов), а доступ к данным идёт через EF Core контексты из `eUDrive.DataAccess/Context` (`UserContext`, `ProductContext`, `OrderContext`, `CertificateContext`) и их конфигурации/миграции.

6. **Формирование ответа клиенту**  
   После выполнения action-методов контроллер возвращает результат в HTTP-ответ (`Ok`, `BadRequest`, `NotFound`), а для защищённых роутов доступ определяется JWT-токеном и ролями пользователя.
