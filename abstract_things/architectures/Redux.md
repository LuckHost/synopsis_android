# Redux architecture

[источник тут](https://habr.com/ru/companies/wheely/articles/548982/)

![кто читает тому пусть повезет](./images/redux_archit.gif)

### State

Состояние является **иммутабельным** объектом и единственным источником истины для всего приложения. 

### Store

Store содержит глобальное состояние (State) вашего приложения, а также все подключенные к нему *Middleware* и *Reducer*. 

Типичное API позволяет вам получать текущее состояние, отправлять события (Action), подписываться и отписываться от изменений состояния.

### Action

События, единственные данные, которые вы можете отправлять вашему *Store*. События как правило сообщают о взаимодействии с приложением или являются своего рода намерением явно изменить состояние. Опционально могут содержать внутри себя дополнительные данные.

### Reducer

Чистая функция, которая меняет текущее состояние в ответ на пришедшее событие (Action). Для тех, кто не знает, чистая функция всегда возвращает одинаковое значение при одинаковых входных данных (детерминированность) и не имеет побочных эффектов (никаким образом не изменяет локальные переменные, не осуществляет ввод, вывод, и т.д.).

Поскольку состояние у нас является иммутабельным, *Reducer* использует Copy-on-write подход, копируя состояние целиком с изменением только необходимой части.

### Middleware

*Middleware* является своего рода промежуточным звеном, позволяя перехватывать события (Action) и заменять их в случае необходимости, до того, как они попадут в наш *Reducer*.

*Middleware* является тем самым механизмом принятия решений и местом, где содержится бизнес-логика нашего приложения. 

## А как подружить Redux с Android?

### Описываем состояние

Помните, что я говорил о роли состояния в Redux? Простой иммутабельный объект, который содержит всю необходимую нам информацию и позволяет легко производить над ним операции по принципу Copy-on-write. Лучшее, что вы можете выбрать для этого подхода в Kotlin - `data class`. 

**ApplicationState.kt**
```kotlin
data class ApplicationState(
  val counter: Int = 0
)
```
Выглядит так же просто, как и звучит, едем дальше.

### События

Все события в нашей реализации должны так или иначе реализовывать интерфейс `Action` из библиотеки Redux. Интерфейс `Action` является маркерным и не содержит никаких методов для реализации. Я стараюсь логически декомпозировать события для простоты работы с ними и обработки их в *Middleware* и *Reducer*, а также использовать `sealed class`’ы, последние ограничивают всех возможных наследников до узкого круга того, что нас непосредственно интересует. В итоге наши события будут выглядеть вот так.

**CounterAction.kt**
```kotlin
sealed class CounterAction : Action {
  object Increment : CounterAction()
  
  object Reset : CounterAction()
}
```

## Reducer

В нашем случае это объект, который реализует интерфейс `Reducer<S>`, где `S` - глобальное состояние нашего приложения, т.е. В нашем случае `ApplicationState`. Интерфейс описывает одну-единственную функцию - `reduce`. Не забываем про то, что функция должна быть чистой.

**CounterReducer.kt**
```kotlin
object CounterReducer : Reducer<ApplicationState> {
  override fun reduce(action: Action, state: ApplicationState): 
  ApplicationState = {
    when (action) {
      is CounterAction.Increment ->
        state.copy(counter = state.counter.inc())
          
      is CounterAction.Reset ->
        state.copy(counter = 0)
          
      else ->
        state
    }
  }
}
```

### Store

А теперь соберем все эти компоненты в единый механизм, и поможет нам в этом *Store*. 

Библиотека уже содержит в себе абстрактный *Store* с реализацией всех необходимых методов. Всё что нам нужно сделать - создать наследника класса `AbstractStore<S>` и явно указать тип нашего глобального состояния. Это же состояние будет передаваться в наши *Middleware* и *Reducer*.

**ApplicationStore.kt**

```kotlin
class ApplicationStore(
  initialState: ApplicationState,
  middlewares: List<Middleware<ApplicationState>>,
  reducers: List<Reducer<ApplicationState>>
) : AbstractStore<ApplicationState>(initialState, middlewares, reducers)
```

Теперь нам необходимо создать экземпляр класса `ApplicationStore`, передать ему изначальное состояние и список всех подключенных *Middleware* и *Reducer*. Поскольку *Store*, равно как и `ApplicationState` должны иметь время жизни равное времени жизни нашего приложения - сделаем `AppComponent` и положим наш *Store* туда.

**AppComponent.kt**
```kotlin
object AppComponent {
  val store = ApplicationStore(
    initialState = ApplicationState(),
    middlewares = emptyList(),
    reducers = listOf(CounterReducer)
  )
}
```

Для чистоты кода я также советую создать подобные функции в любом удобном для вас файле, поскольку отправка событий, подписка и отписка будут часто использоваться во всем приложении.

**ReduxFunctions.kt**
```kotlin
fun dispatch(action: Action) =
  AppComponent.store.dispatch(action)
    
fun subscribe(subscription: Subscription<ApplicationState>) =
  AppComponent.store.subscribe(subscription)
    
fun unsubscribe(subscription: Subscription<ApplicationState>) =
  AppComponent.store.unsubscribe(subscription)
```

### Подведем промежуточный итог

Сейчас у нас есть иммутабельное состояние, которое отражает значение нашего счетчика, есть события, которые позволяют взаимодействовать с этим значением, и reducer, который обрабатывает эти события, меняя состояние соответствующим образом. Несмотря на общую простоту конструкции, можно заметить весьма жесткие ограничения. Мы можем быть уверены в том, что состояние может измениться только в результате работы reducer’a, а тот в свою очередь сработает, только если в store будет отправлено соответствующее событие. Дело осталось за малым, UI!