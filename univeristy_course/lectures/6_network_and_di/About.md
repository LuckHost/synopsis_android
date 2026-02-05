## Лекция 6 — Работа с сетью, ответственность и почему всё это вообще не развалилось

До этого момента мы вели себя немного наивно.
Данные в примерах появлялись как будто из воздуха, ViewModel всегда всё знала, а реальность с интернетом, ошибками и ожиданием загрузки как-то стыдливо обходилась стороной.

В этой лекции мы как раз залезаем туда, где начинается взрослая Android-разработка: сеть, слои, интерфейсы и попытка не превратить проект в кашу из классов.

---

### Структура проекта: приводим код в порядок заранее

Прежде чем писать сетевые запросы и подключать Retrofit, имеет смысл договориться, **где что будет жить**.
Если этого не сделать, сеть, UI и бизнес-логика очень быстро перемешаются, и проект начнёт напоминать ящик с проводами без подписей.

Один из самых распространённых и понятных подходов — разделение проекта на три слоя:
**presentation**, **domain** и **data**.

Это не строгая архитектура и не догма, а способ договориться с самим собой и командой.

#### presentation — слой отображения

Этот слой отвечает за всё, что связано с UI и состоянием экранов.

Здесь живут:

* Activity и Composable-функции
* ViewModel
* UI-состояния экранов

Пример структуры:

```
presentation/
 └── items/
     ├── ItemsScreen.kt
     ├── ItemsViewModel.kt
     └── ItemsState.kt
```

`presentation` **не знает**, откуда приходят данные.
Она просто запрашивает их через ViewModel и отображает результат.

Если в этом слое появляется Retrofit или DTO — это повод насторожиться.

#### domain — бизнес-логика и контракты

`domain` — самый «чистый» слой.
Он не зависит от Android, UI и конкретных библиотек.

Здесь находятся:

* бизнес-модели
* интерфейсы репозиториев
* логика, не связанная с отображением

Пример:

```
domain/
 ├── model/
 │   └── Item.kt
 └── repository/
     └── ItemsRepository.kt
```

Domain описывает *что* приложение умеет, но не *как* это реализовано.
Именно поэтому ViewModel зависит от интерфейсов из domain, а не от реализаций.

#### data — реализация работы с данными

`data` — слой, который знает про сеть, базы данных и форматы ответов.

Здесь находятся:

* DTO
* Retrofit-сервисы
* реализации репозиториев
* мапперы данных

Пример:

```
data/
 ├── network/
 │   ├── ApiService.kt
 │   └── ItemDto.kt
 ├── repository/
 │   └── ItemsRepositoryImpl.kt
 └── mapper/
     └── ItemMapper.kt
```

`data` знает про Retrofit, JSON и API.
Но он не знает, как данные будут отображаться.

#### Зачем всё это нужно

Такое разделение позволяет менять слои независимо друг от друга.

UI можно переписать на Compose — `data` не пострадает.
API можно заменить — `presentation` об этом не узнает.
Логику можно тестировать без Android вообще.

Именно с такой структурой становится понятно, **куда именно вписывается Retrofit**,
поэтому дальше мы спокойно переходим к работе с сетью.

---

### Retrofit и REST API

Мобильное приложение чаще всего — это клиент для REST API.
Мы отправляем HTTP-запросы, получаем JSON и превращаем его в объекты.

Retrofit позволяет описать API декларативно — в виде интерфейса.

```kotlin
interface ApiService {

    @GET("items")
    suspend fun getItems(): List<ItemDto>
}
```

Retrofit сам:

* выполнит запрос
* распарсит JSON
* вернёт результат в корутине

Никаких `HttpUrlConnection`, и слава богу.

---

### DTO — данные такими, какими их отдал сервер

DTO — это модели, которые **полностью повторяют контракт API**.

```kotlin
data class ItemDto(
    val id: Int,
    val title: String
)
```

DTO не обязаны быть удобными.
Они обязаны быть точными.

