# synchronized

[источник](https://metanit.com/java/tutorial/8.3.php)

При работе потоки нередко обращаются к каким-то общим ресурсам, которые определены вне потока, например, обращение к какому-то файлу. Если одновременно несколько потоков обратятся к общему ресурсу, то результаты выполнения программы могут быть неожиданными и даже непредсказуемыми. Например, определим следующий код:

```Java
public class Program {
 
    public static void main(String[] args) {
         
        CommonResource commonResource= new CommonResource();
        for (int i = 1; i < 6; i++){
             
            Thread t = new Thread(new CountThread(commonResource));
            t.setName("Thread "+ i);
            t.start();
        }
    }
}
 
class CommonResource {
     
    int x=0;
}
 
class CountThread implements Runnable {
 
    CommonResource res;
    CountThread(CommonResource res){
        this.res=res;
    }
    public void run(){
        res.x=1;
        for (int i = 1; i < 5; i++){
            System.out.printf("%s %d \n", Thread.currentThread().getName(), res.x);
            res.x++;
            try{
                Thread.sleep(100);
            }
            catch(InterruptedException e){}
        }
    }
}
```

Здесь определен класс CommonResource, который представляет общий ресурс и в котором определено одно целочисленное поле x.

Этот ресурс используется классом потока CountThread. Этот класс просто увеличивает в цикле значение x на единицу. Причем при входе в поток значение x=1:

>res.x=1;

То есть в итоге мы ожидаем, что после выполнения цикла res.x будет равно 4.

В главном классе программы запускается пять потоков. То есть мы ожидаем, что каждый поток будет увеличивать res.x с 1 до 4 и так пять раз. Но если мы посмотрим на результат работы программы, то он будет иным:
```
Thread 1 1 
Thread 2 1 
Thread 3 1 
Thread 5 1 
Thread 4 1 
Thread 5 6 
Thread 2 6 
Thread 1 6 
Thread 3 6 
Thread 4 6 
Thread 4 11 
Thread 2 11 
Thread 5 11 
Thread 3 11 
Thread 1 11 
Thread 4 16 
Thread 1 16 
Thread 3 16 
Thread 5 16 
Thread 2 16
```

То есть пока один поток не окончил работу с полем res.x, с ним начинает работать другой поток.

Чтобы избежать подобной ситуации, надо синхронизировать потоки. Одним из способов синхронизации является использование ключевого слова `synchronized`. Этот оператор предваряет блок кода или метод, который подлежит синхронизации. Для его применения изменим класс CountThread:

```Java
class CountThread implements Runnable{
 
    CommonResource res;
    CountThread(CommonResource res){
        this.res=res;
    }
    public void run(){
        synchronized(res){
            res.x=1;
            for (int i = 1; i < 5; i++){
                System.out.printf("%s %d \n", Thread.currentThread().getName(), res.x);
                res.x++;
                try{
                    Thread.sleep(100);
                }
                catch(InterruptedException e){}
            }
        }
    }
}
```
Монитор объекта - инструмент для управленрия доступа к нему.

Как только в рантайме выполнение доходит до `synchronized`, то монитор объекта, который в нем указан, блокируется, и на время блокировки доступ к нему имеет только один поток 

Новый вывод:
```
Thread 1 1 
Thread 1 2
Thread 1 3
Thread 1 4
Thread 3 1 
Thread 3 2
Thread 3 3
Thread 3 4
Thread 5 1 
Thread 5 2
Thread 5 3
Thread 5 4
Thread 4 1 
Thread 4 2
Thread 4 3
Thread 4 4
Thread 2 1 
Thread 2 2
Thread 2 3
Thread 2 4
```

Также можно применять `synchronized` к методу

```Java
class CommonResource{
     
    int x;
    synchronized void increment(){
        x=1;
        for (int i = 1; i < 5; i++){
            System.out.printf("%s %d \n", Thread.currentThread().getName(), x);
            x++;
            try{
                Thread.sleep(100);
            }
            catch(InterruptedException e){}
        }
    }
}
```