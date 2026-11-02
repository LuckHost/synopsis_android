# Завершение потоков 

[источник](https://metanit.com/java/tutorial/8.4.php)

Как правило поток заврешается, когда выполнил свою последнюю операцию (если это не пул потоков). Но бывает такое, что в потоке находится бесконечный цикл, для общения с сервером, к примеру. В этом случае нам нужен механизм завершения потока

#### Использование логической переменной

```Java
class MyThread implements Runnable {
      
    private boolean isActive;
      
    void disable(){
        isActive=false;
    }
      
    MyThread(){
       isActive = true;
    }
      
    public void run(){
          
        System.out.printf("%s started... \n", Thread.currentThread().getName());
        int counter=1; // счетчик циклов
        while(isActive){
            System.out.println("Loop " + counter++);
            try{
                Thread.sleep(400);
            }
            catch(InterruptedException e){
                System.out.println("Thread has been interrupted");
            }
        }
        System.out.printf("%s finished... \n", Thread.currentThread().getName());
    }
}
```

Здесь мы просто прерываем бесконечный цикл, меняя переменную класса `isActive`

#### interrupt (рекомендуемый)

Метод `interrupt()` устанавливает у потока статус, что он прерван. При этом метод возвращает true, если поток может быть прерван и false - если нет

Сам метод поток не завершает, только устанавливает статус. В частости `isInterrupted()` будет возвращать true

```Java
class MyThread implements Runnable {
      
    public void run(){
          
        System.out.printf("%s started... \n", Thread.currentThread().getName());
        int counter=1; // счетчик циклов
        while(!Thread.currentThread().isInterrupted()){
             
            System.out.println("Loop " + counter++);
        }
        System.out.printf("%s finished... \n", Thread.currentThread().getName());
    }
}
public class Program {
  
    public static void main(String[] args) {
          
        System.out.println("Main thread started...");
        MyThread myThread = new MyThread();
        Thread t = new Thread(myThread,"MyThread"); 
        t.start();
        try{
            Thread.sleep(150);
            t.interrupt();
              
            Thread.sleep(150);
        }
        catch(InterruptedException e){
            System.out.println("Thread has been interrupted");
        }
        System.out.println("Main thread finished...");
    }
}
```

Стоит заметить, что при обработке `InterruptedException` в catch статус потока сбрасывается, после чего `isInterrupted` будет возвращать false

Поэтому мы можем либо заново вызывать `interrupt()`

```Java
...
catch(InterruptedException e){
    System.out.println(getName() + " has been interrupted");
    System.out.println(isInterrupted());    // false
    interrupt();    // повторно сбрасываем состояние
}
...
```

Либо сразу выходить из цикла

```Java
...
while(!isInterrupted()){
         
    System.out.println("Loop " + counter++);
    try{
        Thread.sleep(100);
    }
    catch(InterruptedException e){
        System.out.println(getName() + " has been interrupted");
             
        break;  // выход из цикла
    }
}
...
```