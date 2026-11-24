# Специфікація архітектури: Component Diagram бібліотеки (Завдання 5)

## 1. Намір і критерії прийняття

Ця специфікація описує архітектуру бібліотеки у вигляді UML Component Diagram. Вона узгоджується з ER-моделлю (Завдання 1), вимогами та Use Cases (Завдання 2), sequence-діаграмами (Завдання 3) й Activity-діаграмою (Завдання 4). Артефакт має показати компоненти, їхні provided/required-інтерфейси, залежності між компонентами та зовнішні інтеграції.

**Критерії прийняття:**

1. **Покриття домену:** кожен компонент відповідає за підмножину сутностей ERD (`BOOK`, `BOOK_COPY`, `BOOK_AUTHOR`, `BOOK_GENRE`, `AUTHOR`, `GENRE`, `USER`, `MEMBER`, `LIBRARIAN`, `LOAN`, `RESERVATION`, `FINE`) і/або Use Cases (`Borrow`, `Return`, `Reserve`, `CheckDebts`, `CreateFine`).
2. **Ізоляція зовнішніх систем адаптерами:** `Payment`, `OAuth/Identity`, `Email/SMS` доступні лише через `Payment Adapter`, `Identity Adapter`, `Notification Service`; прямі виклики з доменних сервісів до зовнішніх API заборонені.
3. **Транзакційність видачі/повернення:** `BOOK_COPY.status`, `LOAN` (а при поверненні й `FINE`) змінюються в межах однієї БД-транзакції через `Data Access`.
4. **Outbox для подій:** повернення/бронювання → outbox → Outbox Relay Worker → Notification Service → Email/SMS.
5. **Scheduler для прострочення:** Overdue Scheduler періодично знаходить прострочені `LOAN` і створює `FINE` (ADR 0004), надсилає нагадування через outbox.
6. **Інтерфейси:** для кожного зв'язку між компонентами вказано provided/required-інтерфейс, а не «виклик класу».
7. **Форма артефакту:** декларативний опис у PlantUML (`component.puml`) + рендер у Mermaid (`component.md`); обидва синтаксично валідні.

## 2. Компоненти та відповідальність

| Компонент | Сутності ER | UC / EARS (task-2) | Sequence (task-3) | Activity (task-4) |
|---|---|---|---|---|
| Web UI | — | Borrow, Return, Reserve (EARS-01–06) | Діагр. 1–5 | кроки читача |
| API Gateway | — | усі захищені UC (EARS-01–06) | усі | маршрутизація |
| Auth Service | `USER`, `LIBRARIAN` | — | — | — |
| Catalog Service | `BOOK`, `BOOK_COPY`, `BOOK_AUTHOR`, `BOOK_GENRE`, `AUTHOR`, `GENRE` | Borrow, Reserve (наявність) (EARS-01, 04, 05) | Діагр. 1, 2 | S2, S9 |
| Loan Service | `LOAN`, `BOOK_COPY`, `FINE` | Borrow, Return, CreateFine (EARS-01–03, 06) | Діагр. 1–5 | S3, S4, S10, S11, S13 |
| Member Service | `MEMBER` | CheckDebts (EARS-02, 03) | Діагр. 3, 4 | S1a, S1b |
| Reservation Service | `RESERVATION`, `BOOK` | Reserve (EARS-04) | Діагр. 2 | S9 |
| Notification Service | — | — | — | S5, S6, S12 |
| Data Access | усі сутності | усі (EARS-01–06) | виклики БД | — |
| Payment Adapter | `FINE` (оплата) | — | — | — |
| Identity Adapter | `MEMBER` | — | — | — |
| Outbox Relay Worker | — | — | — | — |
| Overdue Scheduler | `LOAN`, `FINE` | CreateFine (EARS-06) | — | — |
| Database | усі сутності | — | — | — |

## 3. Зовнішні інтеграції

