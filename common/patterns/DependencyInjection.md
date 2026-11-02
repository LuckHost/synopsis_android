# DI

То, что постоянно используется в Clean Architecture

Зависимости внутри объектов или классов передаются этому классу извне, он не создает их явно

```kotlin
class SomeClass() {
  private val aboba = SomeCoolClass()
  // doing not so cool things
}
```

```kotlin
class SomeClass(
  private val aboba: SomeCoolClassInterface
) {
  // doing realy cool things like watch anime
  // >:)
}
```