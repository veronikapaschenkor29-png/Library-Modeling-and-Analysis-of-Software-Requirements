# EARS-вимоги: позичення і повернення книги

- **EARS-01 (Event-driven):** WHEN читач запитує видачу AND примірник доступний, THE SYSTEM SHALL створити видачу з терміном 14 днів.
- **EARS-02 (Unwanted Behavior):** IF читач має прострочені борги, THEN THE SYSTEM SHALL відмовити у видачі з повідомленням.
- **EARS-03 (Unwanted Behavior):** IF читач уже має 5 позичених книг, THEN THE SYSTEM SHALL відмовити у видачі.
- **EARS-04 (Event-driven):** WHEN примірника немає в наявності, THE SYSTEM SHALL запропонувати бронювання і створити `RESERVATION`.
- **EARS-05 (State-driven):** WHILE примірник має статус «на руках», THE SYSTEM SHALL блокувати його видачу і бронювання іншим читачам.
- **EARS-06 (Event-driven):** WHEN читач повертає прострочену книгу, THE SYSTEM SHALL створити `FINE` (extend від «Повернути книгу»).