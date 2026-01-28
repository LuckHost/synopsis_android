!!после ревью структуры папок ссылки сбились

---
### Базовые вопросы

Нужно ли придерживаться SOLID-принципов? Почему да/нет? [solid принципы](/basics/principlesOfGoodCode/SOLID/About.md)

Какие паттерны использовали на практике? Приведите примеры.

Опишите паттерны MVP и MVVM. Какие из них использовали? Какой предпочитаете? Знаете ли/использовали другие паттерны? [MVP](/patterns/architecture/MVP.md) | [MVVM](/patterns/architecture/MVVM.md)

Почему слой Model должен быть отделен от View или Presenter?

Что такое инверсия зависимости (dependency inversion)? [DIP](/basics/principlesOfGoodCode/SOLID/DIP.md)

Объясните пример паттерна Singleton. Где его использовать в Android?

Объясните пример паттерна Observer. Где его использовать в Android? [Observer](/patterns/Observer.md)

Объясните пример паттерна Builder. Где его использовать в Android? [Builder](/patterns/Builder.md)

Как вы понимаете термин «архитектура приложения»? Зачем это вообще нужно? Почему инженеры пытаются усложнить процесс разработки и тратят время на проектирование архитектуры? Может, лучше сэкономить ресурсы и пойти по простому пути — держать весь код в одном файле?

Что такое иммутабельный объект? Для чего его используют? Как сделать иммутабельный объект в Java? [answer](/basics/syntaxJava/ImmutableObject.md)

MVP vs MVVM – в чем основное отличие?

**Алгоритмы**

Есть много алгоритмов сортировки. Возможно ли выбрать один самый быстрый и использовать его повсюду? Почему да/нет?

