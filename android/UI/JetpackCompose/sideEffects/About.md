# А для чего side effects?

Парадигма программирования перешла на функциональное, что подчеркивает compose

**Side effect** - когда от выполнения чего либо в программе меняется возможный исходный результат выполнения других функций 

```kotlin
class SomeClass() {
  private var number: Int = 6

  fun sideEffect() {
    second = Random.nextInt()
  } 

  fun calculate(a: Int, b: Int): Int {
    sideEffect() // чистота функции меняется 
    return a + b + number
  }

  fun test() {
    assert(sum(2, 3) == 5) // результат не ожидаем
    assert(sum(4, 3) == 7)
  }
}
```