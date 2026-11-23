# AI-слід — Завдання 5 (Компоненти)

## Вербатимний запит користувача (сесія)
> "продовжити з коміту 12: додати task-5, ai/, переписати DEFENSE/spec під DoD, MADR для ADR, пушити на GitHub під Веронікою"

## Результат роботи (узагальнено)
Побудовано компонентну діаграму бібліотеки: Web UI, API Gateway, Catalog/Loan/Member/Reservation/Notification Service, Data Access, Payment Adapter, Identity Adapter, БД і зовнішні системи (Payment, OAuth/Identity, Email/SMS). Зовнішні системи ізольовано адаптерами; API Gateway — єдина точка входу.

## Фідбек і виправлення
1. Зовнішні системи винесено за межу системи; прямі виклики з доменних сервісів заборонено, доступ лише через `Payment Adapter`, `Identity Adapter`, `Notification Service`.
2. Додано `API Gateway` з інтерфейсами `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`; сервісні інтерфейси `ILoanService`, `IMemberService`, `ICatalogService`, `IReservationService`, `IPaymentPort`, `IOAuthPort`, `INotificationPort`, `IEmailSender`.
3. Інтерфейси узгоджено з `spec.md` §4 (provided/required) і `task-5/component.puml`.
4. Додано аудит-нотатку про трасування компонентів до ER і UC.

Коміти: `b121dc8`, `c07fee9`.

## Обмеження сліду
Повний експорт чату не збережено; наведено узагальнення, а не дослівний журнал ітерацій. Окрім вербатимного запиту вище, дослівних промптів не збережено — цей файл не є повним журналом сесії.