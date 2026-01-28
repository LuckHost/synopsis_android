# CoroutineScope

Каждая корутина должна быть привязана к определенной области, называемой **CoroutineScope**. Этот Scope имеет свой жизненный цикл, помимо этого он управляет жизненным циклом каждой своей корутины. Данная иерархия помогает избежать утечек памяти 

**CoroutineScope** является интерфейсом. Одной из функций, которая создает объект **CoroutineScope**, является **coroutineScope**

```kotlin
import kotlinx.coroutines.*
 
suspend fun main(){
 
    doWork()
 
    println("Hello Coroutines")
}
suspend fun doWork()= coroutineScope{
    launch{
        for(i in 0..5){
            println(i)
            delay(400L)
        }
    }
}
```

Также их можно запустить параллельно 

```kotlin
import kotlinx.coroutines.*
 
suspend fun main()= coroutineScope{
 
    launch{
        for(i in 0..5){
            delay(400L)
            println(i)
        }
    }
    launch{
        for(i in 6..10){
            delay(400L)
            println(i)
        }
    }
 
    println("Hello Coroutines")
}
```

Вывод
```
Hello Coroutines
6
0
7
1
8
2
9
3
10
4
5
```


### runBlocking

Блокирует основной поток и там запускает нужные корутины. Пока они не выполнят свою работу, поток будет заблокирован.

В отличие от `launch` и `async` не является расширением CoroutineScope

```kotlin
import kotlinx.coroutines.*
 
fun main() = runBlocking{
    launch{
        for(i in 0..5){
            delay(400L)
            println(i)
        }
    }
 
    println("Hello Coroutines")
}

```

`coroutineScope` в свою очередь просто приостанавливает поток

Стоит использовать только в main или в тестах

### отличие Scope от Context

Scope - просто обертка, он собирает все воедино, объединяет под собой все корутины и содержит основной Job, который будет являться родителем для новых корутин

Также является источником элементов для построения новых корутин по умолчанию 

Context - набор парметров для корутин 

### GlobalScope

Отменить невозможно, работает до окончания, ломает принципы StructuredConcurrency

Применять с осторожностью

## Создание Scope

```kotlin
CoroutineScope(Job() + Dispatchers.Deafault)

// or

coroutineScope {
  // параллельные операции
  launch(Dispatchers.IO) {
    //todo
  }
  launch(Dispatchers.IO) {
    //todo
  }
}

```

### Создание дочерних Scope

- креш пробрасывается наврех
- родительский останаливает дочерние
- coroutineScope ждет всех

параметры берутся от родителей, Job создается новый 

### SupervisorScope

использует supervisorJob

### Отделение от UI

родительские Scope не живут дольше родителей, если приложение закрыто, а сообщение надо отправить на сервер, лучше эти scope отделять

### withContext

помогает сменить context