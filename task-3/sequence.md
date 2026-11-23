# Sequence-діаграми: позичення книги

**Аудит:** activation bars збалансовані: відкриття/закриття відповідають, фінальні `deactivate` стоять після `end`; учасники узгоджені з ER-моделлю.

## Діаграма 1. Успішна видача

```mermaid
sequenceDiagram
    autonumber
    actor Reader as Читач
    participant WebUI as Web UI
    participant LoanService as Loan Service
    participant MemberService as Member Service
    participant Catalog as Catalog Service
    participant DB as БД

    Reader->>+WebUI: запит видачі
    WebUI->>+LoanService: borrowBook(memberId, bookId)
    LoanService->>+MemberService: checkDebts(memberId)
    MemberService->>+DB: findOverdueLoans(memberId)
    DB-->>-MemberService: борги є/немає
    MemberService-->>-LoanService: hasDebts
    alt Борги є
        LoanService-->>WebUI: відмова (борги)
        WebUI-->>Reader: повідомлення про борги
    else Боргів немає
        LoanService->>+Catalog: getAvailableCopy(bookId)
        Catalog->>+DB: findAvailableCopy(bookId)
        DB-->>-Catalog: copy
        Catalog-->>-LoanService: copy
        LoanService->>+DB: createLoan(copyId, memberId, dueAt)
        DB-->>-LoanService: loan
        LoanService->>+DB: updateCopyStatus(copyId, "на руках")
        DB-->>-LoanService: ok
        LoanService-->>WebUI: loan створено
        WebUI-->>Reader: книгу видано (14 днів)
    end
    deactivate LoanService
    deactivate WebUI
```

## Діаграма 2. Примірника немає → бронювання

```mermaid
sequenceDiagram
    autonumber
    actor Reader as Читач
    participant WebUI as Web UI
    participant LoanService as Loan Service
    participant Catalog as Catalog Service
    participant ReservationService as Reservation Service
    participant DB as БД

    Reader->>+WebUI: запит видачі
    WebUI->>+LoanService: borrowBook(memberId, bookId)
    LoanService->>+Catalog: getAvailableCopy(bookId)
    Catalog->>+DB: findAvailableCopy(bookId)
    DB-->>-Catalog: немає
    Catalog-->>-LoanService: null
    alt Примірника немає
        LoanService->>+ReservationService: createReservation(bookId, memberId)
        ReservationService->>+DB: insertReservation(bookId, memberId)
        DB-->>-ReservationService: reservation
        ReservationService-->>-LoanService: reservation
        LoanService-->>WebUI: пропозиція бронювання
        WebUI-->>Reader: заброньовано
    end
    deactivate LoanService
    deactivate WebUI
```

## Діаграма 3. Відмова через борги

```mermaid
sequenceDiagram
    autonumber
    actor Reader as Читач
    participant WebUI as Web UI
    participant LoanService as Loan Service
    participant MemberService as Member Service
    participant DB as БД

    Reader->>+WebUI: запит видачі
    WebUI->>+LoanService: borrowBook(memberId, bookId)
    LoanService->>+MemberService: checkDebts(memberId)
    MemberService->>+DB: findOverdueLoans(memberId)
    DB-->>-MemberService: борги є/немає
    MemberService-->>-LoanService: hasDebts
    alt Є прострочені борги
        LoanService-->>WebUI: відмова (борги)
        WebUI-->>Reader: повідомлення про борги
    else Боргів немає
        LoanService-->>WebUI: продовжити до перевірки ліміту/наявності (Діаграми 1/4)
        WebUI-->>Reader: продовжити видачу
    end
    deactivate LoanService
    deactivate WebUI
```

## Діаграма 4. Перевищено ліміт 5 книг

```mermaid
sequenceDiagram
    autonumber
    actor Reader as Читач
    participant WebUI as Web UI
    participant LoanService as Loan Service
    participant MemberService as Member Service
    participant DB as БД

    Reader->>+WebUI: запит видачі
    WebUI->>+LoanService: borrowBook(memberId, bookId)
    LoanService->>+MemberService: countActiveLoans(memberId)
    MemberService->>+DB: countLoans(memberId)
    DB-->>-MemberService: 5
    MemberService-->>-LoanService: count = 5
    alt Перевищено ліміт (>= 5)
        LoanService-->>WebUI: відмова (ліміт)
        WebUI-->>Reader: перевищено ліміт 5 книг
    end
    deactivate LoanService
    deactivate WebUI
```

## Діаграма 5. Повернення простроченої книги → `FINE`

```mermaid
sequenceDiagram
    autonumber
    actor Reader as Читач
    participant WebUI as Web UI
    participant LoanService as Loan Service
    participant DB as БД

    Reader->>+WebUI: повернення книги
    WebUI->>+LoanService: returnBook(loanId)
    LoanService->>+DB: findLoan(loanId)
    DB-->>-LoanService: loan
    alt Прострочено
        LoanService->>+DB: updateLoan(loanId, returned_at, status="повернено")
        DB-->>-LoanService: ok
        LoanService->>+DB: createFine(loanId, amount, paid=false)
        DB-->>-LoanService: fine
        LoanService->>+DB: updateCopyStatus(loan.copyId, "доступний")
        DB-->>-LoanService: ok
        LoanService-->>WebUI: штраф створено
        WebUI-->>Reader: повідомлення про штраф
    else Вчасно
        LoanService->>+DB: updateLoan(loanId, returned_at, status="повернено")
        DB-->>-LoanService: ok
        LoanService->>+DB: updateCopyStatus(loan.copyId, "доступний")
        DB-->>-LoanService: ok
        LoanService-->>WebUI: повернення прийнято
        WebUI-->>Reader: без штрафу
    end
    deactivate LoanService
    deactivate WebUI
```