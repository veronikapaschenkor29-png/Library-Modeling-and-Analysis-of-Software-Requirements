# 0007. Окрема sequence-діаграма на кожен сценарій Gherkin

* Статус: прийнято
* Дата: 2026-11-18
* Контекст: Завдання 3 (`task-3/`)

## Context and Problem Statement

Одна діаграма на весь UC «Позичити книгу» змішує 5 Gherkin-сценаріїв (успішна видача, бронювання, відмова через борги, ліміт 5, повернення зі штрафом) і перевантажується `alt`/`opt`/`loop`. Потрібно визначити правила розбиття діаграм.

## Decision Drivers

* Трасування кожного кроку до одного Gherkin-сценарію.
* Читабельність activation bars.
* Узгодженість Mermaid і PlantUML.
* Повне покриття EARS-01..06.

## Considered Options

* **Варіант 1:** одна діаграма на UC «Позичити книгу» з усіма гілками.
* **Варіант 2:** окрема діаграма на кожен із 5 Gherkin-сценаріїв (обрано).

## Decision Outcome

Обрано **Варіант 2**. У `task-3/sequence.md` п'ять окремих діаграм:

1. Успішна видача (EARS-01, EARS-05).
2. Примірника немає → бронювання (EARS-04, EARS-05).
3. Відмова через борги (EARS-02).
4. Перевищено ліміт 5 книг (EARS-03).
5. Повернення простроченої книги → `FINE` (EARS-06).

Учасники: `Читач`, `Web UI`, `Loan Service`, `Member Service`, `Catalog`, `Reservation Service`, `БД`.

### Consequences

* Good: кожна діаграма трасується до одного сценарію.
* Good: activation bars показують роботу сервісів у конкретній гілці.
* Bad: більше файлів-діаграм і потреба легенди для зв'язку між ними.
* Bad: спільні кроки (перевірка сесії) повторюються в кількох діаграмах.

## Links

* `task-3/README.md`, `task-3/sequence.md`
* `task-2/scenarios.feature`
* `docs/adr/0005-borrow-include-debts-check.md`
* `docs/adr/0006-fine-as-extend-on-return.md`
```<|end|><|start|><|start|>assistant to=write<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="path">
C:\Users\arosl\OneDrive\Рабочий стол\Library-Modeling-and-Analysis-of-Software-Requirements\docs\adr\0008-activity-fork-join-status.md