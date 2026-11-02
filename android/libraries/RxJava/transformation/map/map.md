# Map

применяется для каждого элемента данных

```kotlin
val disposable: Disposable = Observable.just("First item", "Second item", "Third item")
        .map(
          if(it.toLowerCase().contains("first")) {
            it + " первый элемент"
          } else {
            it + " не первый элемент"
          }
        )
        .subscribe({
          println(it)
        }, {
          
        })
```

Вывод:

First item первый элемент
Second item не первый элемент
Third item не первый элемент
