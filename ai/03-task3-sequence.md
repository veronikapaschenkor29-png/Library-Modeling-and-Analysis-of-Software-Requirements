# AI-слід — Завдання 3 (Sequence-діаграми)

## Промпт (дослівно)
> Для кожного з 4 сценаріїв позичення книги побудуй окрему sequence-діаграму. Учасники — сутності/сервіси з ER-моделі. Щонайменше один фрагмент alt/opt/loop/par. Реалістичні назви викликів (checkDebts, getAvailableCopy). Activation bars коректні.

## Відповідь AI
Activation bars не закривались у гілці `alt` (залишались відкритими після умови); учасник `Reservation Service` дублював `Loan Service`.

## Мій фідбек
1. Кожен `alt`-блок закриває activation bar у тій самій гілці.
2. Учасники узгоджені з ER: Читач, Web UI, Loan Service, Member Service, Catalog, Reservation Service, БД.
3. Додано фрагмент `alt` для перевірки боргів і ліміту.

## Результат
`task-3/sequence.md`. Коміти: `86eb7e7`, `3f4400f`.