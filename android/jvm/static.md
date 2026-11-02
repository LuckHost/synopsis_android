# Статика

[источник](https://habr.com/ru/companies/funcorp/articles/430836/)

а если точнее, то статические функции, классы, методы

Объект статического класса невозможно создать
### Java

[источник](https://www.geeksforgeeks.org/static-class-in-java/)
В Java статический класс может быть только вложенным

Такой класс не может иметь наследников и в наследниках внешнего класса его не будет

```Java


// Java program to Demonstrate How to
// Implement Static and Non-static Classes
 
// Class 1
// Helper class
class OuterClass {
 
    // Input string
    private static String msg = "GeeksForGeeks";
 
    // Static nested class
    public static class NestedStaticClass {
 
        // Only static members of Outer class
        // is directly accessible in nested
        // static class
        public void printMessage()
        {
 
            // Try making 'message' a non-static
            // variable, there will be compiler error
            System.out.println(
                "Message from nested static class: " + msg);
        }
    }
 
    // Non-static nested class -
    // also called Inner class
    public class InnerClass {
 
        // Both static and non-static members
        // of Outer class are accessible in
        // this Inner class
        public void display()
        {
 
            // Print statement whenever this method is
            // called
            System.out.println(
                "Message from non-static nested class: "
                + msg);
        }
    }
}
 
// Class 2
// Main class
class GFG {
 
    // Main driver method
    public static void main(String args[])
    {
 
        // Creating instance of nested Static class
        // inside main() method
        OuterClass.NestedStaticClass printer
            = new OuterClass.NestedStaticClass();
 
        // Calling non-static method of nested
        // static class
        printer.printMessage();
 
        // Note: In order to create instance of Inner class
        //  we need an Outer class instance
 
        // Creating Outer class instance for creating
        // non-static nested class
        OuterClass outer = new OuterClass();
        OuterClass.InnerClass inner
            = outer.new InnerClass();
 
        // Calling non-static method of Inner class
        inner.display();
 
        // We can also combine above steps in one
        // step to create instance of Inner class
        OuterClass.InnerClass innerObject
            = new OuterClass().new InnerClass();
 
        // Similarly calling inner class defined method
        innerObject.display();
    }
}
```

Вывод
```
Message from nested static class: GeeksForGeeks
Message from non-static nested class: GeeksForGeeks
Message from non-static nested class: GeeksForGeeks
```

### Kotlin
В Kotlin нет статики, как таковой, но есть вещи, которые ее заменяют

К примеру отдельно от всего написанная функция будет конвертирована в статическую при переходе в JVM

>Исходный код
```kotlin
package com.example.mytestapplication

fun testFun(){
    // some code
}
```
>Java 
```java
public final class JustFunKt {
  public static final void testFun() {
    // some code
  }
}
```

Для создания статических методов и переменных в Kotlin также есть синглтон, который инициализируется с помощью ключевого слова `object`

```kotlin
object MySingltoneClass {
// some code
}
```

Для любого класса в Kotlin мы можем создать объект-компаньон. Некий синглтон, привязанный к конкретному классу. Это можно сделать, используя совместно 2 ключевых слова `companion` и `object`:

```kotlin
class SimpleClassKotlin1 {

companion object{

   var companionField = "Hello!"

   fun companionFun (vaue: String){
       // some code
   }
}
}
```

В случае, если объект объявлен компаньоном, допускается не указывать его имя. Тогда ему автоматически присвоится имя Companion. Если нужно, мы можем получить инстанс класса-компаньона таким образом:

```kotlin
val companionInstance = 
    SimpleClassKotlin1.Companion
```

Однако, обращение к свойствам и методам класса-компаньона можно делать напрямую, через обращение класса, к которому он привязан:

```kotlin
SimpleClassKotlin1.companionField
SimpleClassKotlin1.companionFun("Hi!")
```

Декомпилированный код
```Java
public final class SimpleClassKotlin1 {

  @NotNull
  private static String companionField = "Hello!";

  public static final SimpleClassKotlin1.Companion Companion = new SimpleClassKotlin1.Companion((DefaultConstructorMarker)null);

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

Свойство объекта-компаньона представлено в виде статического поля нашего класса:

```Java
private static String companionField = "Hello!";
```

Однако это поле приватное и доступ к нему осуществляется через геттер и сеттер нашего класса компаньона, который здесь представлен в виде public static final class, а его инстанс представлен в виде константы:

```java
public static final SimpleClassKotlin1.Companion Companion = 
    new SimpleClassKotlin1.Companion(
        (DefaultConstructorMarker)null);
```
---
Попробуем осмыслить всё вышеописанное.

Если мы хотим реализовать статические поля или методы класса в Kotlin, то для этого следует воспользоваться `companion object`, объявленным внутри этого класса.
Вызов этих полей и функций из Kotlin будет выглядеть совершенно аналогично вызову статики в Java. А что будет, если мы попробуем вызвать эти поля и функции в Java?

### Java with Kotlin
...Автозаполнение подсказывает нам, что доступны следующие вызовы:

```java
SimpleClassKotlin1.Companion
    .companionFun("hello!");
SimpleClassKotlin1.Companion
    .setCompanionField("hello!");
SimpleClassKotlin1.Companion
    .getCompanionField();
```

То есть здесь мы никуда не денемся от прямого указания имени компаньона. Соответственно, здесь используется имя, которое присвоилось объекту-компаньону по умолчанию. Не очень удобно, так ведь?


Тем не менее, создатели Kotlin дали возможность сделать так, чтобы в Java это выглядело более привычно. И для этого есть несколько способов.

##### JvmField

```kotlin
@JvmField
var companionField = "Hello!"
```

Если применить эту аннотацию к полю companionField нашего объекта-компаньона, то при преобразовании байт-кода в Java увидим, что статическое поле companionField SimpleClassKotlin1 уже не private, а public, а в статическом классе Companion пропали геттер и сеттер для companionField. Теперь мы можем обращаться из Java-кода к companionField привычным образом.

#### lateinit

Второй способ — это указать для свойства объекта компаньона модификатор lateinit, свойства с поздней инициализацией. Не забываем, что это применимо только к var-свойствам, а его тип должен быть non-null и не должен быть примитивным. Ну и не забываем, про правила обращения с такими свойствами.

```kotlin
lateinit var lateinitField: String
```

#### const

И ещё один способ: мы можем объявить свойство объекта-компаньона константой, указав ему модификатор const. Несложно догадаться, что это должно быть val-свойство.

```kotlin
const val myConstant = "CONSTANT"
```

В каждом из этих случаев сгенерированный Java-код будет содержать привычное нам public static поле, в случае с const это поле будет ещё и final. Конечно, стоит понимать, что у каждого из 3х этих случаев есть своё логическое назначение, и только первый из них предназначен специально для удобства использования с Java, остальные получают эту «плюшку» как бы в нагрузку.

#### @JvmStatic

Если мы хотим, чтобы функция объекта-компаньона также преобразовалась в статический метод при генерации Java-кода, то для этого нам надо применить к этой функции аннотацию `@JvmStatic`.
Также допустимо применять аннотацию `@JvmStatic` к свойствам объектов-компаньонов (и просто объектов — синглтонов). В этом случае свойство не превратится в статическое поле, но будут сгенерированы статический геттер и сеттер к этому свойству.