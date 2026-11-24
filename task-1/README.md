# Завдання 1 - ER-модель бібліотеки

## §1. Артефакти

- [er.md](er.md) - ER-діаграма (Mermaid `erDiagram`, Crow's Foot): 11 сутностей, PK/UK/FK, кардинальності.
- [spec.md](spec.md) - специфікація: сутності, атрибути, ключі, зв'язки, нормалізація.
- [DEFENSE.md](DEFENSE.md) - захист: критерії, топ-3 розбіжності, перевірки.
- [../docs/adr/0001-separate-book-copy.md](../docs/adr/0001-separate-book-copy.md), [../docs/adr/0002-book-author-associative.md](../docs/adr/0002-book-author-associative.md), [../docs/adr/0003-user-generalization.md](../docs/adr/0003-user-generalization.md) - архітектурні рішення.

## §2. Сутності (11)

| Сутність | Ключові атрибути |
| :--- | :--- |
| `BOOK` | `id` PK, `title`, `isbn` UK, `year`, `language` |
| `BOOK_COPY` | `id` PK, `book_id` FK, `inventory_number` UK, `condition`, `status` |
| `AUTHOR` | `id` PK, `name` |
| `BOOK_AUTHOR` | `book_id` PK/FK, `author_id` PK/FK, `author_order` (асоціативна) |
| `GENRE` | `id` PK, `parent_id` FK (self), `name`, `slug` UK |
| `USER` | `id` PK, `email` UK, `password_hash`, `first_name`, `last_name`, `role`, `created_at` |
| `MEMBER` | `id` PK, `user_id` FK, `status` |
| `LIBRARIAN` | `id` PK, `user_id` FK, `employee_code` |
| `LOAN` | `id` PK, `copy_id` FK, `member_id` FK, `issued_at`, `due_at`, `returned_at` |
| `RESERVATION` | `id` PK, `book_id` FK, `member_id` FK, `created_at`, `status` |
| `FINE` | `id` PK, `loan_id` FK, `amount`, `reason`, `paid` |

- `BOOK_GENRE` не є сутністю: зв'язок `BOOK ↔ GENRE` - M:N прямий (методика, правило 9).
- Складений PK лише у `BOOK_AUTHOR(book_id, author_id)`; усі інші сутності мають одиничний `id`.

## §3. Правила моделювання (методика)

- Концептуальна модель: без SQL DDL/ORM і фізичної схеми БД (правило 14, розд. 2.11).
- Похідні поля не зберігаються (правило 5): напр., сума штрафу обчислюється запитом, не полем.
- UK: `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- Асоціативна сутність лише з власними атрибутами (правило 10): `BOOK_AUTHOR` має `author_order`; інакше M:N зображується прямим зв'язком.
- Чистий M:N без сполучної таблиці (правило 9): `BOOK ↔ GENRE`.
- Сурогатні `id` для PK - зручність моделі; тип (string/UUID) методикою не визначається (опційно).

## §4. Як перевірити

- Відкрити [er.md](er.md) у рендері Mermaid (GitHub рендерить `erDiagram`).
- Перевірити grep-ом у `task-1/er.md` відсутність блоку сутності для зв'язку `BOOK–GENRE` (див. §2).
- Перевірити відсутність SQL DDL (`CREATE TABLE`, `CREATE INDEX`, `ALTER TABLE`) у `task-1/er.md`.
- Узгодженість атрибутів, ключів і кардинальностей - `spec.md` §2–4 ↔ `er.md`.

## §5. Обмеження та відкриті питання (spec.md §7)

- Концептуальна модель використовує сурогатні ідентифікатори (`id`) як єдиний атрибут ключа; тип (`string`/UUID) - зручність моделі, не визначається методикою (опційно).
- Зв'язок `BOOK–GENRE` M:N показано прямим зв'язком без сполучної таблиці; на фізичному рівні реалізується сполучною таблицею, але в концептуальній моделі свідомо відсутня.
- `GENRE.parent_id` може бути відсутнім (опціональний FK) для кореневих жанрів.
