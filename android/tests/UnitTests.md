# Unit Tests

Для тестирования отдельных блоков, сущностей. Не нужен эмулятор, быстро пишется и работает

#### Виды проверок 

- Тест должен выйти без ошибки 
```kotlin
@Test
fun `some function`() {
  val value = "7"
  assertEquals("expected value", value)
}
```

- Тест должен выйти с ожилаемой ошибкой 
```kotlin
@Test(expected = NullPointerException::class)
fun `some function`() {
  val value: String? = null
  assertEquals("expected value", value)
}
```

## Before and After 

`@Before` и `@After` — это аннотации, которые помогают управлять жизненным циклом тестовых случаев. Они позволяют выполнять код до и после каждого теста соответственно.


`@Before` указывает, что метод должен быть выполнен **перед** каждым тестовым методом.
`@After` указывает, что метод должен быть выполнен **после** каждого тестового метода.


Да, вы правильно понимаете! Аннотации `@Before` и `@After` в Android-тестировании часто применяются для настройки и очистки таких ресурсов, как база данных, перед и после каждого теста. Давайте рассмотрим конкретный сценарий, связанный с тестированием базы данных.

### Пример использования `@Before` и `@After` для работы с базой данных

```kotlin
class ExampleDatabaseTest {

    private lateinit var db: ExampleDatabase
    private lateinit var dao: ExampleDao

    @Before
    fun setUp() {
        // Инициализируем базу данных перед каждым тестом
        db = Room.inMemoryDatabaseBuilder(
            ApplicationProvider.getApplicationContext(),
            ExampleDatabase::class.java
        ).build()
        
        // Получаем доступ к DAO
        dao = db.exampleDao()
    }

    @After
    fun tearDown() {
        // Закрываем базу данных после каждого теста
        db.close()
    }

    @Test
    fun testInsertAndRetrieveData() {
        // Тестируем вставку и получение данных
        val exampleEntity = ExampleEntity(id = 1, name = "Test")
        dao.insert(exampleEntity)
        
        val retrievedEntity = dao.getById(1)
        assert(retrievedEntity?.name == "Test")
    }

    @Test
    fun testDeleteData() {
        // Тестируем удаление данных
        val exampleEntity = ExampleEntity(id = 1, name = "Test")
        dao.insert(exampleEntity)
        dao.delete(exampleEntity)
        
        val retrievedEntity = dao.getById(1)
        assert(retrievedEntity == null)
    }
}
```

### Дополнительно

Кроме `@Before` и `@After`, существуют аннотации `@BeforeClass` и `@AfterClass`, которые выполняют методы один раз перед всеми и один раз после всех тестов. Они полезны, если нужно настроить или очистить что-то глобальное для всего набора тестов.

```kotlin
import org.junit.BeforeClass
import org.junit.AfterClass

class ExampleUnitTest {

    companion object {
        @BeforeClass
        @JvmStatic
        fun setUpClass() {
            // Выполняется один раз перед всеми тестами
        }

        @AfterClass
        @JvmStatic
        fun tearDownClass() {
            // Выполняется один раз после всех тестов
        }
    }

    @Test
    fun testSomething() {
        // Обычный тест
    }
}
```

## Best practise

- Должен быть атомарным, то есть иметь один ассерт
- Должен называться полно и понятно