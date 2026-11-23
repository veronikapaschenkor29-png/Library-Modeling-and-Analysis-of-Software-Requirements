# DEFENSE — Завдання 5: Компонентна архітектура бібліотеки

## 1. Що я просив у AI

AI (через OpenCode CLI) ставилася задача проаналізувати попередні артефакти (ERD, Завдання 1; вимоги/UC/EARS, Завдання 2; sequence-діаграми, Завдання 3; Activity-діаграма, Завдання 4) і запропонувати компонентну модель бібліотеки з provided/required-інтерфейсами та зовнішніми інтеграціями, а також згенерувати PlantUML-діаграму. Узагальнені запити, результати й фідбек — у `ai/05-task5-component.md`; дослівних промптів не збережено (див. `ai/README.md`). Усі рішення перевірено й остаточно зафіксовано автором самостійно.

## 2. Топ-3 розбіжності, які я знайшов і виправив (spec ↔ вихід AI)

| # | Що було не так (проти якого пункту spec) | Мій слід (промпт / фідбек / дод. spec) | Виправлення (коміт) |
|---|---|---|---|
| 1 | Зовнішні системи (`Payment`, `OAuth/Identity`, `Email/SMS`) викликались напряму з доменних сервісів/Web UI — суперечить критерію 1 spec §1 (ізоляція зовнішніх систем адаптерами) | `ai/05-task5-component.md` · фідбек #1; аудит `task-5/component.md` (до `b121dc8`) | Ізоляція через адаптери: `Payment Adapter`, `Identity Adapter`, `Notification Service` (коміт `b121dc8`) |
| 2 | Відсутній `API Gateway`: Web UI звертався до сервісів напряму (`ILoanService` надавався `Loan Service` прямо Web UI) — суперечить критерію 4 (інтерфейси) і межі системи | `ai/05-task5-component.md` · фідбек #2; `task-5/spec.md` §4 | Додано `API Gateway` як єдину точку входу з інтерфейсами `I*Api` (коміт `b121dc8`) |
| 3 | Розбіжні назви інтерфейсів: `ILoanService`/`INotificationPort`/`IPaymentPort`/`IOAuthPort` без Api-рівня та без узгодження зі spec — суперечить критерію 4 | `ai/05-task5-component.md` · `task-5/spec.md` §4 | Узгоджено набір: `ILoanApi`, `IMemberApi`, `ICatalogApi`, `IReservationApi`, `ILoanService`, `IMemberService`, `ICatalogService`, `IReservationService`, `IPaymentPort`, `IOAuthPort`, `INotificationPort`, `IEmailSender` (коміти `b121dc8`, `c07fee9`) |

SHA комітів перевірено командою `git cat-file -e` (exit 0): `b121dc8`, `c07fee9`.

## 3. Ключове рішення

Обрано **модульний моноліт із чіткими компонентами та інтерфейсами й адаптерами до зовнішніх систем** замість мікросервісів: єдиний deployable, внутрішні модулі-сервіси (`Catalog`, `Loan`, `Member`, `Reservation`, `Notification`) з чіткими інтерфейсами; зовнішні системи (`Payment`, `OAuth/Identity`, `Email/SMS`) ізольовані адаптерами (`Payment Adapter`, `Identity Adapter`, `Notification Service`); `API Gateway` — єдина точка входу. Збережено ACID-транзакційність і простоту розгортання, але зафіксовано межі компонентів, що дозволяє еволюційно переходити до розподіленої архітектури. Транзакції `borrowBook`/`returnBook` — в одній БД через `Data Access`; події — через outbox. Деталі — у `task-5/spec.md` і `docs/adr/`.

## 4. Перевірка

- **Узгодженість із попередніми моделями:** кожен компонент трасується до ER/UC/EARS/sequence/activity (`task-5/spec.md` §2, `task-5/README.md`); потік видачі/повернення, outbox і нагадування підтримано компонентами `Loan Service`, `Notification Service`, `Data Access`.
- **Інтерфейси:** усі зв'язки між компонентами підписано іменами provided/required-інтерфейсів (`ILoanService`, `IMemberService`, `IPaymentPort`, `IOAuthPort` тощо); зовнішні системи ізольовані адаптерами (`Payment Adapter`, `Identity Adapter`, `Notification Service`).
- **Транзакційність:** `BOOK_COPY.status` і `LOAN` (а при поверненні й `FINE`) змінюються в межах однієї БД-транзакції через `Data Access`; паралельні видачі не призводять до дублювань/гонок (`spec.md` §5).
- **Синтаксична перевірка:** PlantUML (`task-5/component.puml`) перевірено на коректність синтаксису; Mermaid-блок у `task-5/component.md` рендериться на GitHub.
- **Посилання на PR:** https://github.com/veronikapaschenkor29-png/Library-Modeling-and-Analysis-of-Software-Requirements/pull/1
- **Аудит:** топ-3 розбіжності виправлено через зміну специфікації/інтерфейсів, а не ручним патчем діаграми.