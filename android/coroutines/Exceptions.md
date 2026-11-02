### try-catch

Самый простой метод 

Для обработки `launch` и `async` требуются немного разные подходы

```kotlin
launch {
    try {
        doSmth()
    } catch (e: Exception) {
        // Обработка
    }
}
```

```kotlin
val deferred: Deferred<Data> = async {
    doSmth()
}

try {
    deferref.await()
} catch (e: Exception) {
    // Обработка
}
```

Помимо этого можно создать дочерний scope и отлавливать исключения в нем

```kotlin
try {
    coroutineScope {
        ...
    }
} catch (e: Exception) {
    // Обработка
}
```

### Подписка на результат Job

В данном методе мы подписываемся на результат корутингы. Мы также не можем повлиять на исключение во время ее работы. Помимо этого `invokeOnCompletion` не вызыввается в случае CancelException, а также при исключении до создания CompletionHandler

```kotlin
val job = launch(start = CoroutineStart.LAZY) {
    код с исключением
}

job.invokeOnCompletion { cause: Throwable? ->
    if (cause != null) {
        // Обработка исключения
    } else {
        // Успешное выполнение
    }
}

job.join()
```

### CoroutineExceptionHandler

[источник](https://www.youtube.com/watch?v=P3mr2oN-x9g&list=PL0SwNXKJbuNmsKQW9mtTSxNn00oJlYOLA&index=3)

Обрабатывает исключение в последний момент и не может повлиять на что-то во время работы корутины

Обработчик исключений, которые происходят в корутинах или в их детях

Напоминает общий try-catch блок

```kotlin
CoroutineExceptionHandler { context, error: Throwable ->
    logError(error)
}
```

Когда исключение обработано им, то наверх к родителям оно пробрасываться не будет

### Canceletion Exception

Исключение, которое генерируется при остановке корутны

Это исключение не пробрасывается к родителю, но все дочерние классы остановят свою работу

В случае, если мы используем try-catch, то нам нужно пробросить это исключение наверх, прописав ветку отдельно

```kotlin
    try {
        ...
    } catch (e: CanceletionException) {
        // Пробрасывем наверх
        throw e
    } catch (e: Exception) {
        // Обрабатываем другие исключения 
    }
```