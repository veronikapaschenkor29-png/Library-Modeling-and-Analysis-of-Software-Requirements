# AI-журнал - Завдання 1 (ER-модель)

## Ітерація 1: Початковий запит на проєктування бази даних бібліотеки

**Промт:**
> "Мені потрібно спроєктувати базу даних для бібліотеки. Зроби таблиці для книг, авторів, читачів, позик, бронювань і штрафів. Використай Mermaid erDiagram."

**Отриманий результат від AI:**
```mermaid
erDiagram
    BOOK ||--o{ BOOK_COPY : "має примірники"
    BOOK }o--o{ AUTHOR : "написана авторами"
    USER ||--o{ LOAN : "оформлює позики"
    BOOK_COPY ||--o{ LOAN : "видається"
    USER ||--o{ RESERVATION : "бронює"
    BOOK_COPY ||--o{ RESERVATION : "бронюється"
    LOAN ||--o| FINE : "може мати штраф"
    GENRE ||--o{ GENRE : "батьківський жанр"
    BOOK }o--o{ GENRE : "належить жанрам"

    BOOK {
        uuid id PK
        string isbn UK
        string title
        int publication_year
        string language
        datetime created_at
    }

    BOOK_COPY {
        uuid id PK
        uuid book_id FK
        string inventory_number UK
        string status
        string condition
        datetime created_at
    }

    AUTHOR {
        uuid id PK
        string full_name
        string bio
        date birth_date
    }

    BOOK_AUTHOR {
        uuid book_id FK
        uuid author_id FK
        int author_order
    }

    USER {
        uuid id PK
        string email UK
        string password_hash
        string full_name
        string phone
        string role
        datetime created_at
    }

    MEMBER {
        uuid user_id PK_FK
        string card_number UK
        date registration_date
        string status
    }

    LIBRARIAN {
        uuid user_id PK_FK
        string employee_number UK
        date hire_date
    }

    LOAN {
        uuid id PK
        uuid member_id FK
        uuid book_copy_id FK
        datetime loan_date
        date due_date
        datetime return_date
        string status
    }

    RESERVATION {
        uuid id PK
        uuid member_id FK
        uuid book_copy_id FK
        datetime reservation_date
        date expiry_date
        string status
    }

    FINE {
        uuid id PK
        uuid loan_id FK
        decimal amount
        string reason
        string status
        datetime created_at
    }

    GENRE {
        uuid id PK
        uuid parent_id FK
        string name
        string slug UK
    }
```

**Аналіз результату:**
- AI запропонував 12 сутностей для домену бібліотеки.
- Ключові рішення: M:N між BOOK та AUTHOR через BOOK_AUTHOR, M:N між BOOK та GENRE (прямий, без проміжної сутності), узагальнення USER → MEMBER/LIBRARIAN.
- Виявлено проблему: BOOK_GENRE було включено як проміжна сутність, але згодом прибрано на користь прямого M:N.

---

## Ітерація 2: Уточнення щодо жанрів та авторів

**Промт:**
> "Прибери BOOK_GENRE як окрему сутність. Зроби M:N між BOOK і GENRE прямим. Також додай атрибут author_order у BOOK_AUTHOR для збереження порядку авторів."

**Отриманий результат від AI:**
- Підтверджено прямий M:N між BOOK і GENRE (без проміжної сутності).
- Додано `author_order` (int) у BOOK_AUTHOR.
- Оновлено кардинальність: `BOOK }o--o{ GENRE`.

**Рішення, що прийнято:**
- BOOK_GENRE прибрано з кореневого spec.md.
- GENRE має self-reference (parent_id) для ієрархії жанрів.
- BOOK_AUTHOR зберігає порядок авторів через author_order.

---

## Ітерація 3: Перевірка цілісності та нормалізації

**Промт:**
> "Перевір, чи відповідає ER-модель 3NF. Чи є зайві залежності? Чи правильно визначено PK/UK/FK?"

**Отриманий результат від AI:**
- Усі сутності відповідають 3NF.
- PK визначено для кожної сутності (UUID).
- UK: USER.email, BOOK.isbn, GENRE.slug, BOOK_COPY.inventory_number, MEMBER.card_number, LIBRARIAN.employee_number.
- FK: BOOK_COPY.book_id → BOOK, BOOK_AUTHOR.book_id/author_id, LOAN.member_id/book_copy_id, RESERVATION.member_id/book_copy_id, FINE.loan_id, GENRE.parent_id → GENRE, MEMBER.user_id/LIBRARIAN.user_id → USER.

**Висновок:**
- Модель готова до реалізації.
- Створено spec.md з повним описом PK/UK/FK та кардинальностей.

---

## Підсумок рішень

| Сутність | PK | UK | FK |
|---|---|---|---|
| BOOK | id (UUID) | isbn | - |
| BOOK_COPY | id (UUID) | inventory_number | book_id → BOOK |
| AUTHOR | id (UUID) | - | - |
| BOOK_AUTHOR | (book_id, author_id) | - | book_id → BOOK, author_id → AUTHOR |
| USER | id (UUID) | email | - |
| MEMBER | user_id (PK/FK) | card_number | user_id → USER |
| LIBRARIAN | user_id (PK/FK) | employee_number | user_id → USER |
| LOAN | id (UUID) | - | member_id → MEMBER, book_copy_id → BOOK_COPY |
| RESERVATION | id (UUID) | - | member_id → MEMBER, book_copy_id → BOOK_COPY |
| FINE | id (UUID) | - | loan_id → LOAN |
| GENRE | id (UUID) | slug | parent_id → GENRE (Optional) |

**Коміти:** `a83f0a9`, `915e1f4`, `7d5afdd`