* `Payment` (зовнішня) — через `Payment Adapter` (`IPaymentPort`).
* `Email/SMS` (зовнішня) — через `Notification Service` (`IEmailSender`).
* `OAuth/Identity` (зовнішня) — через `Identity Adapter` (`IOAuthPort`); `Auth Service` виконує автентифікацію за `USER.password_hash` і/або через `IOAuthPort`.

## 4. Інтерфейси (provided / required)

* `Web UI` requires: `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`.
* `API Gateway` provides: `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`; requires: `ILoanService`, `ICatalogService`, `IMemberService`, `IReservationService`, `IAuthService`.
* `Auth Service` provides: `IAuthService`; requires: `IOAuthPort` (через `Identity Adapter`), `Data Access`.
* `Catalog Service` provides: `ICatalogService`; requires: `Data Access`.
* `Loan Service` provides: `ILoanService`; requires: `IPaymentPort`, `INotificationPort`, `IReservationService`, `ICatalogService`, `IMemberService`, `IOutbox`.
* `Member Service` provides: `IMemberService`; requires: `Data Access`.
* `Reservation Service` provides: `IReservationService`; requires: `IOutbox`.
* `Notification Service` provides: `INotificationPort`; requires: `IEmailSender`.
* `Payment Adapter` provides: `IPaymentPort`; requires: зовнішній API `Payment`.
* `Identity Adapter` provides: `IOAuthPort`; requires: зовнішній API `OAuth/Identity`.
* `Data Access` provides: `IOutbox`; requires: `IDbConnection`.
* `Outbox Relay Worker` requires: `IOutbox`, `INotificationPort`.
* `Overdue Scheduler` requires: `ILoanService`.
* `Database` provides: `IDbConnection`.

## 5. Транзакції та події

* **Архітектурний стиль:** модульний моноліт, `API Gateway`, адаптери до зовнішніх систем, outbox (ADR 0009, `../docs/adr/0009-modular-monolith-api-gateway-adapters-outbox.md`).
* **Видача (`borrowBook`):** в одній транзакції — перевірка боргів/ліміту (Member Service), вибір доступного `BOOK_COPY`, створення `LOAN`, оновлення `BOOK_COPY.status` → «на руках»; блокування рядка примірника (`SELECT ... FOR UPDATE`) або унікальний статус для уникнення гонок.
* **Повернення (`returnBook`):** в одній транзакції — оновлення `LOAN` (`returned_at`, статус), створення `FINE` за прострочення (ADR 0004, `docs/adr/0004-fine-on-overdue.md`), оновлення `BOOK_COPY.status` → «доступний».
* **Події:** outbox-запис у тій самій транзакції, надсилання після коміту, збій надсилання не відкочує операцію (ADR 0010, `../docs/adr/0010-outbox-after-commit-email.md`): повернення/бронювання → outbox (Loan/Reservation Service) → Outbox Relay Worker → Notification Service → Email/SMS (`sendLoanCreated`, `sendReminder`, повідомлення про повернення).
* **Overdue Scheduler:** періодично знаходить прострочені `LOAN` і створює `FINE` (ADR 0004, `docs/adr/0004-fine-on-overdue.md`), надсилає нагадування через outbox.

## 6. Out of Scope

* Розгортання в Kubernetes / багатовузловий кластер.
* Окремий пошуковий рушій; пошук виконується в БД.
* Кешування (Redis) — може бути додано пізніше без зміни інтерфейсів.
* Адміністративна роль / backoffice.
* Оплата штрафів через зовнішній платіжний шлюз — може бути додана пізніше (Payment Adapter).

## 7. Критерії перевірки

1. Кожен компонент специфікації присутній на діаграмі (`component.puml`/`component.md`).
2. Зовнішні виклики ізольовані адаптерами.
3. Стани `BOOK_COPY`/`LOAN` і потік outbox відображені сервісами.
4. Діаграми рендеряться без синтаксичних помилок.
5. Транзакції `borrow`/`return` атомарні; паралельні видачі не призводять до дублювань/гонок.