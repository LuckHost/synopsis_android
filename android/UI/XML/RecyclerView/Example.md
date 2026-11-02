# Пример использования

## Работа с объектами списка

Создаем класс объекта
Нужен для того, чтоб работать с ним вне списка и просто для структуризации
```kotlin
data class Item(
    val text: Int
)
```

А также разметку, которую будет использовать Recycler
>item.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tool="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="#e7e7e7"
    android:padding="7dp">

    <TextView
        android:id="@+id/mainText"
        style="@style/TextAppearance.AppCompat.Body2"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:padding="3dp"

        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"

        android:textColor="#202020"
        tool:text="Крутой текст"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## Adapter

Включаем binding, если его еще нет
Пункт не обязательный, но дальше будем пользоваться именно им

> build.grdle.kts
```gradle
android {
    buildFeatures {
		viewBinding = true
    }
}
```

И пишем свой Adapter
```kotlin
class RecyclerItemsAdapter(
    private val data: List<Item>
) : RecyclerView.Adapter<RecyclerItemsAdapter.ItemViewHolder>() {

    class ItemViewHolder(val binding: ItemBinding) :
        RecyclerView.ViewHolder(binding.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ItemViewHolder {
        val inflater = LayoutInflater.from(parent.context)
        val binding = ItemBinding.inflate(inflater, parent, false)

        return ItemViewHolder(binding)
    }

    override fun onBindViewHolder(holder: ItemViewHolder, position: Int) {
        val item = data[position]
        with(holder.binding) {
			// Присваиваем значение из элемента списка 
			// элементу RecyclerView
            mainText.text = item.text
        }
    }

    override fun getItemCount(): Int {
        return data.count()
    }
}
```

## Создание списка

Размещаем на activity RecycelerView

```xml
<!-- Остальной код -->

<androidx.recyclerview.widget.RecyclerView
	android:id="@+id/recyclerView"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:scrollbars="vertical"
	android:scrollbarThumbVertical="@color/recitem_text_color"
	android:scrollbarTrackVertical="@color/recitem_divider_color"
	app:layout_constraintBottom_toBottomOf="parent"
	app:layout_constraintEnd_toEndOf="parent"
	app:layout_constraintStart_toStartOf="parent"
	app:layout_constraintTop_toBottomOf="@+id/pieChart"/>

<!-- Остальной код -->
```

И запуск
```kotlin
private lateinit var adapter: RecyclerReceiptAdapter

override fun onCreate(savedInstanceState: Bundle?) {
	super.onCreate(savedInstanceState)
	binding = ActivityMainBinding.inflate(layoutInflater)

	enableEdgeToEdge()
	setContentView(binding.root)

	ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
		val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
		v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
		insets
	}

	val manager = LinearLayoutManager(activity?.application)

	adapter = RecyclerReceiptAdapter(listOf(
		Item("Item 1"),
		Item("Item 2"),
		Item("Item 3")
	))
	binding.recyclerView.layoutManager =
		manager
	binding.recyclerView.adapter = adapter
}
```