## Гетеры и сеттеры

[источник](https://metanit.com/kotlin/tutorial/4.2.php)

Методы доступа не обязательны для определения, но все же для них есть свой синтаксис в Kotlin

```kotlin
var имя_свойства[: тип_свойства] [= инициализатор_свойства]
    [getter]
    [setter]
```

### Setter

В методах доступа мы можем поместить все что угодно, от условия до вызова функций
Пример сеттера в Kotin
```kotlin
var age: Int = 18
    set(value){
        if((value>0) and (value < 110))
            field = value
    }
 
fun main() {
 
    println(age)    // 18
    age = 45
    println(age)    // 45
    age = -345
    println(age)    // 45
}
```


### Getter

Геттер можно задать как в короткой форме
```kotlin
var age: Int = 18
    set(value){
        if((value>0) and (value <110))
            field = value
    }
    get() = field
```

Так и в длинной
```kotlin
var age: Int = 18
    set(value){
        println("Call setter")
        if((value>0) and (value <110))
            field = value
    }
    get(){
        println("Call getter")
        return field
    }
```

Также геттер можно сделать вычисляемым
```kotlin
fun main() {
    val tom = Person("Tom", "Smith")
    println(tom.fullname)   // Tom Smith
    tom.lastname = "Simpson"
    println(tom.fullname)   // Tom Simpson
}
class Person(var firstname: String, var lastname: String){
 
    val fullname: String
        get() = "$firstname $lastname"
}
```