# 0008. Activity-діаграма з fork/join для атомарного оновлення статусу

* Статус: прийнято
* Дата: 2026-11-18
* Контекст: Завдання 4 (`task-4/`)

## Context and Problem Statement

Процес видачі вимагає паралельно оновити статус примірника (`BOOK_COPY.status`) і надіслати повідомлення «книгу видано», а при поверненні - паралельно перевірити прострочення і надіслати повідомлення. Потрібно відобразити атомарність DB-оновлення статусу та паралельні гілки.

## Decision Drivers

* Атомарність оновлення `BOOK_COPY.status` і `LOAN` в одній транзакції.
* Наочність паралельних гілок (fork/join).
* Рендер у GitHub (Mermaid) без зовнішніх серверів.
* Узгодженість з sequence-діаграмами.

## Considered Options

* **Варіант 1:** лінійна Activity без fork/join (послідовно).
* **Варіант 2:** машина станів (`stateDiagram-v2`).
* **Варіант 3:** лінійна Activity на базі `flowchart TB` з умовними `((fork))`/`((join))` (обрано).

## Decision Outcome

Обрано **Варіант 3**. `F1` паралельно виконує оновлення статусу примірника (S4) і надсилання email «книгу видано» (S5), з `join` J1; `F2` паралельно перевіряє прострочення (S10/S11/S13) і надсилає повідомлення про повернення (S12), з `join` J2. Оновлення статусу примірника і запис `LOAN` виконуються в одній БД-транзакції через `Data Access`, незалежно від порядку гілок fork.

### Consequences

* Good: атомарність DB-оновлення збережена; паралельні гілки не порушують транзакцію.
* Good: діаграма рендериться в GitHub.
* Bad: fork/join позначено умовними круглими вузлами, а не класичними UML-бар'єрами.
* Bad: розмежування ролей лише текстом у вузлах, без swimlanes-доріжок.

## Links

* `task-4/README.md`, `task-4/activity.md`
* `docs/adr/0004-fine-on-overdue.md`
* `docs/adr/0010-outbox-after-commit-email.md`
```<|end|><|start|><|start|>assistant to=write<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="path">
C:\Users\arosl\OneDrive\Рабочий стол\Library-Modeling-and-Analysis-of-Software-Requirements\docs\adr\0009-modular-monolith-api-gateway-adapters-outbox.md