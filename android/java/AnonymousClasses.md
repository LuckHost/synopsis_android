# Anonymous Classes

Класс, который можно объявить прямо внутри другого класса, который имеет всего одну реализацию и не имеет названия (из-за этого и анонимный)


Пример 
```Java
import java.util.Comparator;

public class Main {
    public static void main(String[] args) {
        // Анонимный класс для интерфейса Comparator
        Comparator<Integer> comparator = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1 - o2;
            }
        };

        int result = comparator.compare(5, 3);
        System.out.println("Результат: " + result);
    }
}
```

В Java 8+ лямбда-выражения часто заменяют анонимные классы для функциональных интерфесов