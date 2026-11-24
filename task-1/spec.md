# Специфікація ER-моделі: Бібліотека (Завдання 1)

## 1. Намір (Intent)
Спроєктувати концептуальну ER-модель бібліотеки в 3NF: сутності, атрибути, ключі (PK/UK/FK) і зв'язки з кардинальностями. Модель є основою всіх наступних завдань (Use Cases, Sequence, Activity, Component). Охоплено сутності: `BOOK`, `BOOK_COPY`, `AUTHOR`, `BOOK_AUTHOR`, `GENRE`, `USER`, `MEMBER`, `LIBRARIAN`, `LOAN`, `RESERVATION`, `FINE`. Зв'язок `BOOK ↔ GENRE` — M:N прямий, без сутності `BOOK_GENRE` (методика, правило 9).

## 2. Сутності та атрибути

### 2.1 Каталог
- **BOOK:** `id` (string, PK), `title`, `isbn` (UK), `year` (int), `language`.
- **BOOK_COPY:** `id` (string, PK), `book_id` (FK → BOOK.id), `inventory_number` (UK), `condition`, `status`.
- **AUTHOR:** `id` (string, PK), `name`.
- **BOOK_AUTHOR:** `book_id` (PK, FK → BOOK.id), `author_id` (PK, FK → AUTHOR.id), `author_order`.
- **GENRE:** `id` (string, PK), `parent_id` (FK → GENRE.id, Optional), `name`, `slug` (UK).

### 2.2 Користувачі
- **USER:** `id` (string, PK), `email` (UK), `password_hash`, `first_name`, `last_name`, `role`, `created_at` (datetime).
- **MEMBER:** `id` (string, PK), `user_id` (FK → USER.id), `status`.
- **LIBRARIAN:** `id` (string, PK), `user_id` (FK → USER.id), `employee_code`.

### 2.3 Видачі, бронювання, штрафи
- **LOAN:** `id` (string, PK), `copy_id` (FK → BOOK_COPY.id), `member_id` (FK → MEMBER.id), `issued_at` (datetime), `due_at` (datetime), `returned_at` (datetime).
- **RESERVATION:** `id` (string, PK), `book_id` (FK → BOOK.id), `member_id` (FK → MEMBER.id), `created_at` (datetime), `status`.
- **FINE:** `id` (string, PK), `loan_id` (FK → LOAN.id), `amount` (decimal), `reason`, `paid` (boolean).

## 3. Ключі (PK / UK / FK)
- **PK:** усі сутності мають одиничний ключ `id`; складений PK лише для `BOOK_AUTHOR(book_id, author_id)` — асоціативна сутність, зв'язок M:N несе атрибут `author_order` (методика 2.5.3: складені ключі рідкісні, але тут виправдані).
- **UK:** `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- **FK:** `BOOK_COPY.book_id` → `BOOK.id`; `BOOK_AUTHOR.book_id` → `BOOK.id`, `BOOK_AUTHOR.author_id` → `AUTHOR.id`; `GENRE.parent_id` → `GENRE.id` (Optional); `MEMBER.user_id` → `USER.id`; `LIBRARIAN.user_id` → `USER.id`; `LOAN.copy_id` → `BOOK_COPY.id`, `LOAN.member_id` → `MEMBER.id`; `RESERVATION.book_id` → `BOOK.id`, `RESERVATION.member_id` → `MEMBER.id`; `FINE.loan_id` → `LOAN.id`.

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
| `BOOK` — `GENRE` | M:N (0..N : 0..N) | класифікується (прямий зв'язок, без сполучної таблиці) |
| `USER` — `MEMBER` | 1 : 0..1 | узагальнення: користувач може бути читачем |
| `USER` — `LIBRARIAN` | 1 : 0..1 | узагальнення: користувач може бути бібліотекарем |

## 5. Нормалізація
- 3NF: без дублювання й транзитивних залежностей; обчислювані агрегати (сума штрафу, кількість позичених книг) не зберігаються; похідні поля не зберігаються (методика, правило 5).
- `BOOK_COPY` окремо від `BOOK` (ADR 0001).
- `BOOK_AUTHOR` асоціативна з `author_order` (ADR 0002).
- `USER` окремо від `MEMBER`/`LIBRARIAN` (ADR 0003).
- `BOOK`–`GENRE` M:N без сполучної таблиці (методика, правило 9).

## 6. Посилання
- ER-діаграма: `er.md`.
- Архітектурні рішення: `../docs/adr/0001-separate-book-copy.md`, `../docs/adr/0002-book-author-associative.md`, `../docs/adr/0003-user-generalization.md`.
- Загальна специфікація: `../spec.md` (розд. 1–4).

## 7. Відомі обмеження
- Концептуальна модель використовує сурогатні ідентифікатори (`id`) як єдиний атрибут ключа; тип (`string`/UUID) є зручністю моделі і не визначається методикою (опційно).
- Зв'язок `BOOK`–`GENRE` M:N показано прямим зв'язком без сполучної таблиці відповідно до правила 9; на фізичному рівні він реалізується сполучною таблицею, але в концептуальній моделі вона свідомо відсутня.
- `GENRE.parent_id` може бути відсутнім (опціональний FK) для кореневих жанрів.
