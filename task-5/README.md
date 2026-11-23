# Завдання 5 — Компонентна архітектура бібліотеки

Компонентна діаграма системи бібліотеки: компоненти, їхні provided/required-інтерфейси, залежності та зовнішні інтеграції, узгоджені з ER-моделлю (Завдання 1), вимогами/UC/EARS (Завдання 2), sequence-діаграмами (Завдання 3) та Activity-діаграмою (Завдання 4). Архітектурний стиль — модульний моноліт із чіткими інтерфейсами та адаптерами до зовнішніх систем; API Gateway — єдина точка входу.

## Зміст артефактів

- `component.puml` — повна UML Component Diagram у PlantUML (інтерфейси як льодяники/розетки, пакети за шарами).
- `component.md` — оглядова Mermaid-діаграма + таблиця provided/required-інтерфейсів (рендериться на GitHub).
- `spec.md` — специфікація: намір і критерії, компоненти та відповідальність, зовнішні інтеграції, інтерфейси, транзакції та події, out of scope, критерії перевірки.
- `DEFENSE.md` — захист: що просили у AI, топ-3 розбіжності (spec ↔ вихід AI), ключове рішення, перевірка.

## Як переглянути

- Mermaid-блоки у `component.md` і `spec.md` рендерить GitHub автоматично.
- PlantUML-файл `component.puml` **не рендериться на GitHub автоматично** (потрібен PlantUML-рендерер або розширення IDE). Варіанти перегляду: плагін PlantUML в IDE, онлайн-рендерер plantuml.com, локальний запуск `plantuml component.puml`.
- Текстовий опис діаграми: шари зверху вниз — **Клієнт** (`Web UI`) → **API** (`API Gateway`) → **Сервіси** (`Catalog Service`, `Loan Service`, `Member Service`, `Reservation Service`, `Notification Service`) → **Інфраструктура** (`Data Access`, `Payment Adapter`, `Identity Adapter`) → **Дані** (`БД`) та **Зовнішні системи** (`Email/SMS`, `Payment`, `OAuth/Identity`). Зв'язки підписані іменами інтерфейсів (див. таблицю нижче та `spec.md` §4).

## Компоненти

| Шар | Компоненти |
|---|---|
| Клієнт | `Web UI` |
| API | `API Gateway` |
| Сервіси | `Catalog Service`, `Loan Service`, `Member Service`, `Reservation Service`, `Notification Service` |
| Інфраструктура | `Data Access`, `Payment Adapter`, `Identity Adapter` |
| Дані | `БД (PostgreSQL)` |
| Зовнішні системи | `Email/SMS`, `Payment`, `OAuth/Identity` |

## Інтерфейси (provided / required)

| Інтерфейс | Provided by | Required by |
|---|---|---|
| `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi` | `API Gateway` | `Web UI` |
| `ILoanService` | `Loan Service` | `API Gateway` |
| `IMemberService` | `Member Service` | `API Gateway`, `Loan Service` |
| `ICatalogService` | `Catalog Service` | `API Gateway`, `Loan Service` |
| `IReservationService` | `Reservation Service` | `Loan Service`, `API Gateway` |
| `IPaymentPort` | `Payment Adapter` | `Loan Service` |
| `IOAuthPort` | `Identity Adapter` | `API Gateway` |
| `INotificationPort` | `Notification Service` | `Loan Service` |
| `IEmailSender` | `Email/SMS` (зовнішній) | `Notification Service` |

## Трасування до Завдань 1–4

- **Завдання 1 (ER):** компоненти зіставляються з сутностями `BOOK`, `BOOK_COPY`, `BOOK_AUTHOR`, `BOOK_GENRE`, `MEMBER`, `LOAN`, `RESERVATION`, `FINE` (таблиця у `spec.md` §2); доступ до БД — через `Data Access`.
- **Завдання 2 (Use Cases / EARS):** `borrowBook`, `returnBook`, `reserveBook` (EARS-01–06), перевірка боргів/ліміту, створення штрафу; межа системи та ізоляція зовнішніх систем.
- **Завдання 3 (Sequence):** діаграми 1–5 відображені викликами `Loan`/`Member`/`Catalog`/`Reservation`/`Notification` через інтерфейси з таблиці вище.
- **Завдання 4 (Activity):** потоки видачі/повернення/бронювання, fork/join, повідомлення «книгу видано», нагадування, повернення, обробка прострочення (ADR 0004).

## Перевірка

1. Кожен компонент специфікації присутній на діаграмах (`component.puml`/`component.md`).
2. Зовнішні виклики ізольовані адаптерами (`Payment Adapter`, `Identity Adapter`, `Notification Service`).
3. Стани `BOOK_COPY`/`LOAN` і потік outbox відображені сервісами.
4. Інтерфейси узгоджені між `component.md`, `component.puml` і `spec.md` §4.
5. Транзакції `borrow`/`return` атомарні; паралельні видачі не призводять до дублювань/гонок (`spec.md` §5).
6. Діаграми рендеряться без синтаксичних помилок (Mermaid на GitHub; PlantUML — локально/через рендерер).
7. Деталі захисту та топ-3 розбіжності — у `DEFENSE.md`.