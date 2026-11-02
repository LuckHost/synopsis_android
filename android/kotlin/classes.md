## open / final 

Изначально все классы в Kotlin - `final`, то есть от них нельзя наследоваться, для того, чтоб изменить это, нужно перед классом указать словао `open`

## abstract class 

Нельзя создать экземпляр, но можно унаследовать 

По своей сути - то же самое, что и интерфейс, но при этом может хранить в себе состояния, т.е. mutable переменные 

## data class

Data class — это специальный тип класса в языке программирования Kotlin, предназначенный для представления небольших, простых объектов данных.

От data класса в Kotlin нельзя наследоваться, так как он является final классом.
Однако data класс может наследоваться от других классов.

Преимущества дата-классов по сравнению с обычными классами:

- Автоматическое создание конструктора с параметрами, соответствующими именам свойств класса.
- Автоматическое создание методов equals(), hashCode() и toString(), что упрощает сравнение объектов и сериализацию.
- Автоматическое создание метода copy(), который позволяет создавать новый экземпляр data class с изменёнными значениями некоторых свойств.
- Data class можно использовать для представления следующих типов объектов

## sealed class

[источник](https://developer.alexanderklimov.ru/android/kotlin/sealed.php)

Альтернатива `enum`, за исключением, что `enum` - синглтоны с небольшим количеством полей, где каждый наследник обязан переопределить все переменные.

Все прямые подклассы должны быть вложены в суперкласс. Запечатанный класс не может иметь наследников, объявленных вне класса.

```kotlin
sealed class SealedClass {
    class One(val value: Int) : SealedClass()
    class Two(val x: Int, val y: Int) : SealedClass()
    
    fun eval(e: SealedClass): Int =
            when (e) {
                is SealedClass.One -> e.value
                is SealedClass.Two -> e.x + e.y
            }
}
```

## inline

Класс, который может иметь всего 1 параметр и будет удален в JVM, а его реализация будет вставлена вместо него

Такак как не будет происходить создание еще одного класса, то это хорошо оптимизирует систему

#### inline function 

тоже встравиается реализация функции при каждом вызове, используется в функциях высшего порядка, которые принимают к примеру лямбды

помимо этого инлайн функция может вызывать `return` родителя

## nested 

Такие классы могут быть определены в других классах. Еще их называют вложенными классами. Они обычно выполняют какую-то вспомогательную роль, а определение их внутри класса или интерфейса позволяет разместить их как можно ближе к тому месту, где они непосредственно используются.

**Пример**
```kotlin
class Person{
    class Account(val username: String, val password: String){
 
        fun showDetails(){
            println("UserName: $username  Password: $password")
        }
    }
}
```
[источник](https://metanit.com/kotlin/tutorial/4.7.php)

## inner

Тот же вложенный класс, но помимо этого имеет доступ к свойствам и функциям внешнего класса

## object

Это синглтон 

## companion object 

[источник](https://habr.com/ru/companies/funcorp/articles/430836/)

Синглтон, привязанный к классу

При переводе в Java превращается в класс со статическими методами и переменными

**Kotlin:**
```kotlin
class SimpleClassKotlin1 {

  companion object {
    var companionField = "Hello!"
    fun companionFun (vaue: String) { }
  }

  object OneMoreObject {
    var value = 1
    fun function() { }
  }
}
```

**Java**
```java
public final class SimpleClassKotlin1 {

  @NotNull
  private static String companionField = "Hello!";

  public static final SimpleClassKotlin1.Companion Companion = 
    new SimpleClassKotlin1.Companion((DefaultConstructorMarker)null);

  public static final class OneMoreObject {
    private static int value;
    public static final SimpleClassKotlin1.OneMoreObject INSTANCE;

    public final int getValue() {
      return value;
    }

    public final void setValue(int var1) {
      value = var1;
    }

    public final void function() { 
    }

    static {
      SimpleClassKotlin1.OneMoreObject var0 = new SimpleClassKotlin1.OneMoreObject();
      INSTANCE = var0;
      value = 1;
    }
  }

  public static final class Companion {
    @NotNull
    public final String getCompanionField() {
      return SimpleClassKotlin1.companionField;
    }

    public final void setCompanionField(@NotNull String var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      SimpleClassKotlin1.companionField = var1;
    }

    public final void companionFun(@NotNull String vaue) {
      Intrinsics.checkParameterIsNotNull(vaue, "vaue");
    }

    private Companion() {
    }

    // $FF: synthetic method
    public Companion(DefaultConstructorMarker $constructor_marker) {
      this();
    }
  }
}
```

## anonymous class

```kotlin
fun main() {
    // Анонимный класс для Comparator
    val comparator = object : Comparator<Int> {
        override fun compare(o1: Int, o2: Int): Int {
            return o1 - o2
        }
    }

    val result = comparator.compare(5, 3)
    println("Результат: $result")
}

```