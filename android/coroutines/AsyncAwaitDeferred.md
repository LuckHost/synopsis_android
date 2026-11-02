# Async, await и Deferred

Помимо `launch` есть еще способы создать корутину. Одним из таких является `async`

`async` используется, когда нужен результат выполнения корутины

```kotlin
import kotlinx.coroutines.*
 
suspend fun main() = coroutineScope{
 
    async{ printHello()}
    println("Program has finished")
}
suspend fun printHello(){
    delay(500L)  // имитация продолжительной работы
    println("Hello work!")
}
```

```
Program has finished
Hello work!
```

`async`-корутина возвращает объект **Deferred**, который ожидает результат корутины. Является наследником Job

```kotlin
import kotlinx.coroutines.*
 
suspend fun main() = coroutineScope{
 
    val message: Deferred<String> = async{ getMessage()}
    println("message: ${message.await()}")
    println("Program has finished")
}
suspend fun getMessage() : String{
    delay(500L)  // имитация продолжительной работы
    return "Hello"
}

```
```
message: Hello
Program has finished
```