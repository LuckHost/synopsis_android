# Disposable 

Можно перевести в контексте "устранимый"
isDisposed - утилизирован

Позволяет останавливать поток в нужное время.
Тип этого объекта возвращает класс Observable, когда мы на него подписываемся

```kotlin
val disposable: Disposable = Observable.just("First item", "Second item")
        .subscribe()
```

Disposable содержит всего два метода

dispose() и isDisposed()
первый останавливает Disposable, второй проверяет его состояние

Помимо этого мы можем их группировать

```kotlin
val disposableBag = CompositeDisposable(
        Observable.just("First item", "Second item").subscribe(),
        Observable.just("1", "2").subscribe(),
        Observable.just("One", "Two").subscribe()
);
```