# Матриця трасування вимог (Traceability Matrix) - Завдання 2

## 1. Матриця покриття (EARS → Use Cases → Gherkin → Sequence → Activity → Компоненти)

| ID вимоги | Тип EARS | Опис вимоги | Use Cases | Gherkin-сценарій | Sequence (task-3) | Activity (task-4) | Компоненти (task-5) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **EARS-01** | Event-driven | Видача примірника на 14 днів при наявності | `Borrow`, `CheckDebts` | Успішна видача | Діагр. 1 (Успішна видача) | `S1a`(ні) → `S1b`(ні) → `S2`(так) → `S3`, `F1`/`S4`,`S5`, `J1`, `S6` | `Loan Service`, `Member Service`, `Catalog Service`, `Notification Service` |
| **EARS-02** | Unwanted Behavior | Відмова при прострочених боргах | `CheckDebts`, `Borrow` | Є прострочені борги → відмова | Діагр. 3 (Відмова через борги) | `S1a`(так) → `R2` | `Member Service` |
| **EARS-03** | Unwanted Behavior | Відмова при 5 позичених книгах | `Borrow`, `CheckDebts` | Перевищено ліміт 5 книг → відмова | Діагр. 4 (Перевищено ліміт 5 книг) | `S1b`(так) → `R2` | `Member Service`, `Loan Service` |
| **EARS-04** | Event-driven | Бронювання при відсутності примірника | `Reserve`, `Borrow` | Книги немає → пропозиція забронювати | Діагр. 2 (Примірника немає → бронювання) | `S2`(ні) → `S9` | `Reservation Service`, `Catalog Service`, `Loan Service` |
| **EARS-05** | State-driven | Блокування примірника «на руках» | `Borrow`, `Reserve` | Примірник на руках блокується | Діагр. 1, 2 | `S2` (перевірка доступності) | `Catalog Service`, `Loan Service` |
| **EARS-06** | Event-driven | Створення `FINE` при поверненні простроченої книги | `Return`, `CreateFine` | Повернення простроченої книги → штраф | Діагр. 5 (Повернення простроченої книги → `FINE`) | `L1` → `F2` → `S10`(так) → `S11` → `S13`, `J2` | `Loan Service`, `Overdue Scheduler`, `Data Access` |

## 2. Зворотне покриття Use Cases

| Use Case | EARS | Gherkin | Sequence | Activity | Компоненти | NFR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `Borrow` (Позичити книгу) | EARS-01, EARS-02, EARS-03, EARS-05 | Успішна видача; Є прострочені борги → відмова; Перевищено ліміт 5 книг → відмова; Примірник на руках блокується | Діагр. 1, 3, 4 | `S1a`, `S1b`, `S2`, `S3`, `F1`, `S4`, `S5`, `J1`, `S6` | `Loan Service`, `Member Service`, `Catalog Service`, `Notification Service` | NFR-01, NFR-02, NFR-03, NFR-04 |
| `CheckDebts` (Перевірити борги читача) | EARS-02, EARS-03 | Є прострочені борги → відмова; Перевищено ліміт 5 книг → відмова | Діагр. 3, 4 | `S1a`, `S1b` | `Member Service` | NFR-01, NFR-04 |
| `Return` (Повернути книгу) | EARS-06 | Повернення простроченої книги → штраф | Діагр. 5 | `L1`, `F2`, `S10`, `S12`, `S13`, `J2` | `Loan Service`, `Data Access` | NFR-01, NFR-02, NFR-03, NFR-04 |
| `CreateFine` (Створити штраф) | EARS-06 | Повернення простроченої книги → штраф | Діагр. 5 | `S11` | `Loan Service`, `Overdue Scheduler` | NFR-04 |
| `Reserve` (Забронювати книгу) | EARS-04, EARS-05 | Книги немає → пропозиція забронювати; Примірник на руках блокується | Діагр. 2 | `S2`, `S9` | `Reservation Service`, `Catalog Service`, `Loan Service` | NFR-01, NFR-02, NFR-03, NFR-04 |

## 3. Передумови та постумови Use Cases

### Borrow (Позичити книгу)
- **Preconditions:** Читач ідентифікований як `MEMBER`; немає прострочених боргів; активних позик < 5; існує доступний `BOOK_COPY` (або пропонується бронювання).
- **Postconditions:** створено `LOAN` з `due_at` = issued_at + 14 днів; `BOOK_COPY.status` → «на руках».

### Return (Повернути книгу)
- **Preconditions:** існує `LOAN` для примірника; повернення ініціює Читач/Бібліотекар.
- **Postconditions:** `LOAN.returned_at` і статус оновлені; `BOOK_COPY.status` → «доступний»; якщо прострочено - створено `FINE` (EARS-06).

### Reserve (Забронювати книгу)
- **Preconditions:** примірників `BOOK_COPY` для `BOOK` немає в наявності.
- **Postconditions:** створено `RESERVATION` зі статусом очікування.

## 4. Компоненти ↔ сутності / Use Cases (task-5)

| Компонент | Сутності ER | UC / EARS | Sequence | Activity |
| :--- | :--- | :--- | :--- | :--- |
| Web UI | - | Borrow, Return, Reserve (EARS-01–06) | Діагр. 1–5 | кроки читача |
| API Gateway | - | усі захищені UC | усі | маршрутизація |
| Auth Service | `USER`, `LIBRARIAN` | - | - | - |
| Catalog Service | `BOOK`, `BOOK_COPY`, `BOOK_AUTHOR`, `BOOK_GENRE`, `AUTHOR`, `GENRE` | Borrow, Reserve (наявність) (EARS-01, 04, 05) | Діагр. 1, 2 | `S2`, `S9` |
| Loan Service | `LOAN`, `BOOK_COPY`, `FINE` | Borrow, Return, CreateFine (EARS-01–03, 06) | Діагр. 1–5 | `S3`, `S4`, `S10`, `S11`, `S13` |
| Member Service | `MEMBER` | CheckDebts (EARS-02, 03) | Діагр. 3, 4 | `S1a`, `S1b` |
| Reservation Service | `RESERVATION`, `BOOK` | Reserve (EARS-04) | Діагр. 2 | `S9` |
| Notification Service | - | - | - | `S5`, `S6`, `S12` |
| Data Access | усі сутності | усі (EARS-01–06) | виклики БД | - |
| Payment Adapter | `FINE` (оплата) | - | - | - |
| Identity Adapter | - | - | - | - |
| Outbox Relay Worker | - | - | - | - |
| Overdue Scheduler | `LOAN`, `FINE` | CreateFine (EARS-06) | - | - |
| Database | усі сутності | - | - | - |

## 5. Поза обсягом
- Електронні книги, міжбібліотечний обмін, фізична логістика (`../spec.md`, розд. 8).
- Оплата штрафів через зовнішній платіжний шлюз - може бути додана пізніше (`../task-5/spec.md`, розд. 6).

## 6. Перевірка покриття
- Усі `EARS-01..06` мають щонайменше один UC, Gherkin-сценарій, sequence-діаграму, вузли activity і компоненти.
- Усі 5 Use Cases мають трасування до EARS.
- NFR-01..NFR-04 трасуються до UC і сутностей (розд. 1–2).
