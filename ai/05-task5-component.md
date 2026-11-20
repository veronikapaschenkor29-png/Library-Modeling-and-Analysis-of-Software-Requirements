# AI-слід — Завдання 5 (Компоненти)

## Промпт (дослівно)
> Компонентна діаграма: модулі UI, API/Backend, сервіси (Catalog, Loan, Member, Notification), Data Access, БД. Зовнішні інтеграції: Email/SMS, Payment, OAuth/Identity. Provided/required інтерфейси, залежності, чітка межа системи. PlantUML component.

## Відповідь AI
Не визначив інтерфейси (provided/required); межа системи була розмита (зовнішні сервіси всередині).

## Мій фідбек
1. ILoanService — provided Web UI; INotificationPort — required від Loan Service.
2. Зовнішні інтеграції винесено за межу системи (Email/SMS, Payment, OAuth).
3. Кожен компонент трасується до ER-сутностей і UC.

## Результат
`task-5/component.md`, `component.puml`. Коміти: `99eb2fa`, `8d6cab9`.