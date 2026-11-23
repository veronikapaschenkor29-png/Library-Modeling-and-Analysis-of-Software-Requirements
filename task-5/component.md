# Component Diagram: архітектура бібліотеки

```mermaid
flowchart TB
    subgraph Client["Клієнт"]
        WebUI["Web UI"]
    end
    subgraph API["API"]
        Gateway["API Gateway"]
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
        PaymentAdapter["Payment Adapter"]
        IdentityAdapter["Identity Adapter"]
    end
    subgraph Data["Дані"]
        DB[("БД")]
    end
    subgraph External["Зовнішні системи"]
        EmailSMS["Email/SMS"]
        Payment["Payment"]
        OAuth["OAuth/Identity"]
    end

    WebUI -->|ILoanApi, IMemberApi, ICatalogApi, IReservationApi| Gateway
    Gateway -->|ILoanService| LoanService
    Gateway -->|IMemberService| MemberService
    Gateway -->|ICatalogService| CatalogService
    Gateway -->|IReservationService| ReservationService
    Gateway -->|IOAuthPort| IdentityAdapter

    LoanService -->|IReservationService| ReservationService
    LoanService -->|INotificationPort| NotificationService
    LoanService -->|IPaymentPort| PaymentAdapter
    LoanService --> CatalogService
    LoanService --> MemberService
    LoanService --> DataAccess
    CatalogService --> DataAccess
    MemberService --> DataAccess
    ReservationService --> DataAccess
    DataAccess --> DB
    NotificationService -->|IEmailSender| EmailSMS
    PaymentAdapter -->|зовнішній API| Payment
    IdentityAdapter -->|зовнішній API| OAuth
    NotificationService -->|зовнішній API| EmailSMS
```

## Інтерфейси (provided / required)

| Інтерфейс | Provided by | Required by | Призначення |
| :--- | :--- | :--- | :--- |
| `ILoanApi` | API Gateway | Web UI | `borrowBook`, `returnBook` через API |
| `IMemberApi` | API Gateway | Web UI | профіль читача |
| `ICatalogApi` | API Gateway | Web UI | каталог книг |
| `IReservationApi` | API Gateway | Web UI | бронювання |
| `ILoanService` | Loan Service | API Gateway | `borrowBook`, `returnBook` |
| `IMemberService` | Member Service | API Gateway | дані читача |
| `ICatalogService` | Catalog Service | API Gateway, Loan Service | каталог |
| `IReservationService` | Reservation Service | Loan Service, API Gateway | `createReservation` |
| `IPaymentPort` | Payment Adapter | Loan Service | оплата штрафу |
| `IOAuthPort` | Identity Adapter | API Gateway | автентифікація |
| `INotificationPort` | Notification Service | Loan Service | `sendLoanCreated`, `sendReminder` |
| `IEmailSender` | Email/SMS (зовнішній) | Notification Service | надсилання email/SMS |

## Інтерфейси (Mermaid)

```mermaid
flowchart LR
    WebUI[Web UI] -.->|requires| ILoanApi((ILoanApi))
    Gateway[API Gateway] ---|provides| ILoanApi
    WebUI -.->|requires| IMemberApi((IMemberApi))
    Gateway ---|provides| IMemberApi
    WebUI -.->|requires| ICatalogApi((ICatalogApi))
    Gateway ---|provides| ICatalogApi
    WebUI -.->|requires| IReservationApi((IReservationApi))
    Gateway ---|provides| IReservationApi

    Gateway -.->|requires| ILoanService((ILoanService))
    LoanService[Loan Service] ---|provides| ILoanService
    Gateway -.->|requires| IReservationService((IReservationService))
    ReservationService[Reservation Service] ---|provides| IReservationService

    LoanService -.->|requires| IPaymentPort((IPaymentPort))
    PaymentAdapter[Payment Adapter] ---|provides| IPaymentPort
    Gateway -.->|requires| IOAuthPort((IOAuthPort))
    IdentityAdapter[Identity Adapter] ---|provides| IOAuthPort
    LoanService -.->|requires| INotificationPort((INotificationPort))
    NotificationService[Notification Service] ---|provides| INotificationPort
    NotificationService -.->|requires| IEmailSender((IEmailSender))
    EmailSMS[Email/SMS] ---|provides| IEmailSender
```