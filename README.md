# Концептуальна модель даних бібліотеки

Система для обліку каталогу книг, читачів, видач, бронювань і штрафів (мульти-примірникова бібліотека).

## Артефакти

- `spec.md` - специфікація (сутності, вимоги, NFR, трасування).
- `DEFENSE.md` - індекс захисту (точка входу для рев'ю).
- `docs/adr/` - архітектурні рішення (MADR), `0001..0010`.
- `task-1/` … `task-5/` - ER, Use Cases, Sequence, Activity, Component.
- `ai/` - узагальнені описи запитів до AI та фідбек (дослівних промптів не збережено, див. `ai/README.md`).

## Специфікації (spec.md)

- [spec.md](spec.md) - коренева специфікація моделі даних.
- [task-1/spec.md](task-1/spec.md) - ER-модель.
- [task-2/spec.md](task-2/spec.md) - вимоги та Use Cases.
- [task-3/spec.md](task-3/spec.md) - взаємодія (sequence).
- [task-4/spec.md](task-4/spec.md) - бізнес-процес (activity).
- [task-5/spec.md](task-5/spec.md) - компонентна архітектура.

## Архітектурні рішення (ADR)

- [docs/adr/0001-separate-book-copy.md](docs/adr/0001-separate-book-copy.md) - 0001. Виділення сутності BOOK_COPY для обліку примірників
- [docs/adr/0002-book-author-associative.md](docs/adr/0002-book-author-associative.md) - 0002. Асоціативна сутність BOOK_AUTHOR з author_order
- [docs/adr/0003-user-generalization.md](docs/adr/0003-user-generalization.md) - 0003. Узагальнення USER → MEMBER / LIBRARIAN
- [docs/adr/0004-fine-on-overdue.md](docs/adr/0004-fine-on-overdue.md) - 0004. Штраф FINE як extend від «Повернути книгу»
- [docs/adr/0005-borrow-include-debts-check.md](docs/adr/0005-borrow-include-debts-check.md) - 0005. Use Case «Позичити книгу» з `<<include>>` перевірки боргів читача
- [docs/adr/0006-fine-as-extend-on-return.md](docs/adr/0006-fine-as-extend-on-return.md) - 0006. Уточнення ADR 0004: `Створити штраф` як `<<extend>>` від `Повернути книгу`
- [docs/adr/0007-sequence-per-scenario.md](docs/adr/0007-sequence-per-scenario.md) - 0007. Окрема sequence-діаграма на кожен сценарій Gherkin
- [docs/adr/0008-activity-fork-join-status.md](docs/adr/0008-activity-fork-join-status.md) - 0008. Activity-діаграма з fork/join для атомарного оновлення статусу
- [docs/adr/0009-modular-monolith-api-gateway-adapters-outbox.md](docs/adr/0009-modular-monolith-api-gateway-adapters-outbox.md) - 0009. Компонентна архітектура: модульний моноліт, API Gateway, адаптери, outbox
- [docs/adr/0010-outbox-after-commit-email.md](docs/adr/0010-outbox-after-commit-email.md) - 0010. Надсилання email після коміту через Transactional Outbox

## Діаграми

- [task-1/er.md](task-1/er.md) - ER-діаграма (Mermaid).
- [task-2/usecase.puml](task-2/usecase.puml) - Use Case (PlantUML).
- [task-3/sequence.md](task-3/sequence.md) - Sequence-діаграми.
- [task-4/activity.md](task-4/activity.md) - Activity-діаграма.
- [task-5/component.md](task-5/component.md) - Component (Mermaid).
- [task-5/component.puml](task-5/component.puml) - Component (PlantUML).

## Журнал AI

- [ai/README.md](ai/README.md)
- [ai/01-task1-er.md](ai/01-task1-er.md)
- [ai/02-task2-usecases.md](ai/02-task2-usecases.md)
- [ai/03-task3-sequence.md](ai/03-task3-sequence.md)
- [ai/04-task4-activity.md](ai/04-task4-activity.md)
- [ai/05-task5-component.md](ai/05-task5-component.md)

## Структура завдань

- `task-1/` - ER-модель даних: [er.md](task-1/er.md), [README](task-1/README.md), [DEFENSE](task-1/DEFENSE.md), [spec.md](task-1/spec.md).
- `task-2/` - вимоги та Use Cases: [usecase.puml](task-2/usecase.puml), [ears.md](task-2/ears.md), [scenarios.feature](task-2/scenarios.feature), [README](task-2/README.md), [DEFENSE](task-2/DEFENSE.md), [spec.md](task-2/spec.md).
- `task-3/` - Sequence-діаграми: [sequence.md](task-3/sequence.md), [README](task-3/README.md), [DEFENSE](task-3/DEFENSE.md), [spec.md](task-3/spec.md).
- `task-4/` - Activity-діаграма: [activity.md](task-4/activity.md), [README](task-4/README.md), [DEFENSE](task-4/DEFENSE.md), [spec.md](task-4/spec.md).
- `task-5/` - компонентна архітектура: [component.md](task-5/component.md), [component.puml](task-5/component.puml), [spec.md](task-5/spec.md), [README](task-5/README.md), [DEFENSE](task-5/DEFENSE.md).

## Рендеринг діаграм

Mermaid-блоки в Markdown рендеряться GitHub безпосередньо. PlantUML-файли (`.puml`) не рендеряться на GitHub автоматично; потрібен PlantUML-рендерер або розширення IDE; PNG не комітиться.

## Історія

Робота виконана ітераціями з аудитами та виправленнями. Деталі захисту - у [task-1/DEFENSE.md](task-1/DEFENSE.md), [task-2/DEFENSE.md](task-2/DEFENSE.md), [task-3/DEFENSE.md](task-3/DEFENSE.md), [task-4/DEFENSE.md](task-4/DEFENSE.md), [task-5/DEFENSE.md](task-5/DEFENSE.md). Артефакти: [spec.md](spec.md), [task-1/er.md](task-1/er.md), [task-2/usecase.puml](task-2/usecase.puml), [task-3/sequence.md](task-3/sequence.md), [task-4/activity.md](task-4/activity.md), [task-5/component.md](task-5/component.md).
