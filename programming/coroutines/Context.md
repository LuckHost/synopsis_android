# Coroutine context

[источник](https://www.youtube.com/watch?v=P3mr2oN-x9g&list=PL0SwNXKJbuNmsKQW9mtTSxNn00oJlYOLA&index=3)



Помогает определить среду и контекст выполнения корутины

Состоит из:
- **Job**
- **Dispatcher**
- **CoroutineName**
- **CoroutineExceptionHandler**

Наследуется от CoroutineContext родителя, либо от CoroutineScope

Два контекста могут быть объединены 