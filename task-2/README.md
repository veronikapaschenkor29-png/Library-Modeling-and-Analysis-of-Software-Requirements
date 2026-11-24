# Завдання 2 - Use Cases і вимоги бібліотеки

Актори, EARS-вимоги, Gherkin-сценарії, UC-діаграма. Деталі - у [spec.md](../spec.md).

**Аудит:** акторами є лише Читач і Бібліотекар; платіжний шлюз/email - не актори.

## UC-діаграма (Mermaid)

```mermaid
flowchart LR
    Reader[Читач] --> Borrow[Позичити книгу]
    Reader --> Return[Повернути книгу]
    Reader --> Reserve[Забронювати книгу]
    Librarian[Бібліотекар] --> Return
    Borrow -. include .-> CheckDebts[Перевірити борги]
    Reserve -. extend .-> Borrow
    CreateFine[Створити штраф] -. extend .-> Return
```