В чем сложность поиска произвольного элемента в ArrayList? В LinkedList? [answer](/basics/collections/List.md/#сравнение)

Какие алгоритмы используют в Android/Java коллекциях под капотом?

 

### Структуры данных

HashMap. Используете ли вы на практике? Если да, то зачем? Как она работает изнутри?

Какая разница между HashMap и LinkedHashMap?

Что такое бинарное дерево?

 

### Сохранение данных

Как бы вы реализовали сохранение зашифрованных данных в SharedPreferences? Базу данных?

Как реализовать миграцию таблицы, где нужно из non-nullable поля сделать nullable поле?


## Работа с сетью

Расскажите, какие методы можно применить в REST API? Зачем какой нужен? [answer](/basics/networkPatterns/REST.md)

Что можно использовать, кроме REST API, для работы с сервером? [GraphQL](/basics/networkPatterns/GraphQL.md)


### Многопоточность

Что такое Thread Pool? Каковы его особенности? [answer](/basics/syntaxJava/Asynchrony/ThreadPool.md)

Что такое Executor/ExecutorService? Какую задачу выполняют и как использовать? [answer](/basics/syntaxJava/Asynchrony/ExecutorAndExecutorService.md)

Какие есть виды Executor? [answer](/basics/syntaxJava/Asynchrony/ExecutorAndExecutorService.md)

Какая разница между методами start() и run() в классе Thread? [answer](/basics/syntaxJava/Asynchrony/Threads.md)

На что указывает ключевое слово synchronized? Какова его основная функция? [answer](/processAndStreaming/synchronized.md)

Модификатор volatile. Приходилось ли использовать? Зачем нужен? [answer](/basics/VolatileSynchronizedAtomic.md)

Знаете ли вы о таком понятии, как «эффект гонки» (race condition)? Как это предотвратить? Какие механизмы в Java для предотвращения этого?

Что такое атомарная операция?

Как остановить поток в Java? Можно ли продолжить выполнение потока после его остановки? [answer](/basics/syntaxJava/Asynchrony/ThreadsTermination.md)

Знаете ли вы о потокобезопасных коллекциях в Java/Android? Приходилось ли их использовать?

Какие стратегии можно применить, чтобы добиться потокобезопасности?

Какие варианты реализации потокобезопасности кода есть у Kotlin?

Как сделать переменную потокобезопасной?

Что такое Mutex и Monitor? Кто может выступать в роли монитора?

Что такое атомарные операции?

Почему инкрементация и операции с long не являются атомарными?

Какие классы атомарных переменных?

Что такое устаревшие данные (stale data)? Как избежать этого эффекта?

### Java Core

Механизм Generics. Какую проблему решает?

Что такое soft reference, weak reference?

Что такое сериализация объекта? Какую проблему она решает? Какие стандартные механизмы у Java?

Какой контракт существует между equals() и hashCode()?

По вашему мнению, почему строки в Java сделаны иммутабельными?

Можем ли мы задекларировать пустой интерфейс? Если да, то зачем?

​Что такое String pool? Зачем он нужен?

Что такое StringBuilder, какую проблему он решает?

Что такое Stack в JVM и какие данные там хранятся?

Что такое Heap в JVM и какие данные там хранятся?

Что такое garbage collector, как он вообще работает? Каковы реализации GC?

### Android SDK

Назовите основные изменения в версиях Android.

Как реализовать IPC в системе Android?

Как реализовать отложенную задачу?

Что такое Doze Mode? [answer](/android/features/dozeMode.md)

Что такое App Standby mode? [answer](/android/features/dozeMode.md/#app-standby)

Что такое AIDL и зачем он нужен? Какие типы данных поддерживаются? [answer](/android/aidl.md)

Что такое Multidex?

Что такое KeyStore API?

Что такое PendingIntent?

Как безопасно хранить user-sensitive данные?

Какие методы защиты приложения?

Что такое SSL/TLS Pinning? Как его реализовать в Android?

Что такое ViewBinding? [answer](/UI/XML/ViewBinding.md)

Для чего нужны методы onSaveInstanceState/onRestoreInstanceState? Что такое permissions? Как запросить permissions?

Что такое Intent? Что такое Explicit/Implicit Intent? Что такое Sticky Intent, Pending Intent?

Какие типы данных мы можем положить в Bundle?

В чем разница между Serializable и Parcelable? [answer](/android/appComponents/SerializableAndParcelabe.md)

Если фрагмент для работы нуждается во входных данных, каким образом будет правильно передать их фрагменту?

Что такое ViewModel? Какие ее свойства? 

Объясните работу ViewModel с Jetpack. Что такое ViewModelProviders, ViewModelProvider.Factory?

Что такое LiveData? Зачем её используете?

Какая связь между LiveData и LifecycleOwner?

Приведите пример LifecycleOwner?

Что такое Looper?

Использовали ли HaMeR фреймворк (Handler/Message/Runnable)? Для чего он?

Какую информацию содержит контекст? Какие типы контекста знаете?

Для чего используют Content Provider?

Что такое Data Binding? Что такое View?

Преимущества Fragments против View?

Как работает Content Provider?

Какая разница между Single Activity и Multiple Activity?

Какие виды Context знаете? Где какой использовать?

Объясните работу BroadcastReciever и его реализацию.

Зачем LocalBroadcastManager?

Для чего нужен MotionLayout?

Опишите, как реализовать анимацию в MotionLayout.

Как можно обнаружить проблемы в скорости UI и устранить их?

Расскажите о вариантах реализации custom view.

Что делают методы onMeasure, onLayout, onDraw во View?

Как воплотить анимацию при переходе между Activity-фрагментами?

Когда необходимо использовать foreground service вместо service?

Когда использовать workmanager, а когда service?

Есть ли у workmanager лимиты для выполнения работы?

Расскажите о Jetpack Compose. Зачем придумали основной принцип работы, как устроено?

Что такое WakeLock?

Что такое AlarmManager? Какие особенности работы?

### Kotlin

Чем отличается работа с Exceptions в Kotlin и Java?

Что такое платформенные типы?

Что такое нелокальный return?

Для чего нужны reified generics?

Какая разница между Unit, Any, Nothing?

Расскажите о функциях высшего порядка, лямбда, функциях, которые могут использоваться в качестве аргумента.

Что такое inline-модификатор? Noinline?

Какая разница между crossinline и noinline?

Какие типы конструкторов вы знаете?

Что такое Flow? Что такое SharedFlow? [Flow](/coroutines/Flow/About.md) | [SharedFlow](/coroutines/Flow/SharedFlow.md)

В чем разница методов run, let, apply, also, with, use? [scope functions](/basics/syntaxKotlin/scopeFunctions.md)

Что произойдет, если в классе переопределить метод hashCode следующим образом: override fun hashCode(): Int = Random.nextInt()? А если так: override fun hashCode(): Int = 1?

Расскажите о Flow. В чем разница между Hot и Cold Flow? [Flow](/coroutines/Flow/About.md) | [разница Hot и Cold](/processAndStreaming/HotAndColdObservables.md)

Что такое деструктурирующее объявление? Что нужно сделать, чтобы иметь возможность использовать его для своего класса? Какие проблемы могут возникнуть с таким объявлением?

Для чего использовать data class? Почему нельзя работать с обычным классом?

Приведите пример делегатов в Kotlin? 

Как реализовать кастомный делегат?

Объясните, как работает suspen-функция? Что такое continuation?

Как обрабатывать ошибки в Coroutines?

Что такое SupervisorJob и когда применяется?

Как остановить/отменить Coroutines?