# Специфікація ER-моделі: Бібліотека (Завдання 1)

## 1. Намір (Intent)
Спроєктувати концептуальну ER-модель бібліотеки в 3NF: сутності, атрибути, ключі (PK/UK/FK) і зв'язки з кардинальностями. Модель є основою всіх наступних завдань (Use Cases, Sequence, Activity, Component). Охоплено сутності: `BOOK`, `BOOK_COPY`, `BOOK_AUTHOR`, `BOOK_GENRE`, `AUTHOR`, `GENRE`, `USER`, `MEMBER`, `LIBRARIAN`, `LOAN`, `RESERVATION`, `FINE`.

## 2. Сутності та атрибути

### 2.1 Каталог
- **BOOK:** `id` (UUID, PK), `title`, `isbn` (UK), `year`, `language`.
- **BOOK_COPY:** `id` (UUID, PK), `book_id` (FK → BOOK.id), `inventory_number` (UK), `condition`, `status`.
- **AUTHOR:** `id` (UUID, PK), `name`.
- **BOOK_AUTHOR:** `book_id` (FK → BOOK.id), `author_id` (FK → AUTHOR.id), `author_order`; PK(`book_id`, `author_id`).
- **GENRE:** `id` (UUID, PK), `parent_id` (FK → GENRE.id, Optional), `name`, `slug` (UK).
- **BOOK_GENRE:** `book_id` (FK → BOOK.id), `genre_id` (FK → GENRE.id); PK(`book_id`, `genre_id`).

### 2.2 Користувачі
- **USER:** `id` (UUID, PK), `email` (UK), `password_hash`, `first_name`, `last_name`, `role`, `created_at`.
- **MEMBER:** `id` (UUID, PK), `user_id` (FK → USER.id), `status`.
- **LIBRARIAN:** `id` (UUID, PK), `user_id` (FK → USER.id), `employee_code`.

### 2.3 Видачі, бронювання, штрафи
- **LOAN:** `id` (UUID, PK), `copy_id` (FK → BOOK_COPY.id), `member_id` (FK → MEMBER.id), `issued_at`, `due_at`, `returned_at`.
- **RESERVATION:** `id` (UUID, PK), `book_id` (FK → BOOK.id), `member_id` (FK → MEMBER.id), `created_at`, `status`.
- **FINE:** `id` (UUID, PK), `loan_id` (FK → LOAN.id), `amount`, `reason`, `paid`.

## 3. Ключі (PK / UK / FK)
- **PK:** усі сутності мають `UUID`; складені PK: `BOOK_AUTHOR(book_id, author_id)`, `BOOK_GENRE(book_id, genre_id)`.
- **UK:** `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- **FK:** `BOOK_COPY.book_id` → `BOOK.id`; `BOOK_AUTHOR.book_id` → `BOOK.id`, `BOOK_AUTHOR.author_id` → `AUTHOR.id`; `BOOK_GENRE.book_id` → `BOOK.id`, `BOOK_GENRE.genre_id` → `GENRE.id`; `GENRE.parent_id` → `GENRE.id` (Optional); `MEMBER.user_id` → `USER.id`; `LIBRARIAN.user_id` → `USER.id`; `LOAN.copy_id` → `BOOK_COPY.id`, `LOAN.member_id` → `MEMBER.id`; `RESERVATION.book_id` → `BOOK.id`, `RESERVATION.member_id` → `MEMBER.id`; `FINE.loan_id` → `LOAN.id`.

## 4. Зв'язки та кардинальності
| Зв'язок | Кардинальність | Опис |
| :--- | :--- | :--- |
| `BOOK` — `BOOK_COPY` | 1 : 0..N | один бібліографічний запис має багато примірників |
| `BOOK` — `BOOK_AUTHOR` | 1 : 0..N | книга має багато авторів (через асоціативну сутність) |
| `AUTHOR` — `BOOK_AUTHOR` | 1 : 0..N | автор може писати багато книг |
| `BOOK_COPY` — `LOAN` | 1 : 0..N | примірник може бути виданий багато разів (історія) |
| `MEMBER` — `LOAN` | 1 : 0..N | читач може мати багато видач |
| `MEMBER` — `RESERVATION` | 1 : 0..N | читач може мати багато бронювань |
| `BOOK` — `RESERVATION` | 1 : 0..N | книга може бути заброньована багато разів |
| `LOAN` — `FINE` | 1 : 0..N | видача може мати штрафи |
| `GENRE` — `GENRE` | 1 : 0..N | ієрархія жанрів (parent_id) |
| `BOOK` — `BOOK_GENRE` | 1 : 0..N | M:N книга ↔ жанр через асоціативну сутність |
| `GENRE` — `BOOK_GENRE` | 1 : 0..N | M:N жанр ↔ книга |
| `USER` — `MEMBER` | 1 : 0..1 | узагальнення: користувач може бути читачем |
| `USER` — `LIBRARIAN` | 1 : 0..1 | узагальнення: користувач може бути бібліотекарем |

## 5. Нормалізація
- 3NF: без дублювання й транзитивних залежностей; обчислювані агрегати (сума штрафу, кількість позичених книг) не зберігаються.
- `BOOK_COPY` окремо від `BOOK` (ADR 0001).
- `BOOK_AUTHOR` асоціативна з `author_order` (ADR 0002).
- `USER` окремо від `MEMBER`/`LIBRARIAN` (ADR 0003).

## 6. Посилання
- ER-діаграма: `er.md`.
- Архітектурні рішення: `../docs/adr/0001-separate-book-copy.md`, `../docs/adr/0002-book-author-associative.md`, `../docs/adr/0003-user-generalization.md`.
- Загальна специфікація: `../spec.md` (розд. 1–4).
