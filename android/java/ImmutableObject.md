# Immutable classes and objects

[источник](https://www.geeksforgeeks.org/create-immutable-class-java/)

Незменяемый класс в Java, это такой класс, объекты которого после создания изменить невозможно

Свойства такого объекта
- Объявлен как `final`
- Все элементы класса объявлены как `private final`
- Конструктор должен инициализировать все поля, выполняя глубокую копию, чтобы их нельзя было изменить с ссылкой на объект
- Глубокое копирование должно выполняться в геттерах
- Отсутствие сеттеров


Пример 
```Java
// Java Program to Create An Immutable Class

// Importing required classes
import java.util.HashMap;
import java.util.Map;

// Class 1
// An immutable class
final class Student {

    // Member attributes of final class
    private final String name;
    private final int regNo;
    private final Map<String, String> metadata;

    // Constructor of immutable class
    // Parameterized constructor
    public Student(String name, int regNo,
                   Map<String, String> metadata)
    {

        // This keyword refers to current instance itself
        this.name = name;
        this.regNo = regNo;

        // Creating Map object with reference to HashMap
        // Declaring object of string type
        Map<String, String> tempMap = new HashMap<>();

        // Iterating using for-each loop
        for (Map.Entry<String, String> entry :
             metadata.entrySet()) {
            tempMap.put(entry.getKey(), entry.getValue());
        }

        this.metadata = tempMap;
    }

    // Method 1 
    public String getName() { return name; }

    // Method 2 
    public int getRegNo() { return regNo; }
  
    // Note that there should not be any setters 

    // Method 3
    // User -defined type
    // To get meta data
    public Map<String, String> getMetadata()
    {

        // Creating Map with HashMap reference
        Map<String, String> tempMap = new HashMap<>();

        for (Map.Entry<String, String> entry :
             this.metadata.entrySet()) {
            tempMap.put(entry.getKey(), entry.getValue());
        }
        return tempMap;
    }
}

```

Попробуем изменить значения в мапе и посмотрим, что выйдет
```Java
// Class 2
// Main class
class GFG {

    // Main driver method
    public static void main(String[] args)
    {

        // Creating Map object with reference to HashMap
        Map<String, String> map = new HashMap<>();

        // Adding elements to Map object
        // using put() method
        map.put("1", "first");
        map.put("2", "second");

        Student s = new Student("ABC", 101, map);

        // Calling the above methods 1,2,3 of class1
        // inside main() method in class2 and
        // executing the print statement over them
        System.out.println(s.getName());
        System.out.println(s.getRegNo());
        System.out.println(s.getMetadata());

        // Uncommenting below line causes error
        // s.regNo = 102;

        map.put("3", "third");
        // Remains unchanged due to deep copy in constructor
        System.out.println(s.getMetadata());
        s.getMetadata().put("4", "fourth");
        // Remains unchanged due to deep copy in getter
        System.out.println(s.getMetadata());
    }
}

```

Вывод
```
ABC
101
{1=first, 2=second}
{1=first, 2=second}
{1=first, 2=second}
```

Значение параметра не поменялось

## Зачем нужен?

[источник](https://ru.stackoverflow.com/questions/579018/Зачем-нужны-immutable-объекты)

Представьте что Вы делаете программу для рисования, которая имеет функционал "пошаговый возврат действий". Но для того чтобы возвратится на шаг назад Вам нужно иметь слепок программы в тот самый момент. Поэтому Вы создаете такие компоненты, которые хранят свое состояние в объектах называемые ValueObject. После выполнения какго-то действия нужно пройтись по всей программе и собрать эти объекты, а после сохранить в каком-то хранилище, например массиве. Но тут есть одна проблема - в javascript не примитивные типы передаются по ссылкам. То есть Вы нарисовали первую фигуру и сохранили информационные модели в массив. После нарисовали вторую фигуру и тем самым все информационные модели изменились. Но изменились они не только в компонентах, а ещё и в тот самом массиве, в который мы сохранили данные после рисования первой фигуры. Все, программа сломалась!

Для решения этой проблемы и придумали подход в котором объекты каждый раз создаются новые и не изменяются по ходу изменения программы.

Еще очень часто приводят в пример удобство при отладки, но на мой взгляд современные отладчики делают тоже самое, показывая изменение программы по слоям. Удобство тестирования - это да, сильный аргумент, ведь можно воссоздать любой момент и быстрее разобраться в причинах бага. Отсутствие побочных эффектов - когда функция начинает работать с неизменяемыми объектами, она перестает изменять внешний код. Отсутствие утечек, на мой взгляд возможен только тогда, когда используется функциональный подход, в котором неизменяемость является фундаментальным принципом.