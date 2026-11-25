# Специфікація моделі даних: Бібліотека

Намір: спроєктувати концептуальну ER-модель бібліотеки для обліку каталогу, примірників, читачів, видач, бронювань і штрафів.

## 1. Критерії прийняття
- **3NF:** без дублювання й транзитивних залежностей; обчислювані агрегати (сума штрафу, кількість позичених книг) не зберігаються.
- **PK:** усі сутності мають `UUID`. **UK:** `USER.email`, `BOOK.isbn`, `GENRE.slug`, `BOOK_COPY.inventory_number`.
- **Узагальнення:** `USER` окремо від `MEMBER` і `LIBRARIAN`.
- **Примірник:** `BOOK_COPY` окремо від `BOOK`.
- **Автори:** `BOOK_AUTHOR(book_id, author_id, author_order)`.
- **Жанри:** `BOOK ↔ GENRE` - M:N прямий (без сутності, правило 9); `GENRE.id` (UUID, PK), `parent_id` (FK, Optional), `name`, `slug` (UK).
- **Штраф:** `FINE` створюється лише при поверненні простроченої видачі (extend від «Повернути книгу»); нагадування штраф не створює. Повернення звільняє статус примірника («доступний»).
## 2. Сутності та атрибути
- **BOOK/BOOK_COPY:** `BOOK.id` (UUID, PK), `title`, `isbn` (UK), `year`, `language`; `BOOK_COPY.id` (UUID, PK), `book_id` (FK), `inventory_number` (UK), `condition`, `status`.
- **AUTHOR/BOOK_AUTHOR:** `AUTHOR.id` (UUID, PK), `name`; `BOOK_AUTHOR.book_id` (FK), `author_id` (FK), `author_order`, PK(`book_id`, `author_id`).
- **USER/MEMBER/LIBRARIAN:** `USER.id` (UUID, PK), `email` (UK), `password_hash`, `first_name`, `last_name`, `role`, `created_at`; `MEMBER.id` (UUID, PK), `user_id` (FK), `status`; `LIBRARIAN.id` (UUID, PK), `user_id` (FK), `employee_code`.
- **LOAN/RESERVATION:** `LOAN.id` (UUID, PK), `copy_id` (FK), `member_id` (FK), `issued_at`, `due_at`, `returned_at`; `RESERVATION.id` (UUID, PK), `book_id` (FK), `member_id` (FK), `created_at`, `status`.
- **FINE/GENRE:** `FINE.id` (UUID, PK), `loan_id` (FK), `amount`, `reason`, `paid`; `GENRE.id` (UUID, PK), `parent_id` (FK, Optional), `name`, `slug` (UK).
## 3. Зв'язки
`BOOK ||--o{ BOOK_COPY`; `BOOK ||--o{ BOOK_AUTHOR`; `AUTHOR ||--o{ BOOK_AUTHOR`; `BOOK_COPY ||--o{ LOAN`; `MEMBER ||--o{ LOAN`; `MEMBER ||--o{ RESERVATION`; `BOOK ||--o{ RESERVATION`; `LOAN ||--o{ FINE`; `GENRE ||--o{ GENRE`; `BOOK }o--o{ GENRE`; `USER ||--o| MEMBER`; `USER ||--o| LIBRARIAN`.
## 4. Актори
Читач і Бібліотекар (primary). Платіжний шлюз і email-сервіс не є акторами.
## 5. Вимоги EARS
- **EARS-01:** WHEN читач запитує видачу AND примірник доступний, THE SYSTEM SHALL створити видачу на 14 днів.
- **EARS-02:** IF читач має прострочені борги, THEN система SHALL відмовити у видачі.
- **EARS-03:** IF читач уже має 5 позичених книг, THEN система SHALL відмовити у видачі.
- **EARS-04:** WHEN примірника немає, THE SYSTEM SHALL запропонувати бронювання і створити `RESERVATION`.
- **EARS-05:** WHILE примірник має статус «на руках», система SHALL блокувати його видачу і бронювання іншим читачам.
- **EARS-06:** WHEN читач повертає прострочену книгу, THE SYSTEM SHALL створити `FINE` (extend від «Повернути книгу»).
## 6. Gherkin-сценарії
`Feature: Позичення і повернення книги (EARS-01..06)`; сценарії: успішна видача; книги немає → бронювання; прострочені борги → відмова; перевищено ліміт 5 книг → відмова; повернення простроченої книги → `FINE`; примірник на руках блокується.
## 7. NFR
**NFR-01:** відповідь ≤ 2 с для 95% запитів при 200 одночасних. **NFR-02:** веб-інтерфейс desktop і мобільних. **NFR-03:** мова UI - українська. **NFR-04:** паролі хешуються bcrypt/argon2.
## 8. Поза обсягом
Електронні книги, міжбібліотечний обмін, фізична логістика.
## 9. Матриця трасування
| Вимога | UC | Сценарій |
| :--- | :--- | :--- |
| EARS-01 | Позичити книгу | Успішна видача |
| EARS-02 | Позичити книгу | Прострочені борги → відмова |
| EARS-03 | Позичити книгу | Перевищено ліміт 5 книг → відмова |
| EARS-04 | Позичити книгу | Книги немає → бронювання |
| EARS-05 | Позичити книгу | Примірник на руках блокується |
| EARS-06 | Повернути книгу | Повернення простроченої книги → штраф |

## 10. Трасування діаграм і NFR
- Sequence: 4 сценарії видачі + повернення простроченої книги → `FINE`; учасник `Reservation Service` для бронювання.
- Activity: fork/join для видачі/статусу, повідомлення, нагадування, `FINE`.
- Component: Loan/Member/Catalog/Notification, Data Access, БД, Email/SMS, Payment, OAuth.
- NFR-01..04 перевіряються відповідними тестами продуктивності, сумісності, локалізації і безпеки.