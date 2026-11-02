## Executor

Это интерфейс, который определяет одну задачу: выполнение задачи (Runnable). Позволяет передавать задачу без конкретной реализации

Пример реализации
```Java
Thread 
Executors.newSingleThreadExecutor()
```

## ExecutorService

Расширение Executor, которое позволяет использовать инструменты управления жизненным циклом пула потоков, выполнения задач с возвратом результата (Callable, Runnable)

Пример реализации
```Java
Executors.newFixedThreadPool()
Executors.newCachedThreadPool()
```

### Таблица сравнения

| **Характеристика**       | **Executor**                             | **ExecutorService**                   |
|--------------------------|------------------------------------------|---------------------------------------|
| **Назначение**           | Базовый интерфейс для выполнения задач. | Расширенный интерфейс с доп. функциями. |
| **Выполнение задач**     | Только `Runnable`.                      | Поддержка `Runnable` и `Callable`.   |
| **Возврат результата**   | Нет.                                    | Да, через `Future`.                  |
| **Управление потоками**  | Не поддерживает.                        | Методы управления жизненным циклом.  |



## Виды Executor

- **Single Thread Executor**
    - Создает единственный поток, где и выполняет все задачи
    - `ExecutorService executor = Executors.newSingleThreadExecutor();`
- **Fixed Thread Pull**
    - Создает пул с фиксированным количеством потоков
    - `ExecutorService executor = Executors.newFixedThreadPool(nThreads);`
- **Cached Thread Pull**
    - Динамически создает потоке в пуле. Если их недостаточно, то создает новый, если поток простаивает 60 секунд - удаляет его
    - `ExecutorService executor = Executors.newCachedThreadPool();`
- **Scheduled Thread Pool**
    - Создает пул потоков для выполнения задач с задержкой или периодичностью
    - `ScheduledExecutorService executor = Executors.newScheduledThreadPool(nThreads);`
- **Single Thread Scheduled Thread**
    - Аналог Scheduled Thread Pool, но с одним потоком
    - `ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor();`
- **Work-Stealing Pool** (Java 8+)
    - Использует work-stealing algorithm. Пул имеет изнчанльно количество потоков, равное количеству ядер ЦП. Потоки динамически распределяют задачи для оптимизации 
    - `ExecutorService executor = Executors.newWorkStealingPool();`
