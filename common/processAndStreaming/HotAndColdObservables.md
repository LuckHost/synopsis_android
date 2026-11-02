## Cold Observables

Суть в том, что каждый новый подписчик получает данные с самого начала. Observable генерирует данные с нуля для каждого из них

Пример на RxJava
```kotlin
val coldObservable = Observable.just("Item 1", "Item 2", "Item 3")

coldObservable.subscribe { println("Subscriber 1: $it") }
coldObservable.subscribe { println("Subscriber 2: $it") }

```

Вывод
```
Subscriber 1: Item 1
Subscriber 1: Item 2
Subscriber 1: Item 3
Subscriber 2: Item 1
Subscriber 2: Item 2
Subscriber 2: Item 3

```

Пример с Flow
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun coldFlow(): Flow<Int> = flow {
    for (i in 1..3) {
        delay(1000) // имитируем долгую операцию
        emit(i) // отправляем элемент
    }
}

fun main() = runBlocking {
    val flow = coldFlow()

    flow.collect { println("Subscriber 1: $it") }

    flow.collect { println("Subscriber 2: $it") }
}

```

Вывод
```
Subscriber 1: 1
Subscriber 1: 2
Subscriber 1: 3
Subscriber 2: 1
Subscriber 2: 2
Subscriber 2: 3
```


## Hot Observables

Все подписчики получает один и тот же источник. Данные до  подписи теряются для подписчика

Удобно для работы с "живыми" источниками данных. Ввод с клавиатуры, веб-сокеты, UI ивенты.

Пример на RxJava
```kotlin
val hotObservable = PublishSubject.create<String>()

hotObservable.subscribe { println("Subscriber 1: $it") }

hotObservable.onNext("Item 1")
hotObservable.onNext("Item 2")

hotObservable.subscribe { println("Subscriber 2: $it") }

hotObservable.onNext("Item 3")

```

Вывод
```
Subscriber 1: Item 1
Subscriber 1: Item 2
Subscriber 1: Item 3
Subscriber 2: Item 3
```

Пример обычного Hot Flow
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    val sharedFlow = MutableSharedFlow<Int>()

    launch {
        for (i in 1..3) {
            delay(1000)
            sharedFlow.emit(i)
            println("Emitted: $i")
        }
    }

    launch {
        sharedFlow.collect { println("Subscriber 1: $it") }
    }

    delay(2000)

    launch {
        sharedFlow.collect { println("Subscriber 2: $it") }
    }

    delay(3000)
}

```

Вывод
```
Emitted: 1
Subscriber 1: 1
Emitted: 2
Subscriber 1: 2
Subscriber 2: 2
Emitted: 3
Subscriber 1: 3
Subscriber 2: 3
```

#### Hot Flow с StateFlow

Тот же Hot Flow, но сохраняет последнее состояние. Новый подписчик сразу получит текущее состояние

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    val stateFlow = MutableStateFlow(0)

    launch {
        repeat(5) {
            delay(1000)
            stateFlow.value = it
            println("Emitted: $it")
        }
    }

    launch {
        stateFlow.collect { println("Subscriber 1: $it") }
    }

    delay(3000)

    launch {
        stateFlow.collect { println("Subscriber 2: $it") }
    }

    delay(3000)
}

```

Вывод
```
Emitted: 0
Subscriber 1: 0
Emitted: 1
Subscriber 1: 1
Emitted: 2
Subscriber 1: 2
Emitted: 3
Subscriber 1: 3
Subscriber 2: 3
Emitted: 4
Subscriber 1: 4
Subscriber 2: 4
```

Новый подписчик получает последнее состояние (3) сразу после подписки.



---
## Табличка сравнения
| Характеристика             | Cold Observable                      | Hot Observable                       |
|----------------------------|--------------------------------------|--------------------------------------|
| **Поведение**              | Генерирует данные для каждого подписчика. | Делит один поток данных между подписчиками. |
| **Когда начинают работать?** | При подписке.                       | Независимо от подписчиков, поток может уже идти. |
| **Использование**          | Для фиксированных или конечных данных. | Для "живых" данных или событий.     |
