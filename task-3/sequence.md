# Діаграми послідовності: UC_Borrow (Завдання 3)

Діаграми описують п'ять сценаріїв UC_Borrow; код дослівно з task-3/sequence.puml; рендер PlantUML.

## S1. Успішна видача (EARS-01)

```plantuml
@startuml S1_Success
title UC_Borrow: Успішна видача на 14 днів
actor "Читач" as R
actor "Бібліотекар" as L
participant "Сервіс видачі" as S
participant "Примірник (BOOK_COPY)" as C
participant "Видача (LOAN)" as N
R -> S : запит видачі книги
activate S
S -> N : перевірити активні позики та борги читача
activate N
N --> S : 0 прострочених, 2 активні позики
deactivate N
L -> S : підтвердити наявність примірника
S -> C : перевірити статус примірника
activate C
C --> S : статус доступний
deactivate C
alt ліміт не перевищено і боргів немає
  S -> N : створити видачу (термін 14 днів)
  activate N
  N --> S : LOAN створено
  deactivate N
  S -> C : встановити статус «на руках»
  S --> R : видача оформлена, термін 14 днів
else
  S --> R : відмова
end
deactivate S
@enduml
```

Діаграма показує успішну видачу книги на 14 днів при відсутності боргів і неперевищенні ліміту.

## S2. Книги немає → пропозиція забронювати (EARS-04)

```plantuml
@startuml S2_NoCopy_Reserve
title UC_Borrow: Книги немає, пропонується бронювання
actor "Читач" as R
participant "Сервіс видачі" as S
participant "Примірник (BOOK_COPY)" as C
participant "Бронювання (RESERVATION)" as RES
R -> S : запит книги
activate S
S -> C : перевірити наявність примірника
activate C
C --> S : примірників немає
deactivate C
alt немає вільного примірника
  S -> RES : створити бронювання
  activate RES
  RES --> S : RESERVATION створено
  deactivate RES
  S --> R : пропонується бронювання
else примірник є
  S --> R : переходимо до видачі
end
deactivate S
@enduml
```

Діаграма показує пропозицію бронювання, коли вільного примірника немає.

## S3. Є прострочені борги → відмова (EARS-02)

```plantuml
@startuml S3_Debts_Refused
title UC_Borrow: Відмова при прострочених боргах
actor "Читач" as R
participant "Сервіс видачі" as S
participant "Видача (LOAN)" as N
R -> S : запит видачі книги
activate S
S -> N : перевірити прострочені видачі
activate N
N --> S : є прострочені видачі
deactivate N
alt є прострочені видачі
  S --> R : відмова з повідомленням про борг
else боргів немає
  S --> R : перейти до перевірки ліміту
end
deactivate S
@enduml
```

Діаграма показує відмову у видачі при наявності прострочених боргів.

## S4. Перевищено ліміт 5 книг → відмова (EARS-03)

```plantuml
@startuml S4_Limit_Refused
title UC_Borrow: Відмова при перевищенні ліміту 5 позик
actor "Читач" as R
participant "Сервіс видачі" as S
participant "Видача (LOAN)" as N
R -> S : запит видачі книги
activate S
S -> N : перевірити кількість активних позик
activate N
N --> S : 5 активних позик
deactivate N
alt активних позик 5 і більше
  S --> R : відмова через перевищення ліміту
else менше 5
  S --> R : перейти до видачі
end
deactivate S
@enduml
```

Діаграма показує відмову у видачі при перевищенні ліміту активних позик.

## S5. Примірник на руках блокується (EARS-05)

```plantuml
@startuml S5_Copy_Blocked
title UC_Borrow: Примірник на руках блокує видачу
actor "Читач 2" as R2
participant "Сервіс видачі" as S
participant "Примірник (BOOK_COPY)" as C
R2 -> S : запит видачі примірника
activate S
S -> C : перевірити статус примірника
activate C
C --> S : статус «на руках»
deactivate C
alt примірник недоступний
  S --> R2 : відмова, примірник недоступний
else примірник доступний
  S --> R2 : видача дозволена
end
deactivate S
@enduml
```

Діаграма показує блокування видачі примірника, що вже на руках.
