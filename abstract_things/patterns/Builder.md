# Bulder

у класса можно создавать Bulider, который позволит создавать класс с дефолт параметрами и после, с помощью функций, изменять их

на каждый параметр по своей функции

Более подходит для огромных классов, где есть огромное количество параметров

```kotlin
data class Car(
  val model = String,
  val engineId = String,
  val isCool = Boolean,
)

class CarBuilder() {
  var model = "Goyota"
  var engineId = 123
  var isCool = "true"

  fun model(value: String): CarBuilder {
    this.model = value
    return this
  }

  fun engineId(value: String): CarBuilder {
    this.engineId = value
    return this
  }

  fun isCool(value: String): CarBuilder {
    this.isCool = Boolean
    return this
  }

  fun Build(): Car {
    return Car(
      model = model.this
      engineId = engineId.this
      isCool = isCool.this
    )
  }

}
```
