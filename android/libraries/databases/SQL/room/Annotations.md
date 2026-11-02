# Аннотации


- Аннотируйте класс с помощью `@Entity` и используйте свойство tableName, чтобы задать имя таблицы.
- Задайте первичный ключ, добавив аннотацию `@PrimaryKey` в правильные поля — в нашем случае это идентификатор пользователя.
- Задайте имя столбцов для полей класса, используя аннотацию `@ColumnInfo(name = "column_name")`. Этот шаг можно пропустить, если ваши поля уже названы так, как следует назвать столбец.
- Если в классе несколько конструкторов, добавьте аннотацию `@Ignore`, чтобы указать Room, какой следует использовать, а какой — нет.

```kotlin
@Entity(tableName = "users")
public class User {

    @PrimaryKey
    @ColumnInfo(name = "userid")
    private String mId;

    @ColumnInfo(name = "username")
    private String mUserName;

    @ColumnInfo(name = "last_update")
    private Date mDate;

    @Ignore
    public User(String userName) {
        mId = UUID.randomUUID().toString();
        mUserName = userName;
        mDate = new Date(System.currentTimeMillis());
    }

    public User(String id, String userName, Date date) {
        this.mId = id;
        this.mUserName = userName;
        this.mDate = date;
    }
...
}
```

`@Embedded` используется, когда Room не знает, как сохранить какой-то объект в базу. Эта аннотация подсказывает Room, что нужно взять поля из объекта и считать их полями текущей таблицы.

```Java
@Entity()
public class Employee {
 
   @PrimaryKey(autoGenerate = true)
   public long id;
 
   public String name;
 
   public int salary;
 
   @Embedded
   public Address address;
 
}
```
`Embedded` подскажет Room, что надо просто взять поля из Address и считать их полями таблицы Employee.

`@TypeConverters` применяется к абстрактному подклассу RoomDatabase и позволяет использовать пользовательские конвертеры типов внутри Room. 