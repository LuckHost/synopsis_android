!!после ревью структуры папок ссылки сбились

---

# Базовые вопросы

Какая разница между абстрактным классом и интерфейсом?

Что такое алгоритм и как выбрать правильный?

Что такое сложность алгоритма? Как и с помощью чего её можно вычислить?

Что такое нотация big-O?

Какие алгоритмы сортировки вы знаете? [answer](/abstract_things/algorithms/sort/About.md)

## Структуры данных
Расскажите о таких структурах данных, как List, Set, Map? [answer](/basics/collections/About.md)

Какая разница между ArrayList и LinkedList? [answer](/basics/collections/List.md)

Из каких компонентов состоит библиотека Room? [answer](/libraries/SQL/room/About.md/#компоненты)

Что такое @PrimaryKey, @Ignore, @Embedded, @TypeConverters в Room? [answer](/libraries/SQL/room/Annotations.md)

Для чего нужна миграция в базах данных?

Что такое процесс? [answer](/processAndStreaming/About.md)

Что такое поток? [answer](/processAndStreaming/About.md)

Для чего используют ключевое слово synchronized? [answer](/processAndStreaming/synchronized.md)

Зачем синхронизировать потоки? [описание проблемы](/processAndStreaming/synchronized.md)

Какая разница между синхронным и асинхронным исполнением? [answer](/processAndStreaming/AsyncAndSync.md)

Как мы можем создать поток в Java? [answer](/basics/syntaxJava/Asynchrony/Threads.md)

Что такое deadlock?[answer](/processAndStreaming/Deadlock.md)

Какие варианты реализации многопоточности есть в Android? [answer](/processAndStreaming/AsyncAndSync.md/#асинхронное-програмирование)

Что такое main thread? Какие операции нужно выполнять на main thread, а какие нельзя делать?

# Java 

Что такое Exceptions? Зачем они нужны? [answers](/basics/syntaxJava/Exceptions.md)

Зачем используют ключевые слова final, finally и finalize? [answer](/basics/syntaxJava/FinalFinallyFinalize.md)

Что такое абстрактный класс? Что такое интерфейс?

Что такое анонимный класс? Использовали ли на практике? Для чего? [answer](/basics/syntaxJava/AnonymousClasses.md)

Что такое статический класс (static class)?

Что такое enum? Зачем его используют?

Можем ли мы сделать конструктор приватным?
`Да, можем, для того, чтоб сделать создание класса извне невозможным. System и Math обладают таким свойством` 

Какая разница между ключевыми словами throw и throws?
`throw - выбросить исключение. throws - ключевое слово, которое предупреждает о том, что метод может выбрасывать исключения`

Какая разница между Error и Exception? [answers](/basics/syntaxJava/Exceptions.md)

Какая разница между checked и unchecked exception? [answers](/basics/syntaxJava/Exceptions.md)

Что такое Object class и какие методы он имеет? [answers](/basics/syntaxJava/Object.md)

Какие существуют модификаторы доступа для классов? Какая разница между ними? [answer](/basics/visibilityModifiers.md)

Что такое итератор? [answer](/basics/collections/iterator.md)

Как безопасно удалить элемент из коллекции? [answer](/basics/collections/iterator.md/#безопасный-способ-удаления-элемнтов)

Зачем нам переопределять equals() и когда не нужно это делать? [answer](/basics/HashCodeAndEquals.md)

Какой должен выполняться контракт при переопределении equals()? [answer](/basics/HashCodeAndEquals.md)

# RxJava

В чем разница между map() и flatMap() в RxJava? [answer](/libraries/RxJava/MapAndFlatMap.md)

Когда используете observeOn(), а когда subscribeOn()? [answer](/libraries/RxJava/About.md)

Как можно обработать ошибки в RxJava? [answer](/libraries/RxJava/Exceptions.md)

Какие schedulers знаете в RxJava? Назовите их отличия. [answer](/libraries/RxJava/Schedulers.md)

Что такое Disposable? Зачем его используют? [answer](/libraries/RxJava/Disposable.md)

В чем разница между Hot и Cold Observables? Назовите примеры в RxJava. [answer](/processAndStreaming/HotAndColdObservables.md)

# Android SDK

Какие базовые Android-компоненты можете назвать?
- [Broadcast Receiver](/android/appComponents/components/broadcastReceiver.md)
- [Services](/android/appComponents/components/Service.md)
- [Activity](/android/appComponents/components/Activity.md)
- [Content Provider](/android/appComponents/components/ContentProvider.md)

Что такое ContentProvider? [answer](/android/appComponents/components/ContentProvider.md)

Какие типы Service знаете? [answer](/android/appComponents/components/Service.md)

Что такое BroadcastReceiver и какие типы существуют? [answer](/android/appComponents/components/broadcastReceiver.md)

Опишите [жизненный цикл Activity](/android/appComponents/activityLifecycle.md).

Опишите [жизненный цикл Fragment](/UI/XML/Fragments/LifeCycle.md).

Что такое изменение конфигурации? Что происходит с приложением на Android при этом? [answer](/android/appComponents/AndroidManifest.md/#конфигурация-и-ее-изменение)

Что такое Intent? Что такое explicit/implicit Intent? [answer](/android/appComponents/intent.md)

Что такое ANR? Как избегать таких ситуаций? [answer](/processAndStreaming/About.md/#anr)

Что такое DataBinding? [answer](/UI/XML/DataBinding.md)

Что такое LiveData? Какие виды знаете? [answer](/libraries/LiveData/LiveData.md)

Расскажите, что нужно реализовать, чтобы отобразить список строк в RecyclerView. [answer](/UI/XML/RecyclerView/About.md)

Объясните паттерн ViewHolder. Для чего он применяется? [answer](/UI/XML/RecyclerView/ViewHolder.md)

Что такое DiffUtil? [answer](/UI/XML/RecyclerView/DiffUtil.md)

Для чего используют Group, Guideline, Barriers, Chains в ConstraintLayout? [answer](/UI/XML/Layouts/ConstraintLayout.md)

Что такое WorkManager? Когда используем? [answer](/android/workManager.md)

# Kotlin 

Как задекларировать getter/setter для property? [answer](/basics/syntaxKotlin/gettersAndSetters.md)

Почему классы Kotlin по умолчанию final? 

Какая разница между sealed class и enum?

Почему у Kotlin нет checked exceptions? 

Что такое перегрузка операторов (operator overloading)? Зачем нужен этот механизм?

Как работают примитивы в Kotlin? [answer](/basics/syntaxKotlin/kotlin.md/#примитивы)

Чем отличается const val от val? [answer](/basics/syntaxKotlin/kotlin.md/#const-val-и-val)

Зачем нужны Coroutines? Чем они лучше обычных тредов? [answer](/coroutines/About.md)

Что такое suspend-функция?

Что такое Job? [answer](/coroutines/launchAndJob.md)

Что такое Dispatcher? Какие есть виды? [answer](/coroutines/Dispatcher.md)

Что такое Scope? [answer](/coroutines/Scope.md)

Как писать Java compatible API в Kotlin? [answer](/basics/syntaxKotlin/javaCompatibleAPI.md)

# Другое

Расскажите, что такое memory leak. Как избежать?

Как бы вы искали memory leak?

Расскажите о Dependency injection. Какие варианты реализации в Android?

Для чего нужна система контроля версий?

Что такое Git?

Для чего используем .gitignore-файл?

Расскажите о командах push, pull, fetch в Git?

Что такое merge и rebase? Какая разница?

Что такое CI? Зачем используем?