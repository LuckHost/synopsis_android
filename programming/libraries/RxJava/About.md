# RxJava
or
[ReactiveX](https://github.com/ReactiveX/RxJava)

### import 
```gradle
implementation "io.reactivex.rxjava3:rxjava:3.x.y"
implementation 'io.reactivex.rxjava3:rxandroid:3.x.y'
```

вторая зависимость нужна для того, чтоб подписчики могли находиться на другом потоке от потока выполнения

оень популярная библиотека, которая
работет с многопоточностью и наблюдателями

изначально выполняется в главном потоке, поэтому их желательно менять
```kotlin
  .subscribeOn(Schedulers.io())
  .observeOn(Schedulers.single())
```
первая функция меняет поток выполнения, вторая - поток наблюдателя


