# ER-модель бібліотеки

```mermaid
erDiagram
    BOOK ||--o{ BOOK_COPY : "має примірник"
    BOOK ||--o{ BOOK_AUTHOR : "має автора"
    AUTHOR ||--o{ BOOK_AUTHOR : "пише"
    BOOK_COPY ||--o{ LOAN : "видається"
    MEMBER ||--o{ LOAN : "позичає"
    MEMBER ||--o{ RESERVATION : "бронює"
    BOOK ||--o{ RESERVATION : "бронюється"
    LOAN ||--o{ FINE : "має штраф"
    GENRE ||--o{ GENRE : "є підтипом"
    BOOK }o--o{ GENRE : "класифікується"
    USER ||--o| MEMBER : "є читачем"
    USER ||--o| LIBRARIAN : "є бібліотекарем"

    BOOK {
        string id PK
        string title
        string isbn UK
        int year
        string language
    }

    BOOK_COPY {
        string id PK
        string book_id FK
        string inventory_number UK
        string condition
        string status
    }

    AUTHOR {
        string id PK
        string name
    }

    BOOK_AUTHOR {
        string book_id PK, FK
        string author_id PK, FK
        int author_order
    }

    GENRE {
        string id PK
        string parent_id FK
        string name
        string slug UK
    }

    USER {
        string id PK
        string email UK
        string password_hash
        string first_name
        string last_name
        string role
        datetime created_at
    }

    MEMBER {
        string id PK
        string user_id FK
        string status
    }

    LIBRARIAN {
        string id PK
        string user_id FK
        string employee_code
    }

    LOAN {
        string id PK
        string copy_id FK
        string member_id FK
        datetime issued_at
        datetime due_at
        datetime returned_at
    }

    RESERVATION {
        string id PK
        string book_id FK
        string member_id FK
        datetime created_at
        string status
    }

    FINE {
        string id PK
        string loan_id FK
        decimal amount
        string reason
        boolean paid
    }
```

BOOK_AUTHOR — асоціативна сутність: зв'язок BOOK ↔ AUTHOR несе власний атрибут author_order (правило 10), тому не зводиться до прямого M:N.
Розбіжність з spec.md: BOOK_GENRE не є сутністю (правило 9) — M:N BOOK ↔ GENRE показано прямим зв'язком; spec.md залишено без змін.
Концептуальна модель, без фізичної схеми (SQL DDL, ORM).