# Data Binding

Позволяет связать XML с данными напрямую
Упрощает работу с UI и позволяет писать меньше кода

Поддерживает LiveData и ViewModel

## start

Сначала включаем эту функцию в `build.gradle`

```gradle
android {
    ...
    buildFeatures {
        dataBinding true
    }
}

```

создание разметки
```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <!-- Определение переменных -->
        <variable
            name="viewModel"
            type="com.example.MyViewModel" />
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{viewModel.title}" />
    </LinearLayout>
</layout>
```

Инициализация в коде
```kotlin
val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
binding.viewModel = myViewModel
binding.lifecycleOwner = this

```