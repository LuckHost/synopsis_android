# Что такое iOS и чем отличается от Android

## Абстрактно об iOS

**iOS** — закрытая мобильная ОС Apple. Ядро **XNU** (Mach + BSD), общая основа с macOS — **Darwin**. Сверху слои фреймворков:

- **Core OS** — ядро, драйвера, безопасность
- **Core Services** — `Foundation` (строки, даты, сеть, файлы), Core Data, CloudKit
- **Media** — Core Graphics, Core Animation, AVFoundation, Metal
- **Cocoa Touch** — `UIKit`, `SwiftUI`, уведомления, жесты

Ключевые идеи платформы:

- **Нет виртуальной машины.** Swift/Objective-C компилируются AOT сразу в машинный код ARM. В Android: Kotlin → байткод → DEX → ART (JIT/AOT). Отсюда другая модель памяти (ARC вместо GC) и отсутствие рефлексии уровня Java
- **Одна компания делает железо, ОС и магазин.** Мало моделей устройств, OS обновляется быстро (большинство пользователей на последних 1–2 версиях), поэтому `minimum deployment target` обычно свежий и можно пользоваться новым API. Зато всё решает Apple: правила App Store, что можно в фоне, какой API доступен
- **Система важнее приложения.** iOS жёстко контролирует фон, энергопотребление, доступ к данным. Приложение — гость в песочнице (sandbox)
- **Приложения изолированы.** Нет Intent'ов, ContentProvider'ов и публичных компонентов. Общение между приложениями — URL schemes, Universal Links, Share Sheet, App Extensions, App Groups (только свои приложения одной команды)
- **Дизайн-язык навязан** через Human Interface Guidelines. Пользователи ждут стандартного поведения: свайп назад, нативные алерты, стандартные контролы

## Главные отличия

### Язык и память
| | Android | iOS |
|---|---|---|
| Язык | Kotlin (+ Java легаси) | Swift (+ Objective-C легаси) |
| Память | GC: циклы не проблема | **ARC**: подсчёт ссылок, цикл = утечка |
| Основные типы | почти всё — reference (классы) | **value types**: `struct`, `enum`, `Array`, `Dictionary`, `String` — всё копируется |
| Null | nullable `T?` | Optional `T?` (по сути enum `.some/.none`) |
| Исключения | unchecked, можно поймать что угодно | `throws` явно, а force unwrap, выход за массив, переполнение `Int` — **краш**, который не поймать |

### Жизненный цикл
- **Нет пересоздания экрана при повороте/смене темы/языка.** Configuration changes в смысле Android не существует. `UIViewController` живёт, просто перерисовывается. ViewModel "чтобы пережить поворот" не нужна (архитектурно она нужна по другим причинам)
- **Process death есть, но по-другому.** Приложение в фоне замораживается (suspended), потом может быть убито системой (jetsam) без каких-либо колбэков. При следующем запуске — обычный холодный старт. Автоматического восстановления back stack как в Android нет; state restoration есть, но многие приложения его не делают
- **Фон сильно ограничен.** Нет Service и WorkManager с гарантиями. Есть: несколько секунд дорабатывания (`beginBackgroundTask`), `BGTaskScheduler` (система сама решает когда), background URLSession для загрузок, конкретные background modes (аудио, геолокация, VoIP) — за злоупотребление отклоняют в App Store
- **Одна точка входа**, нет нескольких Activity в манифесте. Сцены (`UIScene`) нужны в основном для iPad multiwindow

### Сборка и распространение
- Только **Xcode на macOS**
- **Подпись обязательна даже для запуска на своём телефоне**: сертификат + provisioning profile + bundle id + team. В Android debug keystore создаётся сам, и об этом не думаешь
- Нужен платный Apple Developer Program ($99/год) для релиза
- Каждый релиз проходит **ручной review** в App Store (обычно до суток-двух, могут отклонить). Быстрого хотфикса нет, откатить версию нельзя — только выпустить новую
- TestFlight ≈ internal/closed testing в Google Play
- Проект — `.xcodeproj` (XML-подобный `project.pbxproj`, любит мерж-конфликты), а не текстовый Gradle-скрипт. Поэтому многие используют Tuist / XcodeGen

