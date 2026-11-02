# View Binding

[источник](https://habr.com/ru/articles/467295/)

Генерирует binding классы для каждого файла разметки (layout) в модуле. Объект сгенерированного binding класса содержит ссылки на все view из файла разметки, для которых указан `android:id`.

Позволяет проще взаимодействовать с разметкой 

### Как включить

Чтобы включить View Binding в модуле надо добавить элемент в файл build.gradle:

```gradle
android {
    ...
    viewBinding {
        enabled = true
    }
}
```

### Как использовать

Каждый сгенерированный binding класс содержит ссылку на корневой view разметки (root) и ссылки на все view, которые имеют id. Имя генерируемого класса формируется как "название файла разметки", переведенное в camel case + "Binding".

Например, для файла разметки `result_profile.xml`:

```xml
<LinearLayout ... >
    <TextView android:id="@+id/name" />
    <ImageView android:cropToPadding="true" />
    <Button android:id="@+id/button"
        android:background="@drawable/rounded_button" />
</LinearLayout>
```

Будет сгенерирован класс ResultProfileBinding, содержащий 2 поля: `TextView name` и `Button button`. Для `ImageView` ничего сгенерировано не будет, как как оно не имеет id. Также в классе ResultProfileBinding будет метод getRoot(), возвращающий корневой LinearLayout.

Чтобы создать объект класса ResultProfileBinding, надо вызвать статический метод inflate(). После этого можно использовать корневой view как content view в Activity:

```kotlin
private lateinit var binding: ResultProfileBinding

@Override
fun onCreate(savedInstanceState: Bundle) {
    super.onCreate(savedInstanceState)
    binding = ResultProfileBinding.inflate(layoutInflater)
    setContentView(binding.root)
}
```

Позже binding можно использовать для получения view:

```kotlin
binding.name.text = viewModel.name
binding.button.setOnClickListener { viewModel.userClicked() }
```