# Завдання 3 - Sequence-діаграми бібліотеки

## Мета
Окрема sequence-діаграма на кожен із 5 сценаріїв позичення і повернення книги; учасники узгоджені з ER-моделлю та компонентами. Деталі - у [spec.md](../spec.md). Діаграми - у [sequence.md](sequence.md).

## Артефакти

- [spec.md](spec.md) - специфікація завдання 3 (взаємодія, sequence).
- [../docs/adr/0005-borrow-include-debts-check.md](../docs/adr/0005-borrow-include-debts-check.md), [../docs/adr/0006-fine-as-extend-on-return.md](../docs/adr/0006-fine-as-extend-on-return.md), [../docs/adr/0007-sequence-per-scenario.md](../docs/adr/0007-sequence-per-scenario.md) - архітектурні рішення.

## 5 сценаріїв

1. [Успішна видача](sequence.md#діаграма-1-успішна-видача) - перевірка боргів, видача примірника, створення `LOAN` (14 днів).
2. [Примірника немає → бронювання](sequence.md#діаграма-2-примірника-немає--бронювання) - створення `RESERVATION`, якщо примірника немає.
3. [Відмова через борги](sequence.md#діаграма-3-відмова-через-борги) - відмова у видачі при прострочених боргах.
4. [Перевищено ліміт 5 книг](sequence.md#діаграма-4-перевищено-ліміт-5-книг) - відмова при 5 активних позиках.
5. [Повернення простроченої книги → `FINE`](sequence.md#діаграма-5-повернення-простроченої-книги--fine) - повернення зі створенням штрафу або без.

## Учасники

`Читач`, `Web UI`, `Loan Service`, `Member Service`, `Catalog`, `Reservation Service`, `БД`.

## Трасування до EARS і UC

- **EARS:** [task-2/ears.md](../task-2/ears.md) - EARS-01 (видача), EARS-02 (борги), EARS-03 (ліміт), EARS-04 (бронювання), EARS-05 (блокування), EARS-06 (штраф).
- **UC:** [task-2/usecase.puml](../task-2/usecase.puml) - `Borrow` (include `CheckDebts`), `Reserve` (extend `Borrow`), `Return` (extend `CreateFine`).

| Діаграма | EARS | UC |
|---|---|---|
| 1. Успішна видача | EARS-01, EARS-05 | Borrow, CheckDebts |
| 2. Примірника немає → бронювання | EARS-04, EARS-05 | Reserve, Borrow |
| 3. Відмова через борги | EARS-02 | CheckDebts |
| 4. Перевищено ліміт 5 книг | EARS-03 | Borrow |
| 5. Повернення простроченої книги → FINE | EARS-06 | Return, CreateFine |

## Перевірка

1. Усі 5 сценаріїв мають окремі діаграми в `sequence.md`.
2. Кожна діаграма має блок `alt` або `opt`.
3. Учасники відповідають сутностям і сервісам з `spec.md` та `task-5/component.md`.
4. Activation bars збалансовані (відкриття/закриття відповідають).
5. Трасування до EARS і UC повне (усі 6 вимог і 5 UC покриті).

**Аудит:** activation bars збалансовані; учасники узгоджені з ER-моделлю.