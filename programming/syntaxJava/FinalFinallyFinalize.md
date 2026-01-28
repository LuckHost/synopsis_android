# Final Finally Finalize

Это абсолютно разные вещи, но были объеденены, так как их легко перепутать, из-за чего вопрос про их различие часто звучит на собеседованиях 

# Final

Ключевое слово для класса, метода, переменной

`final` переменные нельзя изменять
`final` класс нельзя наследовать, расширять
`final` метод нельзя переопределить классом-наследником 

# Finally 

Это ключевое слово try/catch блока

Гарантирует выполнение кода внутри себя, даже если в try присутствует return или возникнет иключение 

```Java
try {
    System.out.println("Try block");
    throw new RuntimeException("demo");
}
finally
{
    System.out.println("Finally block");
}
```

# Finalize 

Это метод, который сборщик мусора всегда вызвает перед тем, как удалить объект

Стоит использовать с осторожностью и в крайних случаях. GC во время своей работы все объекты, которые имеют переопределенный finalize, собирает в очередь и выполняет их удаление в последнюю очередь.

Изначально определен у класса Object, где имеет пустую реализацию, так что в случае нужды каких-либо действий по очистке нужно переопределять этот метод

```Java
class Hello {
    public static void main(String[] args)
    {
        Hello s = new Hello();
        s = null;
 
        // Requesting JVM to call Garbage Collector method
        System.gc();
        System.out.println("Main Completes");
    }
 
    // Here overriding finalize method
    public void finalize()
    {
        System.out.println("finalize method overridden");
    }
}
```

Вывод:
```
finalize method overridden
Main Completes
```