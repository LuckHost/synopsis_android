# Realm

Альтернатива, не похожая на остальные БД в мобильной разработке

Объектно-ориентированная NoSQL база данных

<details> 
<summary>NoSQL</summary> 
NoSQL (от англ. not only SQL — «не только SQL») — категория нереляционных систем управления базами данных (СУБД). Такие системы отличаются от традиционных реляционных СУБД, для работы с которыми используется язык SQL.

У NoSQL нет жёсткой табличной структуры, как у реляционных, — они поддерживают другие модели хранения информации.
Некоторые типы NoSQL-баз данных:

Документоориентированные — данные в виде документов, обычно в формате JSON, BSON или XML. 

Колоночные — хранят информацию не в строках, а в столбцах.

Графовые — информация хранится в виде узлов, рёбер и свойств графов. 

Системы «ключ-значение» 
</details> 

При этом в Realm есть поддержка Android, iOS, JavaScript, .NET, но это не относится KMP


**Пример работы**
```kotlin
// Модель данных
open class User : RealmObject() {
    @PrimaryKey
    var id: Long = 0
    var name: String = ""
    var age: Int = 0
}

// Использование
val realm = Realm.getDefaultInstance()

// Запись
realm.executeTransaction { r ->
    val user = r.createObject(User::class.java, 1)
    user.name = "Alex"
    user.age = 25
}

// Чтение (с автоматическим обновлением!)
val users: RealmResults<User> = realm.where(User::class.java).findAll()
users.addChangeListener { results ->
    println("Users updated: $results") // Реактивное обновление
}
```