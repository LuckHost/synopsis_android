# SwipeRefreshLayout

Компонент позволяет отказаться от сторонних библиотек и собственных велосипедов для реализации шаблона "Pull to Refresh", когда пользователь сдвигает экран, чтобы обновить данные. Подобное поведение можно увидеть в клиентах для Твиттера, чтобы получить новую порцию твитов, не дожидаясь, когда список сообщений обновится самостоятельно.

```xml
<androidx.swiperefreshlayout.widget.SwipeRefreshLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/swipeRefreshLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Внутри можно разместить любую прокручиваемую структуру, например, RecyclerView -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.swiperefreshlayout.widget.SwipeRefreshLayout>
```
### Как работает `SwipeRefreshLayout`

1. Он оборачивает прокручиваемый элемент (например, `RecyclerView`, `ScrollView`, `ListView`), что позволяет пользователю тянуть контент вниз, и при этом появляется индикатор обновления.
2. Когда происходит свайп вниз, запускается процесс обновления данных.
3. Программно обновление можно контролировать с помощью метода `setRefreshing(false)` для завершения анимации.

### Пример кода для обработки обновления

```kotlin
val swipeRefreshLayout = findViewById<SwipeRefreshLayout>(R.id.swipeRefreshLayout)

swipeRefreshLayout.setOnRefreshListener {
    // Здесь логика обновления данных, например, загрузка новых данных с сервера
    updateData()

    // После завершения обновления убираем индикатор
    swipeRefreshLayout.isRefreshing = false
}
```