Если сервер завтра переименует поле — пострадает DTO, но не UI.
Это и есть цель.

---

### Repository: единая точка доступа к данным

Repository — это слой, который знает:

* откуда приходят данные
* в каком виде их вернуть приложению

```kotlin
class ItemsRepository(
    private val apiService: ApiService
) {

    suspend fun loadItems(): List<Item> {
        return apiService.getItems().map { it.toDomain() }
    }
}
```

Здесь DTO превращается в доменную модель.

```kotlin
data class Item(
    val id: Int,
    val title: String
)

fun ItemDto.toDomain(): Item {
    return Item(
        id = id,
        title = title
    )
}
```

ViewModel работает **только** с `Item`, а не с DTO.
Сеть для неё — чёрный ящик.

---

### Интерфейсы и разделение ответственности

Чтобы ViewModel не зависела от конкретной реализации репозитория, используется интерфейс.

```kotlin
interface ItemsRepository {
    suspend fun loadItems(): List<Item>
}
```

```kotlin
class ItemsRepositoryImpl(
    private val apiService: ApiService
) : ItemsRepository {

    override suspend fun loadItems(): List<Item> {
        return apiService.getItems().map { it.toDomain() }
    }
}
```

Теперь ViewModel знает только контракт.
Как именно данные получаются — не её проблема.

---

### ViewModel и состояние

```kotlin
class ItemsViewModel(
    private val repository: ItemsRepository
) : ViewModel() {

    private val _items = MutableLiveData<List<Item>>(emptyList())
    val items: LiveData<List<Item>> = _items

    fun loadItems() {
        viewModelScope.launch {
            _items.value = repository.loadItems()
        }
    }
}
```

ViewModel:

* не знает про Retrofit
* не знает про JSON
* не знает про Compose

Она просто управляет состоянием.

---

### Dependency Injection и Koin

Теперь главный вопрос:
кто всё это создаёт и связывает?

Ответ — **Koin**.
Он простой, декларативный и не требует магии аннотаций.

#### Koin-модуль

```kotlin
val networkModule = module {

    single {
        Retrofit.Builder()
            .baseUrl("https://example.com/")
            .build()
            .create(ApiService::class.java)
    }

    single<ItemsRepository> {
        ItemsRepositoryImpl(get())
    }

    viewModel {
        ItemsViewModel(get())
    }
}
```

Здесь мы описываем граф зависимостей:

* как создать `ApiService`
* как создать `Repository`
* как создать `ViewModel`

#### Подключение Koin в Application

```kotlin
class App : Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            androidContext(this@App)
            modules(networkModule)
        }
    }
}
```

И всё.
Никаких фабрик, синглтонов вручную и хаоса в `Activity`.

---

## Пример: всё вместе (Compose + VM + Koin)


Перейдем к коду
![](./images/vibe_debug.jpg)

### Composable-экран

```kotlin
@Composable
fun ItemsScreen(
    viewModel: ItemsViewModel = getViewModel()
) {
    val items by viewModel.items.observeAsState(emptyList())

    LaunchedEffect(Unit) {
        viewModel.loadItems()
    }

    LazyColumn {
        items(items) { item ->
            Text(
                text = item.title,
                modifier = Modifier.padding(16.dp)
            )
        }
    }
}
```

Что здесь происходит:

* ViewModel получаем из Koin
* состояние наблюдаем через LiveData
* загрузка данных запускается один раз
* UI просто реагирует на изменения


### Activity

```kotlin
class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            ItemsScreen()
        }
    }
}
```

Activity не знает:

* откуда данные
* как устроена сеть
* как создаётся ViewModel

И это идеальное состояние.

### Финальная картина

Данные идут по цепочке:

**API → DTO → Repository → ViewModel → UI**

Каждый слой знает только то, что ему нужно знать.
Меняется сервер — страдает нижний слой.
Меняется UI — сеть остаётся нетронутой.

И да, кода стало больше.
Зато теперь приложение можно поддерживать дольше, чем один семестр.
