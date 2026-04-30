# Coroutine context

[источник](https://www.youtube.com/watch?v=P3mr2oN-x9g&list=PL0SwNXKJbuNmsKQW9mtTSxNn00oJlYOLA&index=3)


`CoroutineContext` - инструмент описания конфигурации, серды выполнения корутины. Ее имя, пул потоков, жизненный цикл, обработчик ошибок

Упрощенно в коде она выглядит так
```kotlin
interface CoroutineContext {
    operator fun <E: Element> get(key: Key<E>): E?
    operator fun plus(context: CoroutineContext): CoroutineContext
}
```

<details>
    <summary>Зачем именно такая структура</summary>

Такой финт ушами нужен для того, чтобы "собирать" контекст было проще. Посмотрим на атрибуты

```kotlin
// CoroutineDispatcher
abstract class CoroutineDispatcher : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<CoroutineDispatcher>
    override val key: CoroutineContext.Key<*> = Key
}

// Job
interface Job : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<Job>
    override val key: CoroutineContext.Key<*> = Key
}

// CoroutineName
data class CoroutineName(val name: String) : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<CoroutineName>
    override val key: CoroutineContext.Key<*> = Key
}
```

На каждом из них остановимся подробнее в других доках, но главное заметить, что они все наследуют `CoroutineContext.Element` и определяют `companion object Key : CoroutineContext.Key<////>`. Разработчикам корутин это позволило решить проблему: корутина должна нести несколько независимых атрибутов, при этом: 
- атрибуты не должны конфликтовать
- должна быть возможность переопределить один, не трогая остальные
- типобезопасный доступ - не `context["dispatcher"]` со строкой, а `context[Dispatchers.Main]`

</details>

<p></p>


Поговорим про "переопределение" и в целом про создание контекста. Контекст состоит из элементов:
1. CoroutineDispatcher - отвечает за поток/пул потоков выполнения корутины
```kotlin
// Встроенные диспатчеры:
Dispatchers.Main       // UI-поток (Android)
Dispatchers.IO         // Для работы с сетью, файлами (пул потоков, до 64)
Dispatchers.Default    // Для тяжёлых вычислений (CPU-bound, по числу ядер)
Dispatchers.Unconfined // Не привязан к потоку (использовать осторожно)
```
2. Job - отвечает за жизненный цикл (запущена, отменена, завершена)
3. CoroutineName - имя корутины
4. CoroutineExceptionHandler - Перехватчик ошибок



Помогает определить среду и контекст выполнения корутины

Состоит из:
- **Job**
- **Dispatcher**
- **CoroutineName**
- **CoroutineExceptionHandler**

Наследуется от CoroutineContext родителя, либо от CoroutineScope

Два контекста могут быть объединены 