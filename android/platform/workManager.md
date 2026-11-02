# WorkManager 

[источник](https://habr.com/ru/companies/simbirsoft/articles/553912/)

Популярная штука для ведения фоновых задач, которые не являются сценариями пользователя 

### Описание и добавление 

```kotlin
class MyWorker(context: Context, params: WorkerParameters) : Worker { 
   override fun doWork(): Result { 
       try {
   //Ваш код
       } catch (ex: Exception) {
          return Result.failure(); //или Result.retry() 
       }
       return Result.success()
   }
}
```

Код внутри метода doWork() будет выполнен в рабочем потоке WorkManager’a.

Далее задачу можно сделать разовой с помощью OneTimeWorkRequestBuilder.

```kotlin
val  myWorkRequest = OneTimeWorkRequestBuilder<MyWorker>().build()
```

Либо ее можно сделать периодической с помощью PeriodicWorkRequestBuilder.

```kotlin
val  myWorkRequest = 
    PeriodicWorkRequestBuilder<MyWorker>(30, TimeUnit.MINUTES, 25, TimeUnit.MINUTES).build()
```

### Критерии запуска задачи

Мы можем задать необходимые условия для запуска задачи:

```kotlin
val constraints = Constraints.Builder()
       .setRequiresCharging(true)
       .setRequiresBatteryNotLow(true)
       .setRequiredNetworkType(NetworkType.CONNECTED)
       .setRequiresDeviceIdle(true) 
       .setRequiresStorageNotLow(true) 
       .build()
```

После этого констрейнты добавляются в билдере work request’a. 

Рассмотрим перечисленные условия запуска:

- **setRequiresCharging** (boolean requiresCharging) — критерий: зарядное устройство должно быть подключено.

- **setRequiresBatteryNotLow** (boolean requiresBatteryNotLow) — критерий: уровень батареи не ниже критического (задача начинает выполняться при уровне заряда больше 20, а останавливается при значении меньше 16).

- **setRequiredNetworkType** (NetworkType networkType) — критерий: наличие интернета. Мы можем указать, какой именно тип сети интернет (NetworkType) должен быть при запуске задачи. Тип соединения с сетью может быть:

    - *CONNECTED* — WiFi или Mobile Data

    - *UNMETERD* — только WiFi

    - *METERED* — только Mobile Data

    - *NOT_ROAMING* — интернет не должен быть роуминговым;

    - *NOT_REQUIRED* — интернет не нужен.

- **setRequiresDeviceIdle** (boolean requiresDeviceIdle) — критерий: девайс не используется какое-то время и ушел “в спячку”. Работает на API 23 и выше.

- **setRequiresStorageNotLow** (boolean requiresStorageNotLow) — критерий: на девайсе должно быть свободное место, не меньше критического порога.

### Цепочки задач

WorkManager может выполнять несколько разовых задач последовательно друг за другом. При этом задача, для которой не выполнены критерии запуска, будет ожидать их соблюдения вместе с теми, что идут после нее. Если порядок задач не важен, можно запустить их параллельно.

```kotlin
//Параллельный запуск 3 задач
WorkManager.getInstance(context)
       .enqueue(myWorkRequest1, myWorkRequest2, myWorkRequest3)

//Последовательный запуск 3х задач
WorkManager.getInstance(context)
       .beginWith(myWorkRequest1)
       .then(myWorkRequest2)
       .then(myWorkRequest3)
       .enqueue()
```

```kotlin
//Первая последовательная цепочка
val chain12 = WorkManager.getInstance(context)
       .beginWith(myWorkRequest1)
       .then(myWorkRequest2);
 
//Вторая последовательная цепочка
val chain34 = WorkManager.getInstance(context)
       .beginWith(myWorkRequest3)
       .then(myWorkRequest4);
 
//Параллельный запуск 2 последовательных цепочек, затем 5 задача
WorkContinuation.combine(chain12, chain34)
       .then(myWorkRequest5)
       .enqueue();
```

**Уникальная цепочка задач**

Мы можем сделать последовательность задач уникальной. Для этого нужно начать последовательность методом beginUniqueWork():

```kotlin
WorkManager.getInstance(context)
       .beginUniqueWork("work123", ExistingWorkPolicy.REPLACE, myWorkRequest1)
       .then(myWorkRequest3)
       .then(myWorkRequest5)
       .enqueue();
```

В этот метод мы передали название уникальной цепочки, стратегию действий при коллизии цепочек с одинаковым именем и саму задачу (можно передать список задач).

Стратегий может быть несколько:

*REPLACE* – остановка последовательности с таким же именем, и запуск новой;

*KEEP* – оставит в работе текущую выполняемую последовательность, а новая будет проигнорирована;

*APPEND* – запустит новую последовательность после выполнения текущей.

### Отмена задачи

Для отмены задачи у класса WorkManager есть следующие методы:

- **cancelAllWork**() — отменяет все запланированные задачи (и не только вашим приложением);

- **cancelAllWorkByTag**(String tag) — отменяет все задачи с указанным тегом;

- **cancelUniqueWork**(String uniqueWorkName) — отменяет уникальную цепочку задач с указанным именем;

- **cancelWorkById**(UUID id) — отменяет задачу по указанному id.


--- 

# Далее - просто копипаст с инточника

да как и весь документ собственно..

6) Статусы задач
Статусы и жизненный цикл задачи может быть различным, в зависимости от того, разовая она или нет. Возможны следующие статусы задач:

