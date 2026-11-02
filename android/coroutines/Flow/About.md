# Flow 

Холодный асинхронный поток данных, котоырй может законится успешно, либо неуспешно. Последовательно эмитит значения

**emit** (эмитить) - отправлять данные подписчикам

Помимо Flow существует SharedFlow и StateFlow, которые являются горячими

## Создание Flow

Самый простой способ - использовать `flowOf()` или оператор `asFlow()`

```kotlin
flowOf(1)
flowOf("a", "b")
listOf("a", "b").asFlow()
```

Если же мы сами хотим эмитить значения, что можем воспользоваться следующей сигнатурой

```kotlin
fun fibonacci(count: Int) = flow {
  reqire(count >= 1) { "count must be positive value" }
  emit(1)

  if (count >= 2) emit(2)

  var previous = 1
  var current = 1

  for (i in 3..count) {
    val a = current
    current += previous
    previous = a
    emit(currernt)
  }
}
```
