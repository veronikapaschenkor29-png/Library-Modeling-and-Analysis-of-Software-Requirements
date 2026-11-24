# Завдання 5: журнал взаємодії з AI (шлях «З AI»)

## Збережені дослівно (ai/README.md)
- п. 2 - запит, що стосується Завдання 5:
  > "продовжити з коміту 12: додати task-5, ai/, переписати DEFENSE/spec під DoD, MADR для ADR, пушити на GitHub під Веронікою"

## Відновлено за описом (ai/05-task5-component.md) - не дослівно
- Запит на побудову компонентної діаграми бібліотеки.
- Запити на виправлення за фідбеком (ізоляція зовнішніх систем, API Gateway, узгодження інтерфейсів, аудит-нотатка).

## Ітерація 1: компонентна діаграма

**Запит (відновлено, не дослівно):** «Побудуй компонентну діаграму бібліотеки з Web UI, API Gateway, доменними сервісами, Data Access, адаптерами зовнішніх систем і БД.»

**Результат (ai/05-task5-component.md):** побудовано компонентну діаграму бібліотеки: Web UI, API Gateway, Catalog/Loan/Member/Reservation/Notification Service, Data Access, Payment Adapter, Identity Adapter, БД і зовнішні системи (Payment, OAuth/Identity, Email/SMS).

**Фідбек і виправлення:**
1. Зовнішні системи винесено за межу системи; прямі виклики з доменних сервісів заборонено, доступ лише через `Payment Adapter`, `Identity Adapter`, `Notification Service`.
2. Додано `API Gateway` з інтерфейсами `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`; сервісні інтерфейси `ILoanService`, `IMemberService`, `ICatalogService`, `IReservationService`, `IPaymentPort`, `IOAuthPort`, `INotificationPort`, `IEmailSender`.
3. Інтерфейси узгоджено з `spec.md` §4 (provided/required) і `task-5/component.puml`.
4. Додано аудит-нотатку про трасування компонентів до ER і UC.

**Коміти:** `b121dc8`, `c07fee9`.

## Обмеження сліду
Повний експорт чату не збережено; наведено узагальнення, а не дослівний журнал ітерацій. Окрім вербатимного запиту вище, дослівних промптів не збережено - цей файл не є повним журналом сесії.