# Launched effect

Нужен для того, чтоб мы могли что-то делать на запуске *composable* функции

Чаще всего использутся для того, чтоб запустить что-то одноразовое на старте функции 

Помимо этого **LaunchedEffect** принимает ***key***

```kotlin
@Composable 
@NonRestartableComposable
@OptIn(InternalComposeApi::class)
LaunchedEffect(
  key1: Any?
  block: suspend CoroutineScope.() -> Unit
)
```

Это те состояния, при изменении которых **LaunchedEffect** будет перезапущен
Нужен в те моменты, когда мы все же хотим перезапуска. К примеру можно использовать в привязке к lifestate

Количество принимаемых ***key*** не ограничено 

В случе, если нам надо просто один раз вызвать функцию, то мы просто можем использовать
```kotlin
LaunchedEffect(key1 = Unit, block = {
  ...
})
// or
LaunchedEffect(key1 = true, block = {
  ...
})
```