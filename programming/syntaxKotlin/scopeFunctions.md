# Scope-functions

[источник](https://metanit.com/kotlin/tutorial/5.7.php)
можно перевести как "_функции контекста_" или "_функции области видимости_" 

Позволяют выполнить для некоторого объекта некоторый код в виде лямбда-выражения. При вызове подобной функции, создается временная область видимости. В этой области видимости можно обращаться к объекту без использования его имени.

По сути, все эти функции делают одно и то же: выполняют блок кода для объекта. Отличие состоит в том, как этот объект становится доступным внутри блока и каков результат всего выражения.

## Общая таблица сравнения

|Функция|Контекстный объект|Return|Функция расширения|
|-|-|-|-|
|let|it|результат лямбды|+||
|run|this|результат лямбды|+||
|run|-|результат лямбды|- (может быть вызвана без)||
|with|this|результат лямбды|- (принимает объект как аргумент)||
|also|it|объект контекста|+||
|apply|this|объект контекста|+||

## let

Лямбда-выражение в функции `let` в качестве параметра `it` получает объект, для которого вызывается функция. Возвращаемый результат функции `let` представляет результат данного лямбда-выражения.

```kotlin
fun main() {
 
    val sam = Person("Sam", "sam@gmail.com")
    sam.email?.let{ println("Email: $it") }     // Email: sam@gmail.com
    // аналог без функции let
    //if(sam.email!=null) println("Email:${sam.email}")
 
    val tom = Person("Tom", null)
    tom.email?.let{ println("Email: $it") } // функция let не будет выполняться
 
}
data class Person(val name: String, val email: String?)
```

Если лямбда-выражение вызывает лишь одну функцию, в которую передается параметр `it`, то можно сократить вызов - указать после оператора `::` название вызываемой функции:

```kotlin
fun main() {
 
    val sam = Person("Sam", "sam@gmail.com")
    sam.email?.let(::println)   // sam@gmail.com
 
    val tom = Person("Tom", "tom@gmail.com")
    tom.email?.let(::printEmail)    // Email: tom@gmail.com
 
}
 
fun printEmail(email: String){
    println("Email: $email")
}
data class Person(val name: String, var email: String?)
```

## with

Лямбда-выражение в функции `with` в качестве параметра `this` получает объект, для которого вызывается функция. Возвращаемый результат функции `with` представляет результат данного лямбда-выражения.

Функция `with` принимает объект, для которого надо выполнить блок кода, в качестве параметра. Далее в самом блоке кода мы можем обращаться к общедоступным свойствам и методам объекта без его имени.

Обычно функция `with()` применяется, когда надо выполнить над объектом набор операций как единое целое.

Часто `with` применяется для установки свойств объектов. Например:

```kotlin
fun main() {
 
    val tom = Person("Tom", -19,null)
    with(tom) {
        if(email==null) email = "${name.lowercase()}@gmail.com"
        if(age !in 1..110) age = 18
    }
    println("${tom.name} (${tom.age}) - ${tom.email}") // Tom (18) - tom@gmail.com
}
data class Person(val name: String, var age:Int, var email: String?)
```

## run

Лямбда-выражение в функции `run` в качестве параметра `this` получает объект, для которого вызывается функция. Возвращаемый результат функции `run` представляет результат данного лямбда-выражения.

Функция `run` похожа на функцию `with` за тем исключением, что `run` реализована как функция расширения. Функция `run` также принимает объект, для которого надо выполнить блок кода, в качестве параметра. Далее в самом блоке кода мы можем обращаться к общедоступным свойствам и методам объекта без его имени.

```kotlin
fun main() {
 
    val tom = Person("Tom", null)
    val validationResult = tom.email?.run {"valid"} ?: "undefined"
    println(validationResult) // undefined
}
data class Person(val name: String, var email: String?)
```

Помимо вызова `run` для объекта-получателя, вы можете использовать его как функцию без расширения. В этом случае run позволяет выполнить блок из нескольких операторов там, где это требуется.

```kotlin
fun main() {
    val hexNumberRegex = run {
        val digits = "0-9"
        val hexDigits = "A-Fa-f"
        val sign = "+-"

        Regex("[$sign]?[$digits$hexDigits]+")
    }

    for (match in hexNumberRegex.findAll("+1234 -FFFF not-a-number")) {
        println(match.value)
    }
}
```

## apply

Лямбда-выражение в функции `apply` в качестве параметра `this` получает объект, для которого вызывается функция. Возвращаемым результатом функции фактически является передаваемый в функцию объект для которого выполняется функция.

```kotlin
fun main() {
 
    val tom = Person("Tom", null)
    tom.apply {
        if(email==null) email = "${name.lowercase()}@gmail.com"
    }
    println(tom.email) // tom@gmail.com
}
data class Person(val name: String, var email: String?)
```

Распространенным сценарием применения функции `apply()` является построение объекта в виде реализации вариации паттерна **Builder**

```kotlin
fun main() {
 
    val bob = Employee()
    bob.name("Bob")
    bob.age(26)
    bob.company("JetBrains")
    println("${bob.name} (${bob.age}) - ${bob.company}") // Bob (26) - JetBrains
}
 
data class Employee(var name: String = "", var age: Int = 0, var company: String = "") {
    fun age(_age: Int): Employee = apply { age = _age }
    fun name(_name: String): Employee = apply { name = _name }
    fun company(_company: String): Employee = apply { company = _company }
}
```

## also 

Лямбда-выражение в функции `also` в качестве параметра `it` получает объект, для которого вызывается функция. Возвращаемым результатом функции фактически является передаваемый в функцию объект для которого выполняется функция.

```kotlin
fun main() {
 
    val tom = Person("Tom", null)
    tom.also {
        if(it.email==null)
            it.email = "${it.name.lowercase()}@gmail.com"
    }
    println(tom.email) // tom@gmail.com
}
data class Person(val name: String, var email: String?)
```