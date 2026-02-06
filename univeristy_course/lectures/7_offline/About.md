## Лекция 7 — Оффлайн, база данных и почему интернету нельзя доверять

До этого момента мы молча предполагали, что интернет всегда есть, работает стабильно и отвечает быстро.
Практика показывает, что это оптимизм, граничащий с фантастикой.

Поэтому в реальных приложениях данные почти всегда:

* кешируются
* сохраняются локально
* восстанавливаются без сети

В этой лекции разбираемся, как Android живёт без интернета и какие инструменты для этого есть.

---

### Локальное хранилище как часть архитектуры

Оффлайн-режим — это не «фича для потом», а базовое ожидание пользователя.
Если приложение каждый раз начинает с пустого экрана и крутилки — оно быстро отправляется в корзину.

С точки зрения архитектуры локальное хранилище — это ещё один источник данных, наряду с сетью.
Repository решает, откуда брать данные: из сети, из базы или из кеша.

---

### Room — база данных в Android

**Room** — это официальный ORM от Google для работы с SQLite.
Он позволяет описывать таблицы и запросы через аннотации и почти не трогать SQL руками.

Начнём с сущности — описания таблицы.

```kotlin
@Entity(tableName = "items")
data class ItemEntity(
    @PrimaryKey val id: Int,
    val title: String
)
```

Entity — это не DTO и не доменная модель.
Это формат хранения данных в базе.

#### DAO — доступ к базе

DAO описывает операции с базой данных.

```kotlin
@Dao
interface ItemDao {

    @Query("SELECT * FROM items")
    suspend fun getAll(): List<ItemEntity>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(items: List<ItemEntity>)

    @Query("DELETE FROM items")
    suspend fun clear()
}
```

Здесь снова важно разделение ответственности:
DAO знает *как* работать с базой, но не знает *зачем*.

#### Database

```kotlin
@Database(
    entities = [ItemEntity::class],
    version = 1
)
abstract class AppDatabase : RoomDatabase() {

    abstract fun itemDao(): ItemDao
}
```

Room сам сгенерирует реализацию.
Никакой магии, просто много кода, который вам не нужно писать.

---

### Repository с кешированием

Теперь самое интересное — связываем сеть и базу.

```kotlin
class ItemsRepositoryImpl(
    private val apiService: ApiService,
    private val itemDao: ItemDao
) : ItemsRepository {

    override suspend fun loadItems(): List<Item> {

        val cached = itemDao.getAll()
        if (cached.isNotEmpty()) {
            return cached.map { it.toDomain() }
        }

        val remote = apiService.getItems()
        itemDao.insertAll(remote.map { it.toEntity() })

        return remote.map { it.toDomain() }
    }
}
```

Repository сначала смотрит в базу.
Если данные есть — возвращает их сразу.
Если нет — идёт в сеть и сохраняет результат локально.

Интернет есть — хорошо.
Интернета нет — пользователь всё равно что-то видит.

---

### Маппинг между слоями

```kotlin
fun ItemDto.toEntity(): ItemEntity {
    return ItemEntity(
        id = id,
        title = title
    )
}

fun ItemEntity.toDomain(): Item {
    return Item(
        id = id,
        title = title
    )
}
```

Да, моделей становится больше.
Зато каждая из них живёт ровно в своём слое и не протекает наружу.

---

### SharedPreferences — лёгкий локальный кеш

Не все данные стоит хранить в базе.
Для простых настроек и флагов существует **SharedPreferences**.

```kotlin
class SettingsStorage(context: Context) {

    private val prefs =
        context.getSharedPreferences("settings", Context.MODE_PRIVATE)

    fun saveTheme(isDark: Boolean) {
        prefs.edit()
            .putBoolean("dark_theme", isDark)
            .apply()
    }

    fun isDarkTheme(): Boolean {
        return prefs.getBoolean("dark_theme", false)
    }
}
```

SharedPreferences подходят для:

* настроек
* флагов первого запуска
* простых пользовательских предпочтений

Если возникает желание сохранить туда список из 500 элементов — это знак остановиться и подумать.

---

### SQLDelight — когда нужен KMP

Для обычного Android-проекта Room более чем достаточен.
Но если проект использует **Kotlin Multiplatform**, Room уже не подойдёт.

В таких случаях используют **SQLDelight**.
Он позволяет писать SQL-запросы вручную, но при этом генерирует типобезопасный Kotlin-код и работает на разных платформах.

В рамках курса мы на нём останавливаться не будем, но важно знать, что такая альтернатива существует.

---

### Итоговая картина

После добавления оффлайна цепочка данных становится длиннее, но надёжнее:

**API → DTO → Entity → Repository → Domain → ViewModel → UI**

Кода становится больше, зато приложение перестаёт зависеть от настроения сети.
А пользователь перестаёт видеть пустой экран в самый неподходящий момент.

И это, пожалуй, одно из самых ценных улучшений, которые можно сделать в мобильном приложении.
