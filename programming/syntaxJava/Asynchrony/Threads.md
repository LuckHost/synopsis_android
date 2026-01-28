# Threads 

[источник (18 год)](https://metanit.com/java/tutorial/8.2.php)
[более свежий источник (22год)](https://skillbox.ru/media/code/potoki-v-java-chto-eto-takoe-i-kak-oni-rabotayut/)

Для создания потока есть два способа

- Наследовать класс Thread
- Реализация интерфейса Runnable

### Наследование от класса Thread

#### Важно
Используется в редких случаях, если надо изменить поведение потока или доступ к методам. 

**Минусы метода:**
- нарушение Composition over Inheritance
- Нельзя запустить повторно
- Нельзя использовать пул потоков ThreadPoolExecutor
- В Java можно наследоваться только от одного класса

#### Реализация 
```Java
class JThread extends Thread {
      
    JThread(String name){
        super(name);
    }
      
    public void run(){
          
        System.out.printf("%s started... \n", Thread.currentThread().getName());
        try{
            Thread.sleep(500);
        }
        catch(InterruptedException e){
            System.out.println("Thread has been interrupted");
        }
        System.out.printf("%s fiished... \n", Thread.currentThread().getName());
    }
}
  
public class Program {
  
    public static void main(String[] args) {
          
        System.out.println("Main thread started...");
        new JThread("JThread").start();
        System.out.println("Main thread finished...");
    }
}
```

Здесь в классе `JThread` мы задаем конструктор, в котором вызываем базовый класс и кладем туда имя потока. Вообще в коструктор класса мы можем передать различные данные, но **главное, чтоб в нем вызывался базовый конструктор, куда будет передано имя потока**.

```Java
JThread(String name){
        super(name);
    }
```

В методе `run` и прописывается весь тот код, который будет выполнен на новом потоке.

Создание потока происходит в `Main`:
```Java 
new JThread("JThread").start();
```

Вывод
```
Main thread started...
Main thread finished...
JThread started... 
JThread finished...
```

### Ожидание завершения потока

Как правило нам нужно ожидать завершения потока, для этого существует оператор `.join()`

```Java
public static void main(String[] args) {
         
    System.out.println("Main thread started...");
    JThread t= new JThread("JThread ");
    t.start();
    try{
        t.join(); 
    }
    catch(InterruptedException e){
     
        System.out.printf("%s has been interrupted", t.getName());
    }
    System.out.println("Main thread finished...");
}
```
Поток, из которго вызывается новый поток, будет ожидать конца его выполнения.
В нашем случае вызываемый поток - Main

Вывод:
```
Main thread started...
JThread  started... 
JThread  finished... 
Main thread finished...
```

### Реализация интерфейса Runnable 

В данном случае мы реализуем интерфейс всего с одним методом: `run()`

```Java
class MyThread implements Runnable {
      
     
    public void run(){
          
        System.out.printf("%s started... \n", Thread.currentThread().getName());
        try{
            Thread.sleep(500);
        }
        catch(InterruptedException e){
            System.out.println("Thread has been interrupted");
        }
        System.out.printf("%s finished... \n", Thread.currentThread().getName());
    }
} 
  
public class Program {
  
    public static void main(String[] args) {
          
        System.out.println("Main thread started...");
        Thread myThread = new Thread(new MyThread(),"MyThread");
        myThread.start();
        System.out.println("Main thread finished...");
    }
}
```

Создание происходит через конструктор класса Thread, куда мы передаем наш класс и имя потока

```Java
Thread myThread = new Thread(new MyThread(),"MyThread");
```

Также мы можем представить все это в виде лямбда-выражения

```Java
public class Program {
  
    public static void main(String[] args) {
          
        System.out.println("Main thread started...");
        Runnable r = ()->{
            System.out.printf("%s started... \n", Thread.currentThread().getName());
            try{
                Thread.sleep(500);
            }
            catch(InterruptedException e){
                System.out.println("Thread has been interrupted");
            }
            System.out.printf("%s finished... \n", Thread.currentThread().getName());
        };
        Thread myThread = new Thread(r,"MyThread");
        myThread.start();
        System.out.println("Main thread finished...");
    }
}
```