ENQUEUED – задача запланирована;

RUNNING – задача выполняется;

SUCCEEDED (SUCCESS) – задача выполнена, терминальное состояние;

FAILED (FAILURE) – задача не была выполнена, не повторять, терминальное состояние;

RETRY – задача не была выполнена, повторить через некоторое время;

BLOCKED – задача включена в цепочку, и ее очередь выполнения еще не наступила;

CANCELLED – задача отменена, терминальное состояние.

Для разовой задачи возможен следующий флоу:


Источник

Если одна из разовых задач включена в цепочку и завершилась с состоянием FAILED, следующие за ней по цепочке задачи будут отменены.

Для периодической задачи существует другой флоу:


Источник

Как мы видим, для периодической задачи существует одно терминальное состояние: CANCELLED.

Так как WorkManager является частью Jetpack, получить информацию о задаче можно в виде LiveData:

WorkManager.getInstance(context)
   .getWorkInfoIdLiveData(myWorkRequest.id)
   .observe(this, {
         	Log.d(TAG, "onChanged: " + it.state);
})
7) Входные и выходные данные задачи
Мы можем задать входные данные, необходимые для ее работы.

val myData = Data.Builder()
       .putString("keyA", "value1")
       .putInt("keyB", 1)
       .build()
 
val myWorkRequest1 = new OneTimeWorkRequestBuilder<MyWorker>()
       .setInputData(myData)
       .build()
Когда задача будет запущена, мы сможем получить эти данные внутри нее с помощью:

val valueA = getInputData().getString("keyA", "")
val valueB = getInputData().getInt("keyB", 0)
После выполнения мы можем в Result.success() или Result.failure() передать выходные данные задачи.

8) Передача данных между задачами
При создании цепочки задач выходные данные одной задачи будут передаваться во входные данные другой. Рассмотрим такой пример. Пусть задачи myWorkRequest1 и myWorkRequest2 выполняются параллельно, затем выполняется myWorkRequest3. В результате выходные данные из первой и второй задач попадут в третью. 

WorkManager.getInstance(context)
       .beginWith(myWorkRequest1, myWorkRequest2)
       .then(myWorkRequest3)
       .enqueue()

//Данные из 1 задачи
val output = Data.Builder()
       .putString("keyA", "value1")
       .putInt("keyB", 1)
       .build()
Result.success(output)
//Данные из 2 задачи
val output = Data.Builder()
       .putString("keyA", "value2")
       .putInt("keyB", 2)
       .build()
Result.success(output)
Так как мы задали одинаковые ключи, невозможно предположить, какое именно значение попадет в третью задачу, ведь первая и вторая задача выполняются параллельно. Важно об этом помнить.

9) InputMerger
Для преобразования нескольких выходных результатов в один входной используются реализации класса InputMerger. По умолчанию используется OverwritingInputMerger, перезаписывающий значение по уже существующему ключу. Это было видно на предыдущем примере с двумя параллельными задачами. Если же нам нужно при совпадении ключа записать все пришедшие значения, следует использовать ArrayCreatingInputMerger. 

