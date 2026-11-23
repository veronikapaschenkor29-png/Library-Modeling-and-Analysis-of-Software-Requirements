# DEFENSE — Завдання 1: ER-модель бібліотеки

## 1. Що я просив у AI
Спроєктувати ER-модель бібліотеки з 3NF, UUID PK, окремим `BOOK_COPY`, асоціативним `BOOK_AUTHOR`, узагальненням `USER` і жанрами. Промпти: `ai/01-task1-er.md`.

## 2. Топ-3 розбіжності, які я знайшов і виправив (spec ↔ вихід AI)
| # | Що було не так (проти якого пункту spec) | Мій слід (промпт / фідбек / дод. spec) | Виправлення (коміт) |
|---|---|---|---|
| 1 | Відсутній зв'язок `BOOK↔GENRE` і UK-обмеження (`spec.md` §1–3) | `ai/01-task1-er.md` · фідбек #4–5 · `spec.md` §1–3 | `4a75f68` |
| 2 | Відсутні `inventory_number UK` і явний composite PK для `BOOK_GENRE` (`spec.md` §1–2) | `ai/01-task1-er.md` · аудит ER · `spec.md` §1–2 | `dd377c6` |
| 3 | Композитний PK для `BOOK_AUTHOR` не був зафіксований у моделі (`spec.md` §1–2) | `ai/01-task1-er.md` · фідбек #1 · `spec.md` §1–2 | `dd377c6` |

## 3. Ключове рішення
`BOOK_COPY` окремо від `BOOK`, щоб обліковувати стан і статус кожного примірника; `USER` з узагальненням `MEMBER/LIBRARIAN`, щоб розділити автентифікацію і профілі ролей (`docs/adr/0001-separate-book-copy.md`, `docs/adr/0003-user-generalization.md`).

## 4. Перевірка
- ERD у `task-1/er.md` звірено з `spec.md`: сутності, атрибути, PK/FK/UK.
- Унікальні ключі позначено: `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- Зв'язок `BOOK↔GENRE` реалізовано через `BOOK_GENRE`; composite PK зафіксовано в `spec.md`.