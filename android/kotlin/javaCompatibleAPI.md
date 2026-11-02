# Java-friendly code on Kotlin

Для того, чтоб подружить Java код с Kotlin, стоит использовать некоторые методы

### Использование аннотаций

- **@JvmStatic**
Для генерации статических методов в классах
- **@JvmField**
Для генерации полей без геттеров и сеттеров
- **@JvmOverloads**
Для автоматической генерации перегрузок методов
  ```kotlin
  class Example {
    @JvmOverloads
    fun greet(name: String = "World") {
        println("Hello, $name!")
    }
  }
  ```
  ```Java
  Example example = new Example();
  example.greet();         // Hello, World!
  example.greet("Alice");  // Hello, Alice!

  ```


- **@Throws**
Для явного указания возможных выбрасываемых исключений

### Обеспечеие обработки null

Нужно учитывать, что в Kotlin есть non-nullable типы, которые в Java изначально не определены, за этим стоит следить. Для создания не нуллабельного типа в Java можно использовать аннотации `@Nullable` и `@NonNull`

### Другое

Помимо всего прочего стоит:
- следить за внутренними классами
- стараться создавать Mutable коллекции, либо пользоваться Java Collections Framework