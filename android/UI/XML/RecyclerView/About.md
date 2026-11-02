# Recycler View

[источник раз](https://habr.com/ru/articles/705064/)
[источник два](https://developer.android.com/develop/ui/views/layout/recyclerview)

RecyclerView "из коробки" предоставляет гораздо больше инструментов для кастомизации и оптимизации списка, чем ListView. Если кратко характеризовать RecyclerView, то можно сказать, что это список на стероидах.

RecyclerView работает следующим образом: на экране устройства отображаются видимые элементы списка; при прокрутке списка верхний элемент **уходит за пределы экрана и очищается**, а после **помещается вниз экрана и заполняется новыми данными**.

### Основные компоненты RecyclerView
Для корректной работы RecyclerView необходимо реализовать следующие компоненты:

- `RecyclerView`, который необходимо добавить в макет нашего Activity;

- `Adapter`, который содержит, обрабатывает и связывает данные со списком;

- `ViewHolder`, который служит для оптимизации ресурсов и является своеобразным контейнером для всех элементов, входящих в список;

- `ItemDecorator`, который позволяет отрисовать весь декор;

- `ItemAnimator`, который отвечает за анимацию элементов при добавлении, редактировании и других операций;

- `DiffUtil`, который служит для оптимизации списка и добавления стандартных анимаций.

### Метод компановки

Элементы в вашем RecyclerView упорядочиваются с помощью класса `LayoutManager` . Библиотека RecyclerView предоставляет три менеджера компоновки, которые справляются с наиболее распространёнными ситуациями компоновки:

- `LinearLayoutManager` упорядочивает элементы в виде одномерного списка.
- `GridLayoutManager` упорядочивает элементы в двумерной сетке:
  - Если сетка расположена вертикально, `GridLayoutManager` пытается сделать так, чтобы все элементы в каждом ряду имели одинаковую ширину и высоту, но разные ряды могут иметь разную высоту.
  - Если сетка расположена горизонтально, `GridLayoutManager` пытается сделать так, чтобы все элементы в каждом столбце имели одинаковую ширину и высоту, но разные столбцы могут иметь разную ширину.
- `StaggeredGridLayoutManager` Это похоже на `GridLayoutManager`, но не требует, чтобы элементы в строке имели одинаковую высоту (для вертикальных сеток) или элементы в одном столбце имели одинаковую ширину (для горизонтальных сеток). В результате элементы в строке или столбце могут располагаться со смещением относительно друг друга.

### Адаптер и view holder

Эти два класса работают вместе, определяя способ отображения данных. `ViewHolder` — это оболочка для `View`, которая содержит макет для отдельного элемента в списке. `Adapter` создает объекты `ViewHolder` по мере необходимости, а также устанавливает данные для этих представлений. Процесс привязки представлений к их данным называется *привязкой (binding)*.

Когда вы определяете свой адаптер, вы переопределяете три ключевых метода:

- `onCreateViewHolder()`: `RecyclerView` вызывает этот метод всякий раз, когда ему нужно создать новый `ViewHolder`. Метод создаёт и инициализирует `ViewHolder` и связанные с ним `View`, но не заполняет содержимое представления — `ViewHolder` ещё не привязан к конкретным данным.

- `onBindViewHolder()`: `RecyclerView` вызывает этот метод, чтобы связать `ViewHolder` с данными. Метод получает необходимые данные и использует их для заполнения макета держателя представления. Например, если `RecyclerView` отображает список имён, метод может найти нужное имя в списке и заполнить виджет `TextView` держателя представления.

- `getItemCount()`: `RecyclerView` вызывает этот метод, чтобы получить размер набора данных. Например, в приложении «Адресная книга» это может быть общее количество адресов. `RecyclerView` использует это, чтобы определить, когда больше нет элементов, которые можно отобразить.

Вот типичный пример простого адаптера с вложенным `ViewHolder` элементом, который отображает список данных. В этом случае `RecyclerView` отображает простой список текстовых элементов. Адаптеру передаётся массив строк, содержащий текст для `ViewHolder` элементов.

```kotlin

class CustomAdapter(private val dataSet: Array<String>) :
        RecyclerView.Adapter<CustomAdapter.ViewHolder>() {

    /**
     * Укажите тип используемых вами представлений
     * (пользовательский ViewHolder)
     */
    class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val textView: TextView

        init {
            // Определите слушиватель нажатий для представления Viewholder
            textView = view.findViewById(R.id.textView)
        }
    }

    // Создание новых представлений (вызывается менеджером компоновки)
    override fun onCreateViewHolder(viewGroup: ViewGroup, viewType: Int): ViewHolder {
        // Создание нового представления, 
        // которое определяет пользовательский интерфейс элемента списка
        val view = LayoutInflater.from(viewGroup.context)
                .inflate(R.layout.text_row_item, viewGroup, false)

        return ViewHolder(view)
    }

    // Заменить содержимое представления (вызывается менеджером компоновки)
    override fun onBindViewHolder(viewHolder: ViewHolder, position: Int) {

        // Get element from your dataset at this position and replace the
        // contents of the view with that element
        viewHolder.textView.text = dataSet[position]
    }

    // Возвращает размер вашего набора данных (вызывается менеджером компоновки).
    override fun getItemCount() = dataSet.size

}

```

### Использование 

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val dataset = arrayOf("January", "February", "March")
        val customAdapter = CustomAdapter(dataset)

        val recyclerView: RecyclerView = findViewById(R.id.recycler_view)
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = customAdapter
    }
}
```