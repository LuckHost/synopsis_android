# Relations

[источник тык](https://startandroid.ru/ru/courses/architecture-components/27-course/architecture-components/534-urok-10-room-zapros-iz-neskolkih-tablic-relation.html)
[источник тык тык](https://developer.android.com/reference/androidx/room/Relation)

Если вы хотите, чтобы объект БД хранил в себе другой объект, у вас два (по моим скудным знаниям) пути

- JSON строки
- Еще одна таблица с сылкой на нее

> JSON строка прячется внутри базы данных
![мем](./images/airplane_in_airplane.jpg)

**JSON** легок и понятен, но делать поисковые операции по нему доволньо тяжело, поэтому ниже есть описание создания новых таблиц

Дальше просто вырваный из контекста ответ ChatGPT
--- 

Для создания отдельной таблицы `Product` и связи с таблицей `Receipt` потребуется:

1. Создать сущность `Product`.
2. Создать промежуточную таблицу для связи `Receipt` и `Product` (многие ко многим).
3. Обновить `Receipt` и настроить Room для использования новых таблиц.

### Шаг 1. Создание сущности `Product`

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "product")
data class Product(
    @PrimaryKey(autoGenerate = true) val productId: Int = 0,
    val name: String,
    val count: Float,
    val countType: CountType,
    val amount: Float,
    val currency: String
) {
    enum class CountType {
        KILOGRAMS, UNITS, GRAMS
    }
}
```

### Шаг 2. Создание промежуточной таблицы `ReceiptProductCrossRef`

Так как один `Receipt` может содержать множество `Product`, и один `Product` может встречаться в разных `Receipt`, создадим таблицу связи `ReceiptProductCrossRef`:

```kotlin
import androidx.room.Entity

@Entity(primaryKeys = ["receiptId", "productId"])
data class ReceiptProductCrossRef(
    val receiptId: Int,
    val productId: Int
)
```

### Шаг 3. Обновление сущности `Receipt`

Теперь изменим `Receipt`, чтобы указать на связь с продуктами.

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey
import androidx.room.ColumnInfo
import java.util.Date

@Entity(tableName = "receipt")
data class Receipt(
    @PrimaryKey(autoGenerate = true) val uid: Int = 0,
    @ColumnInfo(name = "date") val date: Date?,
    @ColumnInfo(name = "storeName") val storeName: String?,
    @ColumnInfo(name = "totalAmount") val totalAmount: Float?
)
```

### Шаг 4. Создание моделей для связи `Receipt` и `Product`

Чтобы Room мог подгружать связанные данные, создадим вспомогательный класс `ReceiptWithProducts`:

```kotlin
import androidx.room.Embedded
import androidx.room.Junction
import androidx.room.Relation

data class ReceiptWithProducts(
    @Embedded val receipt: Receipt,
    @Relation(
        parentColumn = "uid",
        entityColumn = "productId",
        associateBy = Junction(ReceiptProductCrossRef::class)
    )
    val products: List<Product>
)
```

### Шаг 5. Обновление DAO для работы со связями

Теперь нужно обновить `ReceiptDao`, чтобы можно было работать с `ReceiptWithProducts`:

```kotlin
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.Query
import androidx.room.Transaction

@Dao
interface ReceiptDao {

    @Insert
    suspend fun insertReceipt(receipt: Receipt): Long

    @Insert
    suspend fun insertProduct(product: Product): Long

    @Insert
    suspend fun insertReceiptProductCrossRef(crossRef: ReceiptProductCrossRef)

    @Transaction
    @Query("SELECT * FROM receipt WHERE uid = :receiptId")
    suspend fun getReceiptWithProducts(receiptId: Int): ReceiptWithProducts
}
```

### Шаг 6. Регистрация базы данных

Обновим базу данных, чтобы зарегистрировать все новые сущности:

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase
import androidx.room.TypeConverters

@Database(
    entities = [Receipt::class, Product::class, ReceiptProductCrossRef::class],
    version = 1
)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
    abstract fun receiptDao(): ReceiptDao
}
```

Теперь структура базы данных поддерживает нормализованное хранение `Product` в отдельной таблице, и вы сможете извлекать `Receipt` вместе с продуктами.