# Блокировка vs приостановка потока

Здесь объясняется разница между блокировкой и приостановкой потока

Этот вопрос я ни разу не слышал на собеседованиях, но эти понятия часто фигурируют в статьях и материалах об асинхронности, так что их важно различать

### Блокировка

Это состояние потока, когда он не может выполнять никакой другой работы

Даже когда он простаивает, ему будут выделяться ресурсы и память

Пример
```kotlin
fun main() {
    val startTime = System.nanoTime() // Засекаем время начала выполнения

    println("Start: ${Thread.currentThread().name}")

    // Запускаем 100 фоновыми потоками
    repeat(100) { index ->
        Thread {
            println("Background task $index started on ${Thread.currentThread().name}")
            Thread.sleep(1000) // Блокировка потока
            println("Background task $index finished on ${Thread.currentThread().name}")
        }.start()
    }

    // Засекаем время окончания выполнения
    val endTime = System.nanoTime() // Засекаем время окончания

    println("End: ${Thread.currentThread().name}")

    // Выводим время выполнения
    val elapsedTime = (endTime - startTime) / 1_000_000 // Время в миллисекундах
    println("Total time taken: $elapsedTime ms")
}

```


Вывод
```
Start: main
Background task 0 started on Thread-0
Background task 1 started on Thread-1
Background task 2 started on Thread-2
...
Background task 99 started on Thread-99
End: main
Total time taken: 100000 ms


```

- Основной поток (main) блокируется на 5 секунд.
- Параллельно выполняется фоновая работа в отдельном потоке (Thread-0).
- Если бы мы хотели добавить в main еще задачи, они бы не выполнялись до завершения Thread.sleep().

### Приостановка (suspension)

В данном случе поток может освобождаться для выполнения других задач. Во время приостановки выполнения одной корутины он может переключиться на выполнение другой. В таком случае простаивание потока сводится к нулю.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val startTime = System.nanoTime() // Засекаем время начала выполнения

    println("Start: ${Thread.currentThread().name}")

    // Запускаем 100 фоновыми задачами с использованием корутин
    repeat(100) { index ->
        launch(Dispatchers.Default) {
            println("Background task $index started on ${Thread.currentThread().name}")
            delay(1000) // Приостановка потока, не блокирует
            println("Background task $index finished on ${Thread.currentThread().name}")
        }
    }

    // Засекаем время окончания выполнения
    val endTime = System.nanoTime() // Засекаем время окончания

    println("End: ${Thread.currentThread().name}")

    // Выводим время выполнения
    val elapsedTime = (endTime - startTime) / 1_000_000 // Время в миллисекундах
    println("Total time taken: $elapsedTime ms")
}

```

Вывод
```
Start: main
Background task 0 started on DefaultDispatcher-worker-1
Background task 1 started on DefaultDispatcher-worker-2
Background task 2 started on DefaultDispatcher-worker-3
...
Background task 99 started on DefaultDispatcher-worker-4
End: main
Total time taken: 1000 ms

```
