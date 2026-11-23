# Специфікація архітектури: Component Diagram бібліотеки (Завдання 5)

## 1. Намір і критерії прийняття

Специфікація описує архітектуру бібліотеки у вигляді UML Component Diagram, узгоджену з ER-моделлю (Завдання 1), вимогами/UC/EARS (Завдання 2), sequence-діаграмами (Завдання 3) та Activity-діаграмою (Завдання 4).

**Критерії прийняття:**

1. **Ізоляція зовнішніх систем адаптерами:** `Payment`, `OAuth/Identity`, `Email/SMS` доступні лише через `Payment Adapter`, `Identity Adapter`, `Notification Service`; прямі виклики з доменних сервісів до зовнішніх API заборонені.
2. **Транзакційність видачі/повернення:** `BOOK_COPY.status` і `LOAN` (а при поверненні й `FINE`) змінюються в межах однієї БД-транзакції через `Data Access`.
3. **Узгодженість з процесом Завдання 4:** резервування при відсутності примірника, повідомлення «книгу видано», нагадування, повернення.
4. **Інтерфейси:** кожен зв'язок між компонентами виражено provided/required-інтерфейсом.
5. **Форма артефакту:** `component.puml` + `component.md`, обидва синтаксично валідні.

## 2. Компоненти та відповідальність

| Компонент | Сутності ER | UC / EARS (task-2) | Sequence (task-3) | Activity (task-4) |
|---|---|---|---|---|
| Web UI | — | Borrow, Return, Reserve (EARS-01–06) | Діагр. 1–5 | кроки читача |
| API Gateway | — | усі захищені UC (EARS-01–06) | усі | маршрутизація |
| Catalog Service | BOOK, BOOK_COPY, BOOK_AUTHOR, BOOK_GENRE | Borrow, Reserve (наявність) (EARS-01, 04, 05) | Діагр. 1, 2 | S2, S9 |
| Loan Service | LOAN, BOOK_COPY, FINE | Borrow, Return, CreateFine (EARS-01/06) | Діагр. 1–5 | S3, S4, S10, S11, S13 |
| Member Service | MEMBER | CheckDebts (EARS-03) | Діагр. 3, 4 | S1a, S1b |
| Reservation Service | RESERVATION, BOOK | Reserve (EARS-04) | Діагр. 2 | S9 |
| Notification Service | — | — | — | S5, S6, S12 |
| Data Access | усі сутності | усі (EARS-01–06) | виклики БД | — |
| Payment Adapter | FINE (оплата) | — | — | — |
| Identity Adapter | MEMBER | — | — | — |
| Database | усі сутності | — | — | — |

## 3. Зовнішні інтеграції

* `Payment` — через `Payment Adapter` (`IPaymentPort`).
* `Email/SMS` — через `Notification Service` (`IEmailSender`).
* `OAuth/Identity` — через `Identity Adapter` (`IOAuthPort`).

## 4. Інтерфейси (provided / required)

| Інтерфейс | Provided by | Required by |
|---|---|---|
| `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi` | API Gateway | Web UI |
| `ILoanService` | Loan Service | API Gateway |
| `IMemberService` | Member Service | API Gateway |
| `ICatalogService` | Catalog Service | API Gateway, Loan Service |
| `IReservationService` | Reservation Service | Loan Service, API Gateway |
| `IPaymentPort` | Payment Adapter | Loan Service |
| `IOAuthPort` | Identity Adapter | API Gateway |
| `INotificationPort` | Notification Service | Loan Service |
| `IEmailSender` | Email/SMS (зовнішній) | Notification Service |

## 5. Транзакції і події

* **Видача (`borrowBook`):** в одній транзакції — перевірка боргів/ліміту (Member Service), вибір доступного `BOOK_COPY`, створення `LOAN`, оновлення `BOOK_COPY.status` → «на руках»; блокування рядка примірника (`SELECT ... FOR UPDATE`) або унікальний статус для уникнення гонок.
* **Повернення (`returnBook`):** в одній транзакції — оновлення `LOAN` (`returned_at`, статус), створення `FINE` за прострочення (ADR 0004), оновлення `BOOK_COPY.status` → «доступний».
* **Події:** повернення/бронювання → outbox (Loan/Reservation Service) → Outbox Relay → Notification Service → Email/SMS (`sendLoanCreated`, `sendReminder`, повідомлення про повернення).
* **Overdue Scheduler:** періодично знаходить прострочені `LOAN` і створює `FINE` (ADR 0004), надсилає нагадування через outbox.

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