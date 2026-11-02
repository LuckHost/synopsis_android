# Coroutines

[источник](https://habr.com/ru/articles/838974/)

Корутины - метод выполнения асинхронного кода. То есть разные задачи, методы, могут выполняться на одном потоке одновременно.

Главное отличие от параллельных потоков - корутины не блокируют выполнение друг друга. На примере сетевого взаимодействия:

```kotlin
// код абстрактный, скорее всего не компилируется, 
// нужен для примера

fun main() {
    println("начало работы")
    val newThread = BackgroundThread()

    // Фоновый поток будет простаивать 3 секунды
    newThread.start()

    
    println("конец работы")
}

fun callServer() {
    println("начало зпроса")
    // Долгий запрос, 3 секунды
    Thread.sleep(3000) 
    val result = "ответ сервера"

    println(result)
}

class BackgroundThread() : Thread() {
    override fun run() {
        callServer()
    }
}
```

Вывод будет примерно таким

```
начало работы
начало запроса
конец работы
ответ сервера
```

Создание новых потоков и простаивание 3 секунд в `callServer` - дорогостоящая операция для ресурсов, особенно если мы говорим про Android и мобильные устройства. Корутины позволяют оптимизировать простаивание:

```kotlin
fun main() = runBlocking {
    println("начало работы")

    // Создаем корутину
    val job = launch {    
        callServer()
    }
    
    println("конец работы")

    // ждем, пока корутина тоже выполнится, 
    // не завершаем работу всей программы
    job.join()
}

suspend fun callServer() {
    println("начало зпроса")
    // Долгий запрос, 3 секунды
    delay(3000) 
    val result = "ответ сервера"

    println(result)
}
```

Вывод
```
начало работы
конец работы
начало запроса
ответ сервера
```

### Suspend функции

Suspend функция - прерываемая функция. Помечаются специальным оператором `suspend`, который изначально имеется в синтаксисе kotlin. Благодаря этому оператору при компиляции функция немного преобразуется.

Обычная `suspend` функция и ее вызов
```kotlin
fun main() = runBlocking {
    val someData: Int = getData()

    println(someData)
}

suspend fun getData(): Int {
    delay(3000)
    return 42
}
```
<p></p>
<s>Дикая функция</s> функция после преобразования (под капотом).

```kotlin
fun main() = runBlocking {
    val continuation = Continuation<Int> { someData ->
        println(someData)
    }

    getData(continuation)
}

fun getData(
    continuation: Continuation<Int>
): Unit {
    if (continuation.isCompleted) {
        return
    }

    delayCallback(3000) {
        continuation.resume(43)
    }
}

fun delayCallback(
    delay: Long,
    callback: () -> Unit
) {
    Thread.sleep(3000),
    callback()
}

```

Как можно видеть, все `suspend` функции при преобразовании становятся обычными, принимают на вход некий `Continuation`. 

`Continuation` 

На самом деле, конечно, все гораздо сложнее, но для понимания хватит и этого. 