InputMerger можно задать при создании задачи. Добавим ArrayCreatingInputMerger для myWorkRequest3 из предыдущего примера.

val myWorkRequest3 = new OneTimeWorkRequestBuilder<MyWorker>()
       .setInputMerger(ArrayCreatingInputMerger.class)
       .build()

 //Получаем данные:
val valueA = getInputData().getStringArray("keyA")
val valueB = getInputData().getIntArray("keyB")
val valueC = getInputData().getStringArray("keyC")
val valueD = getInputData().getStringArray("keyD")
Теперь по ключу keyA мы получим не последнее записанное значение, а массив ["value1", "value2"]. Также и для ключа keyB — [1, 2].

10) Кастомная конфигурация WorkManager
WorkManager имеет собственный провайдер, WorkManagerInitializer, который мержится в манифест приложения. Он создает пул потоков, необходимый для работы, фабрики для создания InputMerger’ов и WorkerFactory (создает объекты Worker’ов, полезна в случаях, когда класс Worker’a получил новое имя, и WorkManager должен соотнести действия, запланированные на старое имя, с новым). При необходимости эти параметры можно изменить.

Для начала нужно отключить стандартный провайдер WorkManager’a. Это делается в манифесте приложения путем объявления.

<provider android:authorities=”${applicationId}.workmanager-init”
android:name=”androidx.work.impl.WorkManagerInitializer”
tools.node=”remove” />
После этого нужно сделать класс нашего приложения реализацией интерфейса Configuration.Provider. И в переопределенном методе изменить нужные нам параметры. Например, заменим стандартный Executor, в потоках которых выполняется код Worker’ов, и изменим минимальный уровень логирования:

class TestProjectApplication : Application(), Configuration.Provider {
   override fun getWorkManagerConfiguration(): Configuration {
       return Configuration.Builder()
           .setExecutor(Executors.newFixedThreadPool(5))
           .setMinimumLoggingLevel(Log.DEBUG)
           .build()
   }
}
11) Тестирование
Для тестирования Worker’ов нужно подключить в проект зависимость

androidTestImplementation "androidx.work:work-testing:$work_version"
В этом пакете есть классы, которые помогают в тестовой среде. 

Пусть у нас есть Worker, который складывает 2 числа и выдает результат в качестве выходных данных. Протестируем его:

@Test
fun testAdditionWorker() {
   // создаем входные данные для теста
   val inputData = workDataOf("first" to 1, "second" to 2)
  // создаем объект Worker’a
   val worker = TestListenableWorkerBuilder<MyWorker>(context, inputData).build()

   // Получаем результат
   val result = worker.doWork()

   // Проверяем, что работа завершилась успешно и выдала нужный результат
   assertTrue(result is Success)
   assertEquals((result as Success).outputData.getInt("result", 0), 4)
}
Дополнительно можно проверить, что код Worker’a выполняется только тогда, когда выполнены все условия его запуска. А также проверить статус работы. 

Пусть у нас есть тот же Worker, выполняющий сложение 2 чисел. Зададим ему условия старта и протестируем.

WorkManagerTestInitHelper.initializeTestWorkManager(context)
val testDriver = WorkManagerTestInitHelper.getTestDriver(context)!!
val workManager = WorkManager.getInstance(context)
val constraints = Constraints.Builder()
   .setRequiredNetworkType(NetworkType.UNMETERED)
   .setRequiresCharging(true)
   .build()
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
   .setConstraints(constraints)
   .build()

// синхронно в рамках тестовой среды получаем результат выполнения
workManager.enqueue(workRequest).result.get()
// задаем необходимые условия запуска работы
testDriver.setAllConstraintsMet(workRequest.id)

// получаем статус работы
val workInfo = workManager.getWorkInfoById(workRequest.id).get()
assertEquals(workInfo.state, WorkInfo.State.SUCCEEDED)
Заключение
WorkManager предоставляет широкие возможности для асинхронной работы, которая должна выполняться в определенном состоянии устройства, откладывать ее, планировать, выполнять параллельно, перезапускать, запускать периодически, работать в многопроцессном режиме. Также этот инструмент гарантирует выполнение задачи даже после закрытия приложения и перезагрузки устройства. А его доступность, начиная с API 14, делает его must-have инструментом для подобного рода задач.