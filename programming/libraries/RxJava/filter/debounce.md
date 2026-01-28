# debounce

```kotlin
val disposable = Observable.just("First item", "Second item", "Third item")
        .debounce(
          //представим, что данные приходят быстро
          500,
          TimeUnit.MILLISECONDS
        )
        .subscribe({
          println(it)
        }, {
          
        })
``` 

Вывод:

Third item

Отправляет данные подписчику только тогда, когда истечет таймер между временем их получения.

Отлично подходит для поисковика, так как туда будут отправляться не "к" "ко" "коф" "кофе", а сразу "кофе"

