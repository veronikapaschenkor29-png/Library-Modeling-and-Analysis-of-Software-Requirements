# 0009. Компонентна архітектура: модульний моноліт, API Gateway, адаптери, outbox

* Статус: прийнято
* Дата: 2026-11-18
* Контекст: Завдання 5 (`task-5/`)

## Context and Problem Statement

Потрібно змоделювати архітектуру бібліотеки: компоненти, provided/required-інтерфейси, залежності та зовнішні інтеграції, узгоджені з ER-моделлю, EARS/UC, sequence- і Activity-діаграмами. Вибір архітектурного стилю визначає, як сервіси відповідають за сутності `BOOK`, `BOOK_COPY`, `MEMBER`, `LOAN`, `RESERVATION`, `FINE`.

## Decision Drivers

* Узгодженість із попередніми моделями (ERD, EARS, UC, sequence, activity).
* Чіткі межі відповідальності (SRP) через інтерфейси.
* Ізоляція зовнішніх систем (Email/SMS, Payment, OAuth) адаптерами.
* Атомарність транзакцій borrow/return.
* Рендер у GitHub (Mermaid) і PlantUML.

## Considered Options

* **Варіант 1:** моноліт із внутрішніми модулями.
* **Варіант 2:** мікросервіси (окремі процеси/контейнери).
* **Варіант 3:** модульний моноліт із чіткими компонентами, інтерфейсами, API Gateway і адаптерами (обрано).

## Decision Outcome

Обрано **Варіант 3**. Архітектура - модульний моноліт. `API Gateway` є єдиною точкою входу. Доменні сервіси (`Catalog`, `Loan`, `Member`, `Reservation`, `Auth`, `Notification`) працюють через інтерфейси. Зовнішні системи доступні лише через `Payment Adapter`, `Identity Adapter`, `Notification Service`. Події (повернення/бронювання) передаються через `IOutbox` (Data Access) і ретранслюються `Outbox Relay Worker`. `Overdue Scheduler` створює `FINE` за прострочені `LOAN`.

Компоненти: `Web UI`, `API Gateway`, `Auth Service`, `Catalog Service`, `Loan Service`, `Member Service`, `Reservation Service`, `Notification Service`, `Data Access`, `Payment Adapter`, `Identity Adapter`, `Outbox Relay Worker`, `Overdue Scheduler`, `БД`.

### Consequences

* Good: межі компонентів зафіксовані інтерфейсами; зовнішні залежності ізольовані; транзакційність зберігається в межах одного процесу/БД.
* Good: архітектура узгоджується з ERD, EARS, UC, sequence- і Activity-діаграмами.
* Bad: компоненти не розгортаються незалежно (єдиний процес); потрібна дисципліна, щоб не обходити інтерфейси.
* Bad: при зростанні навантаження може знадобитися винесення `Notification Service` і адаптерів в окремі процеси.

## Links

* `task-5/README.md`, `task-5/spec.md`, `task-5/component.md`
* `docs/adr/0004-fine-on-overdue.md`
* `docs/adr/0010-outbox-after-commit-email.md`
```<|end|><|start|><|start|>assistant to=write<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="path">
C:\Users\arosl\OneDrive\Рабочий стол\Library-Modeling-and-Analysis-of-Software-Requirements\docs\adr\0010-outbox-after-commit-email.md