# Component Diagram: архітектура бібліотеки

```mermaid
flowchart TB
    subgraph Client["Клієнт"]
        WebUI["Web UI"]
    end
    subgraph Services["Сервіси"]
        CatalogService["Catalog Service"]
        LoanService["Loan Service"]
        MemberService["Member Service"]
        NotificationService["Notification Service"]
        ReservationService["Reservation Service"]
    end
    subgraph Infra["Інфраструктура"]
        DataAccess["Data Access"]
    end
    subgraph Data["Дані"]
        DB[("БД")]
    end
    subgraph External["Зовнішні системи"]
        EmailSMS["Email/SMS"]
        Payment["Payment"]
        OAuth["OAuth/Identity"]
    end

    WebUI -->|ILoanService| LoanService
    LoanService -->|INotificationPort| NotificationService
    LoanService -->|IReservationService| ReservationService
    LoanService --> CatalogService
    LoanService --> MemberService
    LoanService --> DataAccess
    CatalogService --> DataAccess
    MemberService --> DataAccess
    ReservationService --> DataAccess
    DataAccess --> DB
    NotificationService --> EmailSMS
    LoanService -->|IPaymentPort| Payment
    WebUI -->|IOAuthPort| OAuth
```

## Інтерфейси (provided / required)

| Інтерфейс | Provided by | Required by | Призначення |
| :--- | :--- | :--- | :--- |
| `ILoanService` | Loan Service | Web UI | `borrowBook`, `returnBook` |
| `INotificationPort` | Notification Service | Loan Service | `sendLoanCreated`, `sendReminder` |
| IReservationService | Reservation Service | Loan Service | createReservation |
| IPaymentPort | Payment (зовнішній) | Loan Service | оплата штрафу |
| IOAuthPort | OAuth/Identity (зовнішній) | Web UI | автентифікація |

## Інтерфейси (Mermaid)

```mermaid
flowchart LR
    WebUI[Web UI] -.->|requires| ILoanService((ILoanService))
    LoanService[Loan Service] ---|provides| ILoanService
    LoanService -.->|requires| INotificationPort((INotificationPort))
    NotificationService[Notification Service] ---|provides| INotificationPort
    LoanService -.->|requires| IReservationService((IReservationService))
    ReservationService[Reservation Service] ---|provides| IReservationService
```