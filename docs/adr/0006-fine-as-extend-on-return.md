# 0006. Уточнення ADR 0004: `Створити штраф` як `<<extend>>` від `Повернути книгу`

* Статус: прийнято
* Дата: 2026-11-18
* Контекст: Завдання 2 (`task-2/`), уточнення `docs/adr/0004-fine-on-overdue.md`

## Context and Problem Statement

ADR 0004 вже визначив, що `FINE` створюється лише при поверненні простроченої видачі. Цей ADR не змінює того рішення, а уточнює його місце в Use Case моделі: `Створити штраф` є `<<extend>>` до `Повернути книгу`, що виконується лише за умови `due_at < returned_at`.

## Decision Drivers

* Уникнення дублювання рішення ADR 0004.
* Явна умовність створення `FINE` в UC-діаграмі.
* Трасування до EARS-06 і sequence/activity.
* Ідемпотентність: один `FINE` на одну прострочену `LOAN`.

## Considered Options

* **Варіант 1:** створювати `FINE` завжди при поверненні (без extend).
* **Варіант 2:** створювати `FINE` за умови прострочення як `<<extend>>` (обрано, узгоджено з ADR 0004).
* **Варіант 3:** окремий UC «Обробити прострочення» поза поверненням.

## Decision Outcome

Обрано **Варіант 2**, узгоджено з ADR 0004. `Створити штраф` розширює `Повернути книгу` лише якщо `returned_at > due_at`. Штраф прив'язаний до `LOAN` (ADR 0004), створюється один раз, не дублюється при повторних викликах повернення.

### Consequences

* Good: UC-діаграма явно показує умовність створення штрафу.
* Good: не створюються зайві `FINE` при вчасному поверненні.
* Good: узгоджено з sequence-діаграмою 5 і Activity-діаграмою (S10/S11).
* Bad: потрібна перевірка `due_at` vs `returned_at` у `Loan Service`.
* Bad: повторне повернення тієї самої видачі має бути ідемпотентним (перевірка статусу `LOAN`).

## Links

* `docs/adr/0004-fine-on-overdue.md` (базове рішення, не змінюється)
* `task-2/usecase.puml`, `task-2/ears.md` (EARS-06)
* `task-3/sequence.md` (діаграма 5), `task-4/activity.md`
* `docs/adr/0009-modular-monolith-api-gateway-adapters-outbox.md`
```<|end|><|start|><|start|>assistant to=write<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="path">
C:\Users\arosl\OneDrive\Рабочий стол\Library-Modeling-and-Analysis-of-Software-Requirements\docs\adr\0007-sequence-per-scenario.md