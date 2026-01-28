# Generics

Как и в Java, в Kotlin классы могут иметь типовые параметры.

```kotlin
class Box<T>(t: T) {
    var value = t
}
```
Для того чтобы создать объект такого класса, необходимо предоставить тип в качестве аргумента.

`val box: Box<Int> = Box<Int>(1)`
Но если параметры могут быть выведены из контекста (в аргументах конструктора или в некоторых других случаях), можно опустить указание типа.

`val box = Box(1)` 
`1` имеет тип Int, поэтому компилятор отмечает для себя, что тип переменной box — Box<Int>



# Вариантность, ковариантность и контравариантность

[источник тут](https://metanit.com/kotlin/tutorial/6.3.php)

Вариантность описывает, как обобщенные типы, типизированные классами из одной иерархии наследования, соотносятся друг с другом.

### Табличка сравнения

**Обозначения**
- ↑ Данный объект является родителем для сравниваемого
- ↓ Данный объект является производным от сравниваемого 
- X Данный объект никак не относится к сравниваемому

|Название|Отношение к child|Отношение к parent|Синтаксис|
|-|-|-|-|
|Инвариатность|X|X|T: Object|
|ковариантность|↑|↓|out T: Object|
|контравариантность|↓|↑|in T: Object|

### Инвариантность

Инвариантность предполагает, что, если у нас есть классы `Base` и `Child`, где `Child` - производный класс от `Base`, то класс `SomeClass<Base>` не является ни базовым классом для `SomeClass<Child>`, ни производным. Например, у нас есть следующие типы:

```kotlin
interface Messenger<T: Message>()
 
open class Message(val text: String)
class EmailMessage(text: String): Message(text)
```

В данном случае мы не можем присвоить объект `Messenger<EmailMessage>` переменной типа `Messenger<Message>` и наоборот, они никак между собой не соотносятся, несмотря на то, что `EmailMessage` наследуется от `Message`:

```kotlin
fun changeMessengerToEmail(obj: Messenger<EmailMessage>){
    val messenger: Messenger<Message> = obj   // ! Ошибка
}
fun changeMessengerToDefault(obj: Messenger<Message>){
    val messenger: Messenger<EmailMessage> = obj      // ! Ошибка
}
```
Мы можем присвоить переменным по умолчанию только объекты их типов:

```kotlin
fun changeMessengerToDefault(obj: Messenger<Message>){
    val messenger: Messenger<Message> = obj
}
fun changeMessengerToEmail(obj: Messenger<EmailMessage>){
    val messenger: Messenger<EmailMessage> = obj
}
```

### Ковариантость

Ковариантость предполагает, что, если у нас есть классы `Base` и `Child`, где `Base` - базовый класс для `Child`, то класс `SomeClass<Base>` является базовым классом для `SomeClass<Child>`

Для определения обобщенного типа как ковариантного параметр обощения определяется с ключевым словом `out`:

```kotlin
interface Messenger<out T: Message>
open class Message(val text: String)
class EmailMessage(text: String): Message(text)
```
И теперь переменной типа `Messenger<Message>` мы можем присвоить значение типа `Messenger<EmailMessage>`

```kotlin
fun changeMessengerToEmail(obj: Messenger<EmailMessage>){
    val messenger: Messenger<Message> = obj
}
```

### Контравариантность

Контравариантость предполагает в какой-то степени обратную ситуацию. Контравариантость предполагает, что, если у нас есть классы `Base` и `Child`, где `Base` - базовый класс для `Child`, то объекту `SomeClass<Child>` мы можем присвоить значение `SomeClass<Base>` (при ковариантности, наоборот, - объекту `SomeClass<Base>` можно присвоить значение `SomeClass<Child>`)

Для определения обобщенного типа как контравариантного параметр обобщения определяется с ключевым словом `in`:

```kotlin
interface Messenger<in T: Message>
open class Message(val text: String)
class EmailMessage(text: String): Message(text)
```

В данном случае интерфейс `Messenger` является контравариантным, так как его параметр определен со словом `in`.

И теперь переменной типа `Messenger<EmailMessage>` мы можем присвоить значение типа `Messenger<Message>`

```kotlin
fun changeMessengerToDefault(obj: Messenger<Message>){
    val messenger: Messenger<EmailMessage> = obj
}
```