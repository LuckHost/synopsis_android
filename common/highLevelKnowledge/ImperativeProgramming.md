# Imperative programming 

В этом подходе мы описываем шаг действий для достижения результата. Мы говорим ЧТО надо сделать, чтоб добиться того, чего нам нужно. Циклы, x=x+1, это все про императивное программирование. Легче для понимания и чаще используется

```kotlin
fun factorial(n: Int) {
  var counter = 1
  var result = 1

  while (counter <= n) {
    result = result * counter
    counter += 1
  }
}
```