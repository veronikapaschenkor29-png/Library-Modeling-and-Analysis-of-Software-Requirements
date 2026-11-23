# DEFENSE — Завдання 5: Компонентна архітектура бібліотеки

## 1. Що я просив у AI
Змоделювати компонентну архітектуру бібліотеки: компоненти, provided/required-інтерфейси, ізоляцію зовнішніх систем, узгодженість з ER/UC/EARS/sequence/activity. Промпти: `ai/05-task5-component.md`.

## 2. Топ-3 розбіжності, які я знайшов і виправив (spec ↔ вихід AI)

| # | Що було не так | Слід | Виправлення (коміт) |
|---|---|---|---|
| 1 | Зовнішні системи (Payment, OAuth/Identity, Email/SMS) викликались напряму з доменних сервісів/Web UI | `ai/05-task5-component.md` · фідбек #1 · `task-5/component.md` (до b121dc8) | Ізоляція через адаптери: `Payment Adapter`, `Identity Adapter`, `Notification Service` (`b121dc8`) |
| 2 | Відсутній API Gateway: Web UI звертався до сервісів напряму (`ILoanService` надавався Loan Service прямо Web UI) | `ai/05-task5-component.md` · фідбек #2 | Додано `API Gateway` як єдину точку входу з інтерфейсами `I*Api` (`b121dc8`) |
| 3 | Розбіжні назви інтерфейсів: `ILoanService`/`INotificationPort`/`IPaymentPort`/`IOAuthPort` без Api-рівня та без узгодження зі spec | `ai/05-task5-component.md` · `task-5/spec.md` §4 | Узгоджено набір: `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`, `ILoanService`, `IMemberService`, `ICatalogService`, `IReservationService`, `IPaymentPort`, `IOAuthPort`, `INotificationPort`, `IEmailSender` (`b121dc8`, `c07fee9`) |

## 3. Ключове рішення
Модульний моноліт: єдиний deployable, внутрішні модулі-сервіси (Catalog, Loan, Member, Reservation, Notification) з чіткими інтерфейсами; зовнішні системи (Payment, OAuth/Identity, Email/SMS) ізольовані адаптерами. Транзакції `borrowBook`/`returnBook` — в одній БД через Data Access; події — через outbox. Деталі: `task-5/spec.md`, `docs/adr/`.

## 4. Перевірка
- Узгодженість з моделями: кожен компонент трасується до ER/UC/EARS/sequence/activity (`task-5/spec.md` §2).
- Інтерфейси: provided/required узгоджені між `task-5/component.md`, `task-5/component.puml` і `spec.md` §4.
- Синтаксис: `component.puml` і `component.md` рендеряться без помилок.
- Аудит: зовнішні виклики ізольовані адаптерами; межа системи чітка (`spec.md` §1, §3).
- PR: https://github.com/veronikapaschenkor29-png/Library-Modeling-and-Analysis-of-Software-Requirements/pull/1
