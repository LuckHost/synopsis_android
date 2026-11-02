# Dagger

[истооочник](https://www.youtube.com/watch?v=Roo4JVLJ4IY&t=1485s)

Крутая штука для внедрения зависимостей
Много может, много делает, сложнее настраивать

Работает на **кодогенерации**
Использует аннотации для объявления своих классов

## Module

Объявляется аннотацией `@Module`. В них мы должны предоставлять зависимости

```kotlin
@Module
// @Singleton если хотим синглтон
class DataModule {
    @Provides
    fun provideCoolStorage(context: Context) : CoolStorageInterface {
        return SharedPrefStorageImpl(context = context)
    }
}
```

#### ViewModel

Так как в Dagger без обходов нельзя напрямую получить VM, то можно возвращать её Factory

**Factory**
```kotlin
class MainViewModelFactory(
    val getCoolParamsUseCase: getCoolParamsUseCase,
    val saveCoolParamsUseCase: saveCoolParamsUseCase,
) : ViewModelProvider.Factory {

    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return MainViewModel(
            getCoolParamsUseCase = getCoolParamsUseCase,
            saveCoolParamsUseCase = saveCoolParamsUseCase
        ) as T
    }
}
```

**Provider**
```kotlin
@Module
class AppModule(val context: Context) {
    @Provides
    fun provideContext(): Context {
        return context
    }

    @Provides
    fun provideMainViewModelFactory(
        getCoolParamsUseCase: getCoolParamsUseCase,
        saveCoolParamsUseCase: saveCoolParamsUseCase,
    ) : MainViewModelFactory {
        return MainViewModelFactory(
            getCoolParamsUseCase = getCoolParamsUseCase,
            saveCoolParamsUseCase = saveCoolParamsUseCase
        )
    }
}
```

## Component

Объявляется аннотацией `@Component`, отвечает за сборку всего воедино 

```kotlin
@Component(modules = [AppModule::class, DomainModule::class, DataModule::class])
interface AppComponent {
    // Dagger сам переопределит эту функцию позже
    fun inject(mainActivity: MainActivity)

    // в случае, если у нас несколько Activity, то просто создаем еще методы

    // fun inject(otherActivity: OtherActivity)
}
```

## Start

В App, который наследуется от Application, нужно указать билдер с переменной, в которую будет положен компонент

Так как Dagger работатет на кодогеренации, перед написанием этого класса нужно запустить сборку проекта, иначе класса `DaggerAppComponent` не будет видно

```kotlin
class App: Application() {
    lateinit var appComponent: AppComponent

    override fun onCreate() {
        super.onCreate()

        appComponent = DaggerAppComponent
            .builder()
            .appModule(AppModule(context = this))
            .build()
    }
}
```

В Activity нам надо запустить сам Dagger

```kotlin
class MainActivity : AppCompatActivity() {

    // указываем, какие переменные надо инжектить
    @Inject
    lateinit var vmFactory: MainViewModelFactory

    private lateinit var vm: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)

        // старт Dagger, после этого всего поля будут добавлены 
        (applicationContext as App).appComponent.inject(this)

        // создаем VM
        vm = ViewModelProvider(this, vmFactory)
            .get(MainViewModel::class.java)

        ...
    }
} 
```