# Завдання 1 — ER-модель бібліотеки

## Мета
Спроєктувати концептуальну ER-модель даних бібліотеки в 3NF: сутності, атрибути, ключі (PK/UK/FK) і зв'язки з кардинальностями. Модель є основою всіх наступних завдань (Use Cases, Sequence, Activity, Component).

## Артефакти
- [er.md](er.md) — ER-діаграма (Mermaid `erDiagram`) і перелік складених PK.
- [spec.md](spec.md) — специфікація завдання 1 (сутності, атрибути, зв'язки).
- [../spec.md](../spec.md) — специфікація: сутності, атрибути, зв'язки, EARS (розд. 1–4).
- [../docs/adr/0001-separate-book-copy.md](../docs/adr/0001-separate-book-copy.md), [../docs/adr/0002-book-author-associative.md](../docs/adr/0002-book-author-associative.md), [../docs/adr/0003-user-generalization.md](../docs/adr/0003-user-generalization.md) — архітектурні рішення.
- [../ai/01-task1-er.md](../ai/01-task1-er.md) — узагальнений AI-слід (вербатимних промптів немає, див. [../ai/README.md](../ai/README.md)).

## Що покрито
- 13 сутностей: `BOOK`, `BOOK_COPY`, `AUTHOR`, `BOOK_AUTHOR`, `USER`, `MEMBER`, `LIBRARIAN`, `LOAN`, `RESERVATION`, `FINE`, `GENRE`, `BOOK_GENRE` (плюс самозв'язок `GENRE.parent_id`).
- UUID для всіх PK; UK: `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- Складені PK: `BOOK_AUTHOR(book_id, author_id)`, `BOOK_GENRE(book_id, genre_id)`.
- Узагальнення `USER → MEMBER/LIBRARIAN`; окремий `BOOK_COPY` для фізичних примірників; `BOOK_AUTHOR.author_order` для порядку авторів.
- 3NF: без обчислюваних атрибутів; підсумки рахуються запитами.

## Як переглянути
- ER-діаграма в `er.md` — Mermaid `erDiagram`; рендериться на GitHub і в IDE з підтримкою Mermaid без додаткових інструментів.
- ADR — Markdown, читаються як текст.
- Файли `.puml` у цьому завданні не використовуються (PlantUML з'являється в task-2).

## Перевірка
- Усі id — uuid; зв'язки й кардинальності узгоджені з `spec.md` §1–4.
- UK і FK позначені в `er.md` і `spec.md` §2.
- Історія змін: `0692af9` (початкова модель) → `b33a62c` (нормалізація) → `a963654`/`d667463` (ADR) → `4a75f68`/`dd377c6` (виправлення `BOOK_GENRE`/UK/composite PK); фінальний аудит `69dba47`.
- Усі SHA перевірено через `git cat-file -e`.