### UI
- Единицы — **points** (≈ dp), картинки `@2x`/`@3x` вместо density buckets
- Нет системной кнопки "назад": свайп от левого края и кнопка в navigation bar. Сломать свайп назад — классический баг
- Мало размеров экранов, но есть safe area (чёлка, Dynamic Island, home indicator)

## Что на что похоже

| Android | iOS | Комментарий |
|---|---|---|
| Kotlin | Swift | |
| Gradle | Xcode build system + SPM | build variants ≈ schemes + build configurations |
| Maven/Gradle deps | **SPM**, CocoaPods, Carthage | CocoaPods уходит, новое — SPM |
| `AndroidManifest.xml` | `Info.plist` + entitlements | |
| `applicationId` | bundle identifier | |
| Activity / Fragment | `UIViewController` | |
| View | `UIView` | |
| XML layouts | Storyboard/xib (легаси) или вёрстка кодом | |
| ConstraintLayout | Auto Layout | |
| RecyclerView + Adapter + DiffUtil | `UICollectionView` / `UITableView` + `DiffableDataSource` | |
| Jetpack Compose | SwiftUI | `@Composable` ≈ `View`, `remember { mutableStateOf }` ≈ `@State` |
| `Modifier` | view modifiers | порядок тоже важен |
| `LaunchedEffect` | `.task {}` | |
| `CompositionLocal` | `@Environment` | |
| Navigation Component | `UINavigationController` / `NavigationStack`, Coordinator | |
| `Application` | `AppDelegate` / SwiftUI `App` | |
| Lifecycle callbacks | `viewDidLoad`, `viewWillAppear`, ... / `scenePhase` | |
| ViewModel + StateFlow | ViewModel + `@Observable` / `@Published` | |
| Coroutines | Swift Concurrency (`async/await`, `Task`) | |
| `Dispatchers.Main` | `@MainActor`, `DispatchQueue.main` | |
| `Dispatchers.IO/Default`, Executors | GCD global queues, `Task.detached` | |
| `coroutineScope { async {} }` | `withTaskGroup` / `async let` | |
| `Mutex`, `synchronized` | `actor`, `NSLock`, serial queue | |
| Flow | `AsyncSequence` / `AsyncStream` | |
| RxJava | Combine (или RxSwift в легаси) | |
| Looper / Handler | RunLoop / `DispatchQueue.main` | |
| Retrofit + OkHttp | `URLSession` (+ Alamofire) | |
| kotlinx.serialization / Gson | `Codable` | |
| Room | Core Data / SwiftData / GRDB | |
| SharedPreferences / DataStore | `UserDefaults` | |
| EncryptedSharedPreferences / Keystore | Keychain | |
| Hilt / Dagger / Koin | ручной DI через init, Swinject, Factory, Needle | стандарта нет |
| LiveData observers / listeners | delegate, closures, NotificationCenter | |
| LocalBroadcastManager / event bus | `NotificationCenter` | |
| Intent filters / deep links | URL schemes / Universal Links | |
| FCM | APNs (FCM на iOS — обёртка над APNs) | |
| WorkManager | `BGTaskScheduler` | без гарантий выполнения |
| `strings.xml` | String Catalog (`.xcstrings`) | |
| `res/drawable`, colors | Asset Catalog (`.xcassets`) | |
| Emulator | Simulator | симулятор — не эмулятор, гоняет код нативно на Mac, есть отличия от девайса |
| Logcat | Xcode console, `Logger` (os_log), Console.app | |
| Layout Inspector | View Hierarchy Debugger | |
| Android Profiler | Instruments | |
| LeakCanary | Memory Graph Debugger, Instruments Leaks | |
| JUnit / Espresso | XCTest, Swift Testing / XCUITest | |
| Play Console / internal testing | App Store Connect / TestFlight | |
| ProGuard/R8 | нет аналога (код и так нативный) | |
| `@Parcelize` / Bundle | не нужно — нет пересоздания экранов, данные передаются через init | |
