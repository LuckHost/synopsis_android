## inline

Ключевое слово, которое используется для оптимизации работы с лямбда-функциям, которые передаются в качестве аргумента

Вместо того, чтоб создавать новый объект, компилятор вставит содержимое лямбды в том месте, где она используется 

```kotlin
inline fun doSomething(block: () -> Unit) {
    println("Before block")
    block()
    println("After block")
}

fun main() {
    doSomething {
        println("Inside block")
    }
}

```
Компилятор вставит код `block()` напрямую в тело вызова `doSomething()`

## crossinline 

Используется для лямбда-выражений, котоыре не должны завершать выполнение функции через `return` 

```kotlin
inline fun performAction(crossinline action: () -> Unit) {
    println("Start")
    action()  // Нельзя завершить выполнение функции через return
    println("End")
}

fun main() {
    performAction {
        println("Executing action")
        // return  // Ошибка: return запрещен
    }
}

```

## noinline

Используется, когда мы намеренно хотим отказаться от inline, так как компилятор иногда может сам создавать inline объекты

```kotlin
inline fun processData(noinline block: () -> Unit) {
    println("Processing data...")
    val function = block  // Лямбда не встраивается, хранится как объект
    function()
}

fun main() {
    processData {
        println("Executing block")
    }
}

```

## reified

