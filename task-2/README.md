# Завдання 2 — Use Cases і вимоги бібліотеки

## Мета
Описати функціональні вимоги (EARS), Use Case модель, Gherkin-сценарії, акторів, NFR і трасування для бібліотеки, узгоджені з ER-моделлю Завдання 1.

## Артефакти
- [ears.md](ears.md) — EARS-01..06 (вимоги до позичення/повернення).
- [usecase.puml](usecase.puml) — UML Use Case діаграма (PlantUML).
- [scenarios.feature](scenarios.feature) — Gherkin-сценарії.
- [spec.md](spec.md) — специфікація завдання 2 (вимоги, NFR, трасування).
- [../spec.md](../spec.md) — специфікація (розд. 5–10, трасування).
- [../docs/adr/0001-separate-book-copy.md](../docs/adr/0001-separate-book-copy.md), [../docs/adr/0002-book-author-associative.md](../docs/adr/0002-book-author-associative.md), [../docs/adr/0003-user-generalization.md](../docs/adr/0003-user-generalization.md), [../docs/adr/0004-fine-on-overdue.md](../docs/adr/0004-fine-on-overdue.md), [../docs/adr/0005-borrow-include-debts-check.md](../docs/adr/0005-borrow-include-debts-check.md), [../docs/adr/0006-fine-as-extend-on-return.md](../docs/adr/0006-fine-as-extend-on-return.md) — архітектурні рішення.
- [../ai/02-task2-usecases.md](../ai/02-task2-usecases.md) — узагальнений AI-слід (вербатимних промптів немає, див. [../ai/README.md](../ai/README.md)).

## Що покрито
- Актори: `Читач` (Reader), `Бібліотекар` (Librarian); зовнішні сервіси не є акторами.
- EARS-01..06: видача з терміном 14 днів, відмова при прострочених боргах, ліміт 5 книг, бронювання, блокування примірника «на руках», штраф при поверненні простроченої книги.
- Use Cases: `Позичити книгу` (include `Перевірити борги читача`), `Повернути книгу`, `Створити штраф` (extend), `Забронювати книгу` (extend).
- Gherkin: успішна видача, відсутність примірників → бронювання, прострочені борги → відмова, ліміт 5 → відмова, повернення простроченої → `FINE`, блокування примірника.
- NFR-01..04 і трасування EARS ↔ UC ↔ сценарії (`spec.md` §7, §9–10).

## Як переглянути
- `ears.md`, `scenarios.feature`, `spec.md` — Markdown/Gherkin, читаються як текст на GitHub.
- `usecase.puml` — PlantUML; потребує PlantUML-рендерера або розширення IDE. Mermaid-блоки рендеряться GitHub безпосередньо, а файли `.puml` — ні.
- UC-діаграма подана лише як PlantUML (`usecase.puml`); Mermaid-нотація не відтворює генералізацію акторів коректно.

## Перевірка
- EARS-01..06 узгоджені з `usecase.puml` і `scenarios.feature`.
- `extend` для «Створити штраф» і «Забронювати книгу», `include` для перевірки боргів.
- Акторами залишено лише Читача і Бібліотекаря (коміт `5c848f3`).
- Історія змін: `1b170bf` (актори/UC/сценарії) → `bea0583` (NFR/ADR-0004) → `5c848f3` (актори) → `e970ae3`/`4fe8de7` (EARS-05/06, напрямок extend, сценарії).
- Усі SHA перевірено через `git cat-file -e`.