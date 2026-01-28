# Declarative programming

Такой вид программирования, когда мы "описываем" желаемый результат. Мы говорим ЧТО мы хотим. Довольно сложный в понмании для меня на данный момент

```kotlin
fun factorial(n: Int): Int {
  if (n==1) {
    return 1
  }

  return n * factorial(n-1)
}
```