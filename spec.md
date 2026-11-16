# Специфікація моделі даних: Бібліотека

## 1. Критерії прийняття (Acceptance Criteria)
- **Нормалізація (3NF):** відсутність дублювання даних і транзитивних залежностей.
- **Ідентифікатори:** усі сутності мають первинний ключ типу `UUID`.
- **Розділення користувача та ролей:** обліковий запис `USER` відокремлено від профілів `MEMBER` і `LIBRARIAN` (узагальнення).
- **Окремий примірник:** `BOOK_COPY` виділено окремо від `BOOK` для обліку фізичних екземплярів.
- **Асоціативна сутність авторів:** `BOOK_AUTHOR` зберігає порядок авторів (`author_order`).
- **Штраф за прострочення:** `FINE` створюється при поверненні простроченої видачі (`extend` від «Повернути книгу»).

## 2. Сутності та атрибути
- **BOOK:** `id` (UUID, PK), `title` (String), `isbn` (String), `year` (Int), `language` (String).
- **BOOK_COPY:** `id` (UUID, PK), `book_id` (UUID, FK -> BOOK), `inventory_number` (String), `condition` (String), `status` (String).
- **AUTHOR:** `id` (UUID, PK), `name` (String).
- **BOOK_AUTHOR:** `book_id` (UUID, FK -> BOOK), `author_id` (UUID, FK -> AUTHOR), `author_order` (Int).
- **USER:** `id` (UUID, PK), `email` (String), `password_hash` (String), `first_name` (String), `last_name` (String), `role` (String), `created_at` (DateTime).
- **MEMBER:** `id` (UUID, PK), `user_id` (UUID, FK -> USER), `status` (String).
- **LIBRARIAN:** `id` (UUID, PK), `user_id` (UUID, FK -> USER), `employee_code` (String).
- **LOAN:** `id` (UUID, PK), `copy_id` (UUID, FK -> BOOK_COPY), `member_id` (UUID, FK -> MEMBER), `issued_at` (DateTime), `due_at` (DateTime), `returned_at` (DateTime).
- **RESERVATION:** `id` (UUID, PK), `book_id` (UUID, FK -> BOOK), `member_id` (UUID, FK -> MEMBER), `created_at` (DateTime), `status` (String).
- **FINE:** `id` (UUID, PK), `loan_id` (UUID, FK -> LOAN), `amount` (Decimal), `reason` (String), `paid` (Boolean).
- **GENRE:** `id` (UUID, PK), `parent_id` (UUID, FK -> GENRE, Optional), `name` (String), `slug` (String).

## 3. Зв'язки
- `BOOK ||--o{ BOOK_COPY` (1 : 0..N) — книга має примірники.
- `BOOK ||--o{ BOOK_AUTHOR` (1 : 0..N) — книга має авторів.
- `AUTHOR ||--o{ BOOK_AUTHOR` (1 : 0..N) — автор пише книги.
- `BOOK_COPY ||--o{ LOAN` (1 : 0..N) — примірник видається.
- `MEMBER ||--o{ LOAN` (1 : 0..N) — читач має видачі.
- `MEMBER ||--o{ RESERVATION` (1 : 0..N) — читач має бронювання.
- `BOOK ||--o{ RESERVATION` (1 : 0..N) — книга бронюється.
- `LOAN ||--o{ FINE` (1 : 0..N) — видача може мати штраф.
- `GENRE ||--o{ GENRE` (1 : 0..N) — ієрархія жанрів (parent of).
- `USER ||--o| MEMBER` (1 : 0..1) — користувач може бути читачем.
- `USER ||--o| LIBRARIAN` (1 : 0..1) — користувач може бути бібліотекарем.

## 4. Актори
- **Читач** (Primary) — шукає книги, позичає, бронює, сплачує штрафи.
- **Бібліотекар** (Primary) — видає і приймає книги, керує каталогом.
Платіжний шлюз і email-сервіс не є акторами (деталі реалізації).

## 5. Вимоги EARS
- **EARS-01 (Event-driven):** WHEN читач запитує видачу AND примірник доступний, THE SYSTEM SHALL створити видачу з терміном 14 днів.
- **EARS-02 (Unwanted Behavior):** IF читач має прострочені борги, THEN система SHALL відмовити у видачі з повідомленням.
- **EARS-03 (Unwanted Behavior):** IF читач уже має 5 позичених книг, THEN система SHALL відмовити у видачі.
- **EARS-04 (Event-driven):** WHEN примірника немає в наявності, THE SYSTEM SHALL запропонувати бронювання і створити `RESERVATION`.
- **EARS-05 (State-driven):** WHILE примірник видається, система SHALL тримати його статус «на руках».
- **EARS-06 (Event-driven):** WHEN читач повертає прострочену книгу, THE SYSTEM SHALL створити `FINE` (extend від «Повернути книгу»).

## 6. Gherkin-сценарії
```gherkin
Feature: Позичення книги (EARS-01..04)

  Scenario: Успішна видача
    Given читач має 0 прострочених і 2 позичені книги
    And є доступний примірник
    When читач запитує видачу
    Then створюється видача з терміном 14 днів
    And статус примірника «на руках»

  Scenario: Книги немає → пропозиція забронювати
    Given читач запитує книгу, примірників немає
    When система перевіряє наявність
    Then пропонується бронювання
    And створюється RESERVATION

  Scenario: Є прострочені борги → відмова
    Given читач має прострочені видачі
    When читач запитує видачу
    Then система відмовляє з повідомленням

  Scenario: Перевищено ліміт 5 книг → відмова
    Given читач має 5 позичених книг
    When читач запитує видачу
    Then система відмовляє через перевищення ліміту
```

## 7. Нефункціональні вимоги (NFR)
- **NFR-01 (Performance):** відповідь ≤ 2 с для 95% запитів при 200 одночасних користувачах.
- **NFR-02 (Compatibility):** веб-інтерфейс для desktop і мобільних браузерів.
- **NFR-03 (Localization):** мова UI — українська.
- **NFR-04 (Security):** паролі хешуються bcrypt або argon2.

## 8. Поза обсягом
- Електронні книги.
- Міжбібліотечний обмін.
- Фізична логістика.

## 9. Матриця трасування

| Вимога | UC | Сценарій |
| :--- | :--- | :--- |
| EARS-01 | Позичити книгу | Успішна видача |
| EARS-02 | Позичити книгу | Є прострочені борги → відмова |
| EARS-03 | Позичити книгу | Перевищено ліміт 5 книг → відмова |
| EARS-04 | Позичити книгу | Книги немає → пропозиція забронювати |
| EARS-05 | Позичити книгу | Успішна видача |
| EARS-06 | Повернути книгу | Сплатити штраф (extend) |