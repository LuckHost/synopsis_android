# Room

[источник](https://developer.android.com/training/data-storage/room)

Библиотека для работы с БД
Является надстройкой над SQLite

**преимущества:**

- Проверка SQL-запросов во время компиляции.
- Удобные аннотации, которые сводят к минимуму повторяющийся и подверженный ошибкам шаблонный код.
- Упрощенные пути миграции базы данных.

### Dependencies 

```kotlin
dependencies {
    val room_version = "2.6.1"

    implementation("androidx.room:room-runtime:$room_version")
    annotationProcessor("androidx.room:room-compiler:$room_version")

    // To use Kotlin annotation processing tool (kapt)
    kapt("androidx.room:room-compiler:$room_version")
    // To use Kotlin Symbol Processing (KSP)
    ksp("androidx.room:room-compiler:$room_version")

    // optional - Kotlin Extensions and Coroutines support for Room
    implementation("androidx.room:room-ktx:$room_version")

    // optional - RxJava2 support for Room
    implementation("androidx.room:room-rxjava2:$room_version")

    // optional - RxJava3 support for Room
    implementation("androidx.room:room-rxjava3:$room_version")

    // optional - Guava support for Room, including Optional and ListenableFuture
    implementation("androidx.room:room-guava:$room_version")

    // optional - Test helpers
    testImplementation("androidx.room:room-testing:$room_version")

    // optional - Paging 3 Integration
    implementation("androidx.room:room-paging:$room_version")
}
```

## Компоненты

- [Класс БД](#класс-бд)
- [Сущность данных](#сущность-данных)
- [Объекты доступа (DAO)](#объекты-доступа-dao)

![какая-то непонятная картинка](./images/room_architecture.png)

## Сущность данных
Следующий код определяет сущность данных `User`. Каждый экземпляр `User` представляет собой строку в таблице `user` в базе данных приложения.

```kotlin
@Entity
data class User(
    @PrimaryKey val uid: Int,
    @ColumnInfo(name = "first_name") val firstName: String?,
    @ColumnInfo(name = "last_name") val lastName: String?
)
```

## Объекты доступа (DAO)
Следующий код определяет DAO под названием `UserDao`. `UserDao` и предоставляет методы, которые остальная часть приложения использует для взаимодействия с данными в таблице `user`.

```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM user")
    fun getAll(): List<User>

    @Query("SELECT * FROM user WHERE uid IN (:userIds)")
    fun loadAllByIds(userIds: IntArray): List<User>

    @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
           "last_name LIKE :last LIMIT 1")
    fun findByName(first: String, last: String): User

    @Insert
    fun insertAll(vararg users: User)

    @Delete
    fun delete(user: User)
}
```

## Класс БД

Следующий код определяет класс `AppDatabase` для хранения базы данных. `AppDatabase` определяет конфигурацию базы данных и служит основной точкой доступа приложения к сохранённым данным. Класс базы данных должен соответствовать следующим условиям:

- Класс должен быть аннотирован с помощью аннотации `@Database`, которая включает массив `entities` со списком всех сущностей данных, связанных с базой данных.
- Класс должен быть абстрактным классом, который расширяется `RoomDatabase`.
- Для каждого класса DAO, связанного с базой данных, класс базы данных должен определять абстрактный метод без параметров, который возвращает экземпляр класса DAO.

```kotlin
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

## Ипользование


После того как вы определили сущность данных, DAO и объект базы данных, вы можете использовать следующий код для создания экземпляра базы данных:

```kotlin
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()
```

Затем вы можете использовать абстрактные методы из `AppDatabase` для получения экземпляра DAO. В свою очередь, вы можете использовать методы экземпляра DAO для взаимодействия с базой данных:

```kotlin
val userDao = db.userDao()
val users: List<User> = userDao.getAll()
```
--- 
получился полный копипаст документации.. 
ну тоже неплохо 