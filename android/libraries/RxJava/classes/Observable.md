# Observable 

Чаще всего используют

```kotlin
val observable = Observable.just(1, 2, 3)

val dispose = observable.subscribe({
    Log.e(TAG, "new data $it")
  }, {

  }
)
```


это базовый тип в RxJava, который используется для работы с последовательностями данных. нужен тогда, когда подписчики могут обработать все данные, т.е., когда данных не много 

содержит помимо этого ветки 

onError
onComplite
onNext

каждые из них содержат ветку Flowable
