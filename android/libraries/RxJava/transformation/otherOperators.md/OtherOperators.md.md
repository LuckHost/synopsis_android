# buffer

позволяет выдваать подписчикам ответ в виде списка из нужных элементов

```kotlin
val disposable: Disposable = Observable.just("First item", "Second item", "Third item")
        .buffer(2)
        .subscribe({
          println(it)
        }, {
          
        })
```

Вывод:
["First item", "Second item"]
["Third item"]


# groupBy

позволяет группировать объекты по признаку и работать дальше с ними 

# scan 

грубо говоря - нахождеие факториалов если исходные данные - цифры 
