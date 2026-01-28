# Delegats

источники:
- [раз](https://metanit.com/kotlin/tutorial/5.6.php)
- [два](https://kotlinlang.ru/docs/delegated-properties.html)

Способ передачи ответственности другому классу

Пример использования делегата

```kotlin
import kotlin.reflect.KProperty
 
fun main() {
 
    val tom = Person("Tom", 37)
    println(tom.age)    //37
    tom.age = 38
    println(tom.age)    //38
    tom.age = -139
    println(tom.age)    //38
 
}
class Person(val name: String, _age: Int){
    var age: Int by LoggerDelegate(_age)
}
class LoggerDelegate(private var personAge: Int) {
    operator fun getValue(thisRef: Person, property: KProperty<*>): Int{
        return personAge
    }
    operator fun setValue(thisRef: Person, property: KProperty<*>, value: Int){
        println("Устанавливаемое значение: $value")
        if(value > 0 && value < 110) personAge = value
    }
}
```

## Стандратные делегаты

### lazy

Первый вызов `get()` запускает лямбда-выражение, переданное `lazy()` в качестве аргумента, и запоминает полученное значение, а последующие вызовы просто возвращают вычисленное значение.

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main() {
    println(lazyValue)
    println(lazyValue)
}
```
**Вывод:**
```
computed!
Hello
Hello
```

---
# p.s. 
#### все ниже просто скопировано с сайта, но не изучено мной, так как мне сейчас нужно понимание на поверхностном уроване 

Observable свойства
Функция Delegates.observable() принимает два аргумента: начальное значение свойства и обработчик (лямбда).

Обработчик вызывается каждый раз при изменении свойства (после выполнения задания). У обработчика три параметра: описание свойства, которое изменяется, старое значение и новое значение.

import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main() {
    val user = User()
    user.name = "first"
    user.name = "second"
}
Этот код выведет:

<no name> -> first
first -> second

Делегирование другому свойству
Если Вам нужно иметь возможность запретить присваивание некоторых значений, используйте функцию vetoable() вместо observable(). Обработчик, переданный vetoable, будет вызван перед присвоением нового значения свойства.

Свойство может делегировать свои геттеры и сеттеры другому свойству. Такое делегирование доступно как для свойств верхнего уровня, так и для свойств класса (членам и расширениям). Свойство делегата может быть:

свойством высшего уровня,
членом или свойством расширения того же класса,
член или свойством расширения другого класса.
Чтобы делегировать свойство другому свойству, используйте квалификатор :: в имени делегата, например, this::delegate или MyClass::delegate.

var topLevelInt: Int = 0
class ClassWithDelegate(val anotherClassInt: Int)

class MyClass(var memberInt: Int, val anotherClassInstance: ClassWithDelegate) {
    var delegatedToMember: Int by this::memberInt
    var delegatedToTopLevel: Int by ::topLevelInt

    val delegatedToAnotherClass: Int by anotherClassInstance::anotherClassInt
}
var MyClass.extDelegated: Int by ::topLevelInt
Это может быть полезно, например, когда вы хотите переименовать свойство обратно совместимым способом: введите новое свойство, пометьте старое аннотацией @Deprecated и делегируйте его реализацию.

class MyClass {
   var newName: Int = 0
   @Deprecated("Use 'newName' instead", ReplaceWith("newName"))
   var oldName: Int by this::newName
}
fun main() {
   val myClass = MyClass()
   // Уведомление: 'oldName: Int' устарело.
   // Используйте 'newName'
   myClass.oldName = 42
   println(myClass.newName) // 42
}

Хранение свойств в ассоциативном списке
Один из самых частых сценариев использования делегированных свойств заключается в хранении свойств в ассоциативном списке (map). Это полезно в “динамическом” коде, например, при работе с JSON. В этом случае вы можете использовать сам экземпляр ассоциативного списка в качестве делегата для делегированного свойства.

class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
В этом примере конструктор принимает ассоциативный список:

val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
Делегированные свойства берут значения из этого ассоциативного списка с помощью строковых ключей, которые связаны с именами свойств.

println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
Также, если вы используете MutableMap вместо read-only Map, поддерживаются изменяемые свойства var.

class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}

Локальные делегированные свойства
Вы можете объявить локальные переменные как делегированные свойства. Например, вы можете сделать локальную переменную ленивой.

fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
Переменная memoizedFoo будет вычислена только при первом обращении к ней. Если условие someCondition будет ложно, значение переменной не будет вычислено вовсе.


Требования к делегированным свойствам
Для read-only свойства (например val), делегат должен предоставлять функцию getValue, которая принимает следующие параметры:

thisRef — должен иметь такой же тип или быть родителем хозяина свойства (для расширений — тип, который расширяется);
property — должен быть типа KProperty<*> или его родительского типа.
getValue() должна возвращать значение того же типа, что и свойство (или его родительского типа).

class Resource

class Owner {
    val valResource: Resource by ResourceDelegate()
}

class ResourceDelegate {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return Resource()
    }
}
Для изменяемого свойства (var) делегат должен дополнительно предоставлять функцию setValue, которая принимает следующие параметры:

thisRef — должен иметь такой же тип или быть родителем хозяина свойства (для расширений — тип, который расширяется);
property — должен быть типа KProperty<*> или его родительского типа;
value — должен быть того же типа, что и свойство (или его родительский тип).
class Resource

class Owner {
    var varResource: Resource by ResourceDelegate()
}

class ResourceDelegate(private var resource: Resource = Resource()) {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return resource
    }
    operator fun setValue(thisRef: Owner, property: KProperty<*>, value: Any?) {
        if (value is Resource) {
            resource = value
        }
    }
}
Функции getValue() и/или setValue() могут быть предоставлены либо как члены класса-делегата, либо как его расширения. Последнее полезно когда вам нужно делегировать свойство объекту, который изначально не имеет этих функций. Обе эти функции должны быть отмечены с помощью ключевого слова operator.

Вы можете создавать делегатов как анонимные объекты без создания новых классов, используя интерфейсы ReadOnlyProperty и ReadWriteProperty из стандартной библиотеки Kotlin. Они предоставляют необходимые методы: getValue() объявлен в ReadOnlyProperty; ReadWriteProperty расширяет его и добавляет setValue(). Это значит, что вы можете передавать ReadWriteProperty всякий раз, когда ожидается ReadOnlyProperty.

fun resourceDelegate(): ReadWriteProperty<Any?, Int> =
    object : ReadWriteProperty<Any?, Int> {
        var curValue = 0 
        override fun getValue(thisRef: Any?, property: KProperty<*>): Int = curValue
        override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
            curValue = value
        }
    }

val readOnly: Int by resourceDelegate()  // ReadWriteProperty неизменяемое
var readWrite: Int by resourceDelegate()

Правила преобразования для делегированных свойств
Под правилами преобразования (ориг.: translation rules) здесь понимаются правила, по которым компилируются делегированные свойства.

Для каждого делегированного свойства компилятор Kotlin “за кулисами” генерирует вспомогательное свойство и делегирует его. Например, для свойства prop генерируется скрытое свойство prop$delegate, и исполнение геттеров и сеттеров просто делегируется этому дополнительному свойству.

class C {
    var prop: Type by MyDelegate()
}

// этот код генерируется компилятором:
class C {
    private val prop$delegate = MyDelegate()
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
Компилятор Kotlin предоставляет всю необходимую информацию о prop в аргументах: первый аргумент this ссылается на экземпляр внешнего класса C и this::prop reflection-объект типа KProperty, описывающий сам prop.


Правила преобразования при делегировании другому свойству
При делегировании другому свойству компилятор Kotlin создает непосредственный доступ к указанному свойству. Это означает, что компилятор не генерирует поле prop$delegate. Такая оптимизация помогает экономить память.

Возьмем, к примеру, следующий код:

class C<Type> {
    private var impl: Type = ...
    var prop: Type by ::impl
}
Геттеры и сеттеры свойств переменной prop напрямую вызывают переменную impl, пропуская операторы делегированного свойства getValue и setValue, и, следовательно, ссылочный объект KProperty не требуется.

Для кода выше компилятор генерирует следующий код:

class C<Type> {
    private var impl: Type = ...

    var prop: Type
        get() = impl
        set(value) {
            impl = value
        }
    
    fun getProp$delegate(): Type = impl // Этот метод нужен только для рефлексии
}

Предоставление делегата
С помощью определения оператора provideDelegate вы можете расширить логику создания объекта, которому будет делегировано свойство. Если объект, который используется справа от by, определяет provideDelegate как член или как расширение, эта функция будет вызвана для создания экземпляра делегата.

Один из возможных случаев использования provideDelegate — это проверка состояния свойства при его инициализации.

Например, если вы хотите проверить имя свойства перед связыванием, вы можете написать что-то вроде следующего:

class ResourceDelegate<T> : ReadOnlyProperty<MyUI, T> {
    override fun getValue(thisRef: MyUI, property: KProperty<*>): T { ... }
}

class ResourceLoader<T>(id: ResourceID<T>) {
    operator fun provideDelegate(
            thisRef: MyUI,
            prop: KProperty<*>
    ): ReadOnlyProperty<MyUI, T> {
        checkProperty(thisRef, prop.name)
        // создание делегата
        return ResourceDelegate()
    }

    private fun checkProperty(thisRef: MyUI, name: String) { ... }
}

class MyUI {
    fun <T> bindResource(id: ResourceID<T>): ResourceLoader<T> { ... }

    val image by bindResource(ResourceID.image_id)
    val text by bindResource(ResourceID.text_id)
}
provideDelegate имеет те же параметры, что и getValue:

thisRef — должен иметь такой же тип, или быть родителем хозяина свойства (для расширений — тип, который расширяется)
property — должен быть типа KProperty<*> или его родительского типа.
Метод provideDelegate вызывается для каждого свойства во время создания экземпляра MyUI, и сразу совершает необходимые проверки.

Не будь этой возможности внедрения между свойством и делегатом, для достижения той же функциональности вам бы пришлось передавать имя свойства явно, что не очень удобно.

// Проверяем имя свойства без "provideDelegate"
class MyUI {
    val image by bindResource(ResourceID.image_id, "image")
    val text by bindResource(ResourceID.text_id, "text")
}

fun <T> MyUI.bindResource(
        id: ResourceID<T>,
        propertyName: String
): ReadOnlyProperty<MyUI, T> {
    checkProperty(this, propertyName)
    // создание делегата
}
В сгенерированном коде метод provideDelegate вызывается для инициализации вспомогательного свойства prop$delegate. Сравните сгенерированный для объявления свойства код val prop: Type by MyDelegate() со сгенерированным кодом выше (когда provideDelegate не представлен):

class C {
    var prop: Type by MyDelegate()
}

// этот код будет сгенерирован компилятором 
// когда функция 'provideDelegate' доступна:
class C {
    // вызываем "provideDelegate" для создания вспомогательного свойства "delegate"
    private val prop$delegate = MyDelegate().provideDelegate(this, this::prop)
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
Заметьте, что метод provideDelegate влияет только на создание вспомогательного свойства и не влияет на код, генерируемый геттером или сеттером.

С интерфейсом PropertyDelegateProvider из стандартной библиотеки, вы можете создавать делегатов поставщиков делегатов без создания новых классов.

val provider = PropertyDelegateProvider { thisRef: Any?, property ->
    ReadOnlyProperty<Any?, Int> {_, property -> 42 }
}
val delegate: Int by provider