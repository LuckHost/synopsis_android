## Лекция 5 — Jetpack Compose

![](./images/jetpack_compose.png)

Jetpack Compose — это декларативный UI-фреймворк для Android.
Вместо того чтобы описывать *как* именно нужно обновлять интерфейс, мы описываем *что* должно быть показано при текущем состоянии.
Compose сам решает, когда и какую часть экрана нужно перерисовать.

[Хороший курс по Jetpack Compose есть тут](https://youtube.com/playlist?list=PLgPRahgE-GcvdwPYg3N_hqveb_0E2anzQ&si=1nFsS9e-BAtCbu3l)

---

### Composable-функции

В Compose интерфейс строится из функций, помеченных аннотацией `@Composable`.

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Привет, $name")
}
```

Такая функция не возвращает View и ничего не создаёт вручную.
Она просто описывает UI. Если входные данные меняются, Compose вызывает её заново.

Важно воспринимать Composable как обычную функцию без побочных эффектов.
Она не должна менять состояние, запускать корутины или ходить в сеть.
Если начинает — это уже тревожный звоночек, а иногда и сирена.

Composable-функции можно свободно комбинировать, вкладывать друг в друга и передавать им состояние. В итоге экран — это дерево функций.

### Контейнеры: Row, Column, Box, Grid

Для размещения элементов Compose использует контейнеры.
Они заменяют собой привычные layout’ы из XML, но работают проще и предсказуемее.

```kotlin
@Composable
fun SimpleLayout() {
    Column {
        Text("Заголовок")
        Row {
            Text("Левый текст")
            Text("Правый текст")
        }
    }
}
```

`Column` размещает элементы вертикально, `Row` — горизонтально.
`Box` используется, когда элементы нужно накладывать друг на друга.

```kotlin
@Composable
fun BoxExample() {
    Box {
        Text("Фон")
        Button(onClick = {}) {
            Text("Кнопка поверх")
        }
    }
}
```

Grid-контейнеры позволяют строить сетки. Чаще всего они используются в варианте со списками, которые можно скроллить, о них речь пойдёт дальше.
В целом идея простая: layout — это код, и он читается так же, как обычный Kotlin.

### LazyRow, LazyColumn, LazyGrid

Списки в Compose реализуются через lazy-контейнеры.
Это аналоги RecyclerView, только без адаптеров, view holder’ов и ритуальных танцев.

```kotlin
@Composable
fun ItemsList(items: List<String>) {
    LazyColumn {
        items(items) { item ->
            Text(text = item)
        }
    }
}
```

Lazy-контейнеры создают и держат в памяти только те элементы, которые реально видны на экране.
Переиспользование происходит автоматически, без участия разработчика.

`LazyRow` работает так же, но горизонтально.
`LazyGrid` позволяет строить сетки, например, для галерей или карточек.

```kotlin
@Composable
fun GridExample(items: List<String>) {
    LazyVerticalGrid(columns = GridCells.Fixed(2)) {
        items(items) { item ->
            Text(text = item)
        }
    }
}
```

Если раньше RecyclerView считался отдельной темой на несколько часов работы, то теперь это просто ещё один контейнер. 

---

#### SideEffect

Composable-функции должны быть чистыми, но иногда нужно выполнить действие, не связанное напрямую с отрисовкой UI.
Для этого существуют side effects.

`SideEffect` выполняется после каждой успешной рекомпозиции:

```kotlin
@Composable
fun SideEffectExample(value: Int) {
    SideEffect {
        println("Текущее значение: $value")
    }

    Text(text = value.toString())
}
```

Этот код не влияет на UI напрямую, но позволяет реагировать на изменения состояния.
Используется редко, но полезен, когда нужно синхронизировать Compose с внешним миром.

#### LaunchedEffect

Гораздо чаще требуется запускать асинхронные операции — загрузку данных, таймеры, подписки.
Для этого используется `LaunchedEffect`.

```kotlin
@Composable
fun LoadData(id: String) {
    LaunchedEffect(id) {
        repository.loadData(id)
    }
}
```

`LaunchedEffect` запускает корутину, привязанную к жизненному циклу Composable.
Если ключ меняется — эффект перезапускается.
Если Composable исчезает с экрана — корутина отменяется автоматически.

Это позволяет писать асинхронный код без ручного управления жизненным циклом.
Наконец-то корутины можно не только запускать, но и не забывать останавливать.

---

### Пример экрана: LazyColumn + ViewModel + состояние

Начнём с ViewModel.
Она хранит список элементов и предоставляет методы для изменения состояния.

```kotlin
class ListViewModel : ViewModel() {

    private val _items = MutableLiveData<List<Int>>(emptyList())
    val items: LiveData<List<Int>> = _items

    fun addItem() {
        val current = _items.value.orEmpty()
        _items.value = current + (current.size + 1)
    }

    fun removeItem() {
        val current = _items.value.orEmpty()
        if (current.isNotEmpty()) {
            _items.value = current.dropLast(1)
        }
    }
}
```

ViewModel ничего не знает про Compose, кнопки или списки.
Она просто управляет состоянием.

Теперь Composable-экран.
Здесь мы получаем состояние из ViewModel и описываем, как оно выглядит.

```kotlin
@Composable
fun ListScreen(viewModel: ListViewModel) {

    val items by viewModel.items.observeAsState(emptyList())

    Column {
        LazyColumn(
            modifier = Modifier
                .weight(1f)
        ) {
            items(items) { item ->
                Text(
                    text = "Элемент $item",
                    modifier = Modifier.padding(16.dp)
                )
            }
        }

        Row(
            modifier = Modifier.padding(16.dp)
        ) {
            Button(
                onClick = { viewModel.addItem() },
                modifier = Modifier.weight(1f)
            ) {
                Text("+1")
            }

            Spacer(modifier = Modifier.width(8.dp))

            Button(
                onClick = { viewModel.removeItem() },
                modifier = Modifier.weight(1f)
            ) {
                Text("-1")
            }
        }
    }
}
```

Здесь важно заметить несколько вещей, даже если не заострять на них внимание вслух.

Compose **не хранит список**.
Он получает его из ViewModel и просто отображает. Кнопки **не меняют UI напрямую**. Они вызывают методы ViewModel, а та обновляет состояние.

Как только список меняется, Compose автоматически перерисовывает `LazyColumn`. Никаких `notifyDataSetChanged`, адаптеров и ручных обновлений.

Осталось связать всё это в Activity. 

```kotlin
class MainActivity : ComponentActivity() {

    private val viewModel: ListViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            ListScreen(viewModel)
        }
    }
}
```

Activity здесь выполняет минимальную роль:
создаёт ViewModel и передаёт её в Compose.

Итого мы получаем экран, где:

* список живёт в ViewModel
* UI полностью декларативный
* состояние управляет интерфейсом, а не наоборот

![источник https://www.costafotiadis.com/post/going-edge-to-edge-with-compose-without-losing-it](./images/compose_meme_2.avif)