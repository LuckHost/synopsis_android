## Exceptions в RxJava

[источник](https://www.geeksforgeeks.org/error-handling-in-rxjava/?ysclid=m3o1dve1o744606776)

Мы довольно часто сталкиваемся с ошибками при выполнении какого-то кода, а когда у нас выполняется несколько штук параллельно 

В RxJava есть несколько методов обработки исключений

### onError()

Первый вариант - просто отлавливать их в переопределенном методе `onError()`

```kotlin
Observable
    .just(1,2,3,4)
    .subscribe(object : Observer<Int> {
        override fun onComplete() {
            Log.d("onComplete ", "Completed")
        }
        override fun onSubscribe(d: Disposable) {
        }
        override fun onNext(i: Int) {
            if (i == 3) {
                throw NullPointerException("Its a Null Pointer Exception")
            }
            Log.d("onNext ", i.toString())
        }
        override fun onError(e: Throwable) {
            Log.d("onError ", e.localizedMessage)
        }
    }
)

```

Вывод:
```
Its a Null Pointer Exception
```

### Операторы

Также существуют операторы, которые позволяют отлавливать исключения:

- doOnError()
- onErrorReturn()
- onErrorReturnItem()
- onErrorResumeNext()
- onExceptionResumeNext()


##### doOnError

Выполняет указанное действие при возникновении ошибки, не изменяя поток данных.

- Используется для побочных действий, например логирования.
- Ошибка передается дальше в поток.
- Не поглощает ошибку.

```kotlin
Observable.just(1, 2, 3, 4)
    .doOnNext {
        if (it == 2) {
            throw RuntimeException("Exception on 2")
        }
    }
    .doOnError { throwable ->
        Log.d("doOnError", "Caught error: ${throwable.message}")
    }
    .subscribe(
        { item -> Log.d("onNext", "Received: $item") },
        { error -> Log.d("onError", "Error: ${error.message}") },
        { Log.d("onComplete", "Completed") }
    )
```

Вывод:
```
onNext: Received: 1
doOnError: Caught error: Exception on 2
onError: Error: Exception on 2
```

##### onErrorReturn

Перехватывает ошибку и возвращает заранее заданное значение.

- Ошибка заменяется значением.
- Завершает поток после возвращения значения.

```kotlin
Observable.just(1, 2, 3, 4)
    .doOnNext {
        if (it == 2) {
            throw RuntimeException("Exception on 2")
        }
    }
    .onErrorReturn { throwable ->
        Log.d("onErrorReturn", "Replacing error with default value")
        -1 // Возвращаемое значение вместо ошибки
    }
    .subscribe(
        { item -> Log.d("onNext", "Received: $item") },
        { error -> Log.d("onError", "Error: ${error.message}") },
        { Log.d("onComplete", "Completed") }
    )
```

Вывод
```
onNext: Received: 1
onErrorReturn: Replacing error with default value
onNext: Received: -1
onComplete: Completed

```

##### onErrorReturnItem

Упрощенная версия onErrorReturn

- Нужен просто для замены значения, если контекст ошибки не важен
- Завершает поток 

```kotlin
Observable.just(1, 2, 3, 4)
    .doOnNext {
        if (it == 3) {
            throw RuntimeException("Exception on 3")
        }
    }
    .onErrorReturnItem(-1) // Возвращаем -1 вместо ошибки
    .subscribe(
        { item -> Log.d("onNext", "Received: $item") },
        { error -> Log.d("onError", "Error: ${error.message}") },
        { Log.d("onComplete", "Completed") }
    )

```

```
onNext: Received: 1
onNext: Received: 2
onErrorReturnItem: Replacing error with -1
onNext: Received: -1
onComplete: Completed
```

##### onErrorResumeNext

При возникновении ошибки продолжает выполнение с помощью другого Observable.

- Позволяет переключиться на альтернативный поток.
- Обрабатывает все ошибки (Throwable), включая исключения времени выполнения и критические ошибки.

```kotlin
Observable.just(1, 2, 3, 4)
    .doOnNext {
        if (it == 2) {
            throw RuntimeException("Exception on 2")
        }
    }
    .onErrorResumeNext(Observable.just(5, 6, 7))
    .subscribe(
        { item -> Log.d("onNext", "Received: $item") },
        { error -> Log.d("onError", "Error: ${error.message}") },
        { Log.d("onComplete", "Completed") }
    )

```

Вывод
```
onNext: Received: 1
onNext: Received: 5
onNext: Received: 6
onNext: Received: 7
onComplete: Completed
```

##### onExceptionResumeNext

Переключается на другой Observable только при возникновении исключений типа Exception. Ошибки типа Error не обрабатываются.

- Обрабатывает только исключения, наследуемые от Exception.
- Игнорирует критические ошибки (например, OutOfMemoryError).

```kotlin
Observable.just(1, 2, 3, 4)
    .doOnNext {
        if (it == 2) {
            throw RuntimeException("Exception on 2")
        }
    }
    .onExceptionResumeNext(Observable.just(8, 9))
    .subscribe(
        { item -> Log.d("onNext", "Received: $item") },
        { error -> Log.d("onError", "Error: ${error.message}") },
        { Log.d("onComplete", "Completed") }
    )

```
Вывод
```
onNext: Received: 1
onNext: Received: 8
onNext: Received: 9
onComplete: Completed

```
