# EARS-вимоги: позичення книги

- **EARS-01 (Event-driven):** WHEN читач запитує видачу AND примірник доступний, THE SYSTEM SHALL створити видачу з терміном 14 днів.
- **EARS-02 (Unwanted Behavior):** IF читач має прострочені борги, THEN система SHALL відмовити у видачі з повідомленням.
- **EARS-03 (Unwanted Behavior):** IF читач уже має 5 позичених книг, THEN система SHALL відмовити у видачі.
- **EARS-04 (Event-driven):** WHEN примірника немає в наявності, THE SYSTEM SHALL запропонувати бронювання і створити `RESERVATION`.