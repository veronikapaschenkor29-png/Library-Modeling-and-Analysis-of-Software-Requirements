# Концептуальна модель даних бібліотеки

Система для обліку каталогу книг, читачів, видач, бронювань і штрафів (мульти-примірникова бібліотека).

## Артефакти

- `spec.md` — специфікація (сутності, вимоги, NFR, трасування).
- `DEFENSE.md` — індекс захисту (точка входу для рев'ю).
- `docs/adr/` — архітектурні рішення (MADR).
- `task-1/` … `task-5/` — ER, Use Cases, Sequence, Activity, Component.
- `ai/` — узагальнені описи запитів до AI та фідбек (дослівних промптів не збережено, див. `ai/README.md`).

## Структура завдань

- `task-1/` — ER-модель даних: [er.md](task-1/er.md), [README](task-1/README.md), [DEFENSE](task-1/DEFENSE.md).
- `task-2/` — вимоги та Use Cases: [usecase.puml](task-2/usecase.puml), [ears.md](task-2/ears.md), [scenarios.feature](task-2/scenarios.feature), [README](task-2/README.md), [DEFENSE](task-2/DEFENSE.md).
- `task-3/` — Sequence-діаграми: [sequence.md](task-3/sequence.md), [README](task-3/README.md), [DEFENSE](task-3/DEFENSE.md).
- `task-4/` — Activity-діаграма: [activity.md](task-4/activity.md), [README](task-4/README.md), [DEFENSE](task-4/DEFENSE.md).
- `task-5/` — компонентна архітектура: [component.md](task-5/component.md), [component.puml](task-5/component.puml), [spec.md](task-5/spec.md), [README](task-5/README.md), [DEFENSE](task-5/DEFENSE.md).

## Діаграми

Mermaid-блоки в Markdown рендеряться GitHub безпосередньо. PlantUML-файли (`.puml`) не рендеряться на GitHub автоматично; потрібен PlantUML-рендерер або розширення IDE; PNG не комітиться.

## Історія

Робота виконана ітераціями з аудитами та виправленнями. Деталі захисту — у [task-1/DEFENSE.md](task-1/DEFENSE.md), [task-2/DEFENSE.md](task-2/DEFENSE.md), [task-3/DEFENSE.md](task-3/DEFENSE.md), [task-4/DEFENSE.md](task-4/DEFENSE.md), [task-5/DEFENSE.md](task-5/DEFENSE.md). Артефакти: [spec.md](spec.md), [task-1/er.md](task-1/er.md), [task-2/usecase.puml](task-2/usecase.puml), [task-3/sequence.md](task-3/sequence.md), [task-4/activity.md](task-4/activity.md), [task-5/component.md](task-5/component.md).