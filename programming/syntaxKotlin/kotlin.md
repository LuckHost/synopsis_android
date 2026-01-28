### Примитивы 

В Kotlin нет примитивных типов int, float, double и т. д., всё является объектами. 

Вместо примитивных типов используются объекты: 
- Byte; 
- Short; 
- Int (не Integer как в Java); 
- Double; 
- Char; 
- Float; 
- Long; 
- Boolean. 

Но все же при преобразовании в JVM происходит оптимизация и некоторые объекты будут конвертированы в примитивы.

Например, когда объявляется переменная `val myAge: Int = 18`, Kotlin будет хранить фактическое значение (18) прямо в памяти как примитивное целое число, когда оно используется в беззнаковом контексте

### Диапозоны

```kotlin
1..10

1 until 10

10 douwntil 1

0..100 step 10
```

### vararg

```kotlin
fun main() {
  someFun(1, 2, 3, 4)
  someFun(*intArrayOf(1, 2, 3, 4), 5, 6, 7)
}


fun someFun(vararg numbers: Int) {
  // do something
}
```

### lateinit

Придуман для Dagger2, так как все зависимости приходилось сначала объявлять null, а потом перезаписывать и проверять на null

#### Где НЕ надо использовать 

- **В объявлении фрагмента** это приводит к багам и утечкам памяти, так как хранится ссылка на старые невидимые объекты, которым не был передан null после Destroy. Решается переведения фрагмента в nullable тип 
- **В асинхронных операциях**

### `const val` и `val`

`const val` - константная переменна времени компиляции. В JVM будет интерпритирована как статическая. Данный вид определения переменной заставляет компилятор встраивать значение константы во все места использования (inline). Использование константы даже не отображается в байт-коде, присутствует только значение константы.

`val` - переменная времени выполнения


### Пары

`data class Pair<out A, out B> : Serializable`
`Pair(first: A, second: B)`


Позволяет получать пары объектов

```kotlin
fun main() {
    val somelist = listOf("a" to 1, "b" to 3, "c" to 2)
    somelist.forEach {
        item -> 
        println(item.javaClass.kotlin)
    }
}
```

**Вывод**
```kotlin
class kotlin.Pair (Kotlin reflection is not available)
class kotlin.Pair (Kotlin reflection is not available)
class kotlin.Pair (Kotlin reflection is not available)
```