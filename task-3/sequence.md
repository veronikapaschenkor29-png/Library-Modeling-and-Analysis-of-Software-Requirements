# Діаграми послідовності: UC_Borrow (Завдання 3)

Діаграми описують п'ять сценаріїв UC_Borrow; код дослівно з task-3/sequence.puml; рендер PlantUML.

## S1. Успішна видача (EARS-01)

```plantuml
@startuml S1_Success
skinparam sequenceMessageAlign center
skinparam defaultFontSize 14
title UC_Borrow: Успішна видача на 14 днів
actor "Читач" as R
actor "Бібліотекар" as L
participant "Сервіс" as S
participant "BOOK_COPY" as C
participant "LOAN" as N
R -> S : запит видачі
S -> N : перевірка боргів
N --> S : 0 прострочених, 2 активні
L -> S : наявність примірника
S -> C : перевірити статус примірника
C --> S : статус: доступний
alt ліміт не перевищено і боргів немає
  S -> N : створити LOAN (14 днів)
  N --> S : LOAN створено
  S -> C : статус: на руках
  S --> R : видача оформлена
else
  S --> R : відмова
end
@enduml
```

Діаграма показує успішну видачу книги на 14 днів при відсутності боргів і неперевищенні ліміту.

## S2. Книги немає → пропозиція забронювати (EARS-04)

```plantuml
@startuml S2_NoCopy_Reserve
skinparam sequenceMessageAlign center
skinparam defaultFontSize 14
title UC_Borrow: Книги немає, пропонується бронювання
actor "Читач" as R
participant "Сервіс" as S
participant "BOOK_COPY" as C
participant "RESERVATION" as RES
R -> S : запит книги
S -> C : перевірка примірника
C --> S : примірників немає
alt немає вільного примірника
  S -> RES : створити RESERVATION
  RES --> S : RESERVATION створено
  S --> R : пропонується бронювання
else примірник є
  S --> R : переходимо до видачі
end
@enduml
```

Діаграма показує пропозицію бронювання, коли вільного примірника немає.

## S3. Є прострочені борги → відмова (EARS-02)

```plantuml
@startuml S3_Debts_Refused
skinparam sequenceMessageAlign center
skinparam defaultFontSize 14
title UC_Borrow: Відмова при прострочених боргах
actor "Читач" as R
participant "Сервіс" as S
participant "LOAN" as N
R -> S : запит видачі
S -> N : перевірка боргів
N --> S : борги є
alt борги є
  S --> R : відмова
else боргів немає
  S --> R : перейти до перевірки ліміту
end
@enduml
```

Діаграма показує відмову у видачі при наявності прострочених боргів.

## S4. Перевищено ліміт 5 книг → відмова (EARS-03)

```plantuml
@startuml S4_Limit_Refused
skinparam sequenceMessageAlign center
skinparam defaultFontSize 14
title UC_Borrow: Відмова при перевищенні ліміту 5 позик
actor "Читач" as R
participant "Сервіс" as S
participant "LOAN" as N
R -> S : запит видачі
S -> N : перевірка позик
N --> S : 5 активних позик
alt активних позик 5 і більше
  S --> R : відмова
else менше 5
  S --> R : перейти до видачі
end
@enduml
```

Діаграма показує відмову у видачі при перевищенні ліміту активних позик.

## S5. Примірник на руках блокується (EARS-05)

```plantuml
@startuml S5_Copy_Blocked
skinparam sequenceMessageAlign center
skinparam defaultFontSize 14
title UC_Borrow: Примірник на руках блокує видачу
actor "Читач 2" as R2
participant "Сервіс" as S
participant "BOOK_COPY" as C
R2 -> S : запит видачі
S -> C : перевірити статус примірника
C --> S : статус: на руках
alt примірник недоступний
  S --> R2 : відмова
else примірник доступний
  S --> R2 : видача дозволена
end
@enduml
```

Діаграма показує блокування видачі примірника, що вже на руках.