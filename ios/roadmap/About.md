# Роадмап iOS для Android-разработчика

Цель — как можно быстрее начать закрывать задачи на работе, но не писать говнокод, за который потом стыдно на ревью.

Исходная точка: я уже знаю Kotlin, корутины, Compose/XML, MVVM, Clean Architecture, DI. Значит учить "программирование" заново не надо, надо **переложить знания на другую платформу** и отдельно разобраться с тем, что в iOS устроено **принципиально иначе** (память, жизненный цикл, сборка и подпись, value types)

- [Что такое iOS и чем отличается от Android](iosVsAndroid.md) — абстрактная картина + таблица "что на что похоже"
- [Неочевидные моменты](nonObvious.md) — грабли, на которые наступает каждый андроидщик
- [Ресурсы](resources.md) — что читать/смотреть и что говорят на хабре

---

## Мак не всегда под рукой

Мак общий, поэтому время за ним — самое дорогое. Всё, что можно сделать без него, делаю на Windows, а за маком только то, что без Xcode не сделать

**Без мака (Windows):**
- Swift как язык: [Swift официально работает на Windows](https://www.swift.org/install/windows/) + расширение Swift для VS Code. Этапы 1, 2 и языковая часть 5 (async/await, actors, Sendable) проходятся полностью — UIKit/SwiftUI там нет, но язык весь
- Читать Swift-код в VS Code: open-source приложения на SwiftUI, исходники популярных библиотек
- WWDC сессии, Apple docs, HIG
- Продумывать задачу заранее: какие файлы трогаю, какие модели/экраны добавляю, черновик кода
- Ревью чужих MR — отличный способ учить идиомы, мак не нужен

**За маком:**
- Сборка, запуск, отладка (breakpoints, LLDB, View Hierarchy, Memory Graph)
- SwiftUI Previews и вёрстка с быстрой обратной связью
- Подпись, запуск на девайсе, TestFlight
- Instruments

**Как не терять время за маком:**
- Приходить с готовым планом и черновиком кода, а не "сейчас посмотрю, что тут"
- Держать проект на маке всегда в собранном состоянии (`pod install` сделан, SPM пакеты скачаны) — первая сборка с тяжёлыми SDK долгая
- Список "проверить на маке" вести заранее: что непонятно из кода и надо посмотреть в отладчике

---

## Принципы

1. **Фокус — современный стек**, остальное на уровне "могу прочитать":
   - SwiftUI + Observation (`@Observable`), свежая минимальная iOS
   - Swift Concurrency, код по умолчанию изолирован на `@MainActor`
   - MVVM + Clean (ViewModel → UseCase → Repository), навигация через Coordinator, ручной DI
   - сеть: `URLSession` / Moya + `Codable`
   - зависимости: SPM, CocoaPods для сторонних SDK
   - UIKit, Combine, Objective-C — читать и точечно править
2. **Маппинг, а не с нуля.** Для каждой новой штуки задаю вопрос "а что это в Android?" и записываю в [таблицу](iosVsAndroid.md#что-на-что-похоже). Где аналога нет — это самое важное, на это уходит основное время
3. **Читать код коллег > читать статьи.** Идиомы Swift быстрее всего впитываются из реального проекта и комментариев на ревью
4. **Сначала маленькие задачи.** Баги, правки UI, мелкие фичи. Большая фича в первый месяц = много переделок
5. **Swift — не "Kotlin с другим синтаксисом".** Синтаксис похож на 80%, но модель памяти и value types отличаются сильно. Если писать на Swift как на Kotlin — будут утечки и странные баги с копированием
6. **Записывать грабли** сюда в конспекты, как и с Android

---

## Этап 0. Окружение и ориентирование (2–3 дня)

- [ ] Mac, Xcode, Apple ID добавлен в команду (Apple Developer Program)
- [ ] Собрать рабочий проект и запустить на **симуляторе** и на **реальном девайсе** (тут сразу столкнусь с подписью)
- [ ] Разобраться в структуре проекта: `.xcodeproj` / `.xcworkspace`, targets, schemes, build configurations (Debug/Release), где лежат зависимости (SPM / CocoaPods / Tuist)
- [ ] Выяснить у команды: UIKit или SwiftUI, какая архитектура (MVVM / VIPER / Clean Swift / TCA), навигация (Coordinator / Router), DI, как идёт релиз (TestFlight, fastlane)
- [ ] Базовые хоткеи Xcode: `Cmd+R` запуск, `Cmd+B` сборка, `Cmd+U` тесты, `Cmd+Shift+O` открыть файл, `Cmd+Shift+K` clean, `Cmd+Click` перейти к определению, `Ctrl+I` переформатировать

**Результат:** проект собирается, я понимаю, где что лежит, и знаю стек

## Этап 1. Swift через призму Kotlin (1–2 недели)

Читаю [A Swift Tour](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/guidedtour/) целиком, потом главы The Swift Programming Language по списку ниже. Параллельно — Swift Playground или маленькие задачи на LeetCode на Swift

- [ ] `let`/`var`, типы, type inference, строки (и почему у `String` нет индексации по `Int`)
- [ ] Optionals: `?`, `!`, `if let`, `guard let`, `??`, optional chaining (≈ nullable в Kotlin, но `guard` — новое и очень частое)
- [ ] Функции: argument labels (`func move(from a: Int, to b: Int)`), default params, `inout`, `@discardableResult`
- [ ] Closures: синтаксис, trailing closure, `$0`, **`@escaping`** и **capture list `[weak self]`**
- [ ] **struct vs class** — value vs reference types, `mutating`, copy-on-write у коллекций. Самая важная тема этапа
- [ ] enum с associated values (≈ sealed class), `switch` с pattern matching, `indirect`
- [ ] Протоколы (≈ интерфейсы, но мощнее): default implementation через extensions, protocol-oriented programming, `associatedtype`
- [ ] Extensions (≈ extension functions, но расширяют тип целиком, можно добавить conformance к протоколу)
- [ ] Generics, `where`, `some` (opaque type) vs `any` (existential)
- [ ] Ошибки: `throws`, `try`/`try?`/`try!`, `do/catch`, `Result`
- [ ] Access control: `private`, `fileprivate`, `internal` (по умолчанию, ≈ модуль), `public`, `open`
- [ ] Property wrappers (`@Something`), computed properties, `lazy`, `willSet/didSet`
- [ ] Стандартные протоколы: `Equatable`, `Hashable`, `Codable`, `Comparable`, `Identifiable`, `CustomStringConvertible`
- [ ] Коллекции и функциональщина: `map`, `compactMap` (≈ mapNotNull), `flatMap`, `filter`, `reduce`, `first(where:)`

**Результат:** читаю любой Swift-код проекта без гугления синтаксиса

## Этап 2. Память: ARC (2–3 дня, но критично)

В Android есть GC, и о циклических ссылках можно не думать. В iOS **ARC — подсчёт ссылок**, цикл = утечка навсегда

- [ ] Как работает ARC: strong / weak / unowned, когда объект освобождается, `deinit`
- [ ] Retain cycles: closure ↔ self, delegate (почему `weak var delegate`), Timer, NotificationCenter
- [ ] `[weak self]` vs `[unowned self]` — когда что. Правило по умолчанию: `weak`
- [ ] Memory Graph Debugger в Xcode и Instruments → Leaks
- [ ] Посмотреть [WWDC21: ARC in Swift: Basics and beyond](https://developer.apple.com/videos/play/wwdc2021/10216/)

**Результат:** на ревью мне не пишут "тут утечка". Проверка — ставлю `print` в `deinit` у своих экранов и вижу, что они освобождаются после закрытия

## Этап 3. UI фреймворк проекта (2–4 недели)

Учу глубоко тот, что используется на проекте. Второй — до уровня "могу прочитать и поправить"

> На хабре сходятся, что нужны оба: в РФ в больших компаниях много UIKit и легаси, новое пишут на SwiftUI. SwiftUI проще зайти после Compose, UIKit — после XML/View
>
> **Мой фокус — SwiftUI + `@Observable`.** SwiftUI учу глубоко, а из UIKit — то, что нужно для интеграции: `UIWindow`, `UINavigationController`, `UIHostingController` (на них часто построены координаторы). Остальной UIKit ниже — на уровне чтения

### UIKit
- [ ] `UIViewController` и его жизненный цикл: `loadView` → `viewDidLoad` → `viewWillAppear` → `viewDidAppear` → `viewWillDisappear` → `viewDidDisappear`
- [ ] `UIView`: `frame` vs `bounds`, `layoutSubviews`, `setNeedsLayout` / `layoutIfNeeded`
- [ ] **Auto Layout кодом** (anchors, `NSLayoutConstraint.activate`), приоритеты, content hugging / compression resistance. Storyboard/xib — только уметь прочитать, если в проекте их нет
- [ ] Safe Area, Layout Margins
- [ ] `UITableView` / `UICollectionView`: переиспользование ячеек, `UICollectionViewCompositionalLayout`, `DiffableDataSource` (≈ RecyclerView + ListAdapter/DiffUtil)
- [ ] Навигация: `UINavigationController`, `present`/`dismiss`, `UITabBarController`, паттерн Coordinator
- [ ] Delegate и target-action паттерны
- [ ] Клавиатура и скролл: `keyboardLayoutGuide`, `UIScrollView`
- [ ] Анимации: `UIView.animate`, Core Animation на базовом уровне

### SwiftUI
- [ ] `View` как struct, `body`, модификаторы и **порядок модификаторов** (≈ Modifier в Compose)
- [ ] Стейт: `@State`, `@Binding`, `@Observable` + `@Bindable` (iOS 17+), `@Environment`; старое API `ObservableObject` / `@StateObject` / `@ObservedObject` / `@Published` (встретится в коде)
- [ ] Identity и lifetime view — [WWDC21: Demystify SwiftUI](https://developer.apple.com/videos/play/wwdc2021/10022/). Это аналог понимания рекомпозиции в Compose
- [ ] Layout: `VStack/HStack/ZStack`, `Spacer`, `frame`, `GeometryReader`, `Lazy*Stack`, `List`, `Grid`
- [ ] Навигация: `NavigationStack` + `navigationDestination`, `sheet`, `fullScreenCover`
- [ ] `.task {}` (≈ `LaunchedEffect`), `.onAppear`, `.onChange`
- [ ] Встраивание: `UIHostingController` (SwiftUI в UIKit) и `UIViewRepresentable` (UIKit в SwiftUI)
- [ ] Previews (`#Preview`)

**Результат:** сам верстаю экран по макету из Figma и прохожу ревью

## Этап 4. Платформа и жизненный цикл приложения (1 неделя)

- [ ] Состояния приложения: not running → inactive → active → background → suspended. `AppDelegate`, `SceneDelegate`, в SwiftUI — `@main App` + `scenePhase`
- [ ] Что происходит в фоне: приложение **замораживается** через несколько секунд, а потом может быть убито системой без уведомления. Фоновая работа: `beginBackgroundTask`, `BGTaskScheduler`, background modes
- [ ] `Info.plist`: permissions (usage description строки), URL schemes, конфигурация
- [ ] Permissions: камера, геолокация, уведомления, фото. Спросить можно один раз
- [ ] Deep links: URL schemes и Universal Links (`apple-app-site-association`)
- [ ] Push: APNs, `UNUserNotificationCenter`, токен, Notification Service Extension
- [ ] Sandbox, App Groups, Keychain, entitlements, App Extensions (виджеты, share extension)
- [ ] Ресурсы: Asset Catalog, `@2x/@3x`, points, String Catalogs (`.xcstrings`) для локализации, Dark Mode, Dynamic Type
- [ ] Пролистать [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — дизайнеры и ревью App Store мыслят этими категориями

## Этап 5. Многопоточность (1–2 недели)

Понимание корутин сильно помогает: Swift Concurrency концептуально очень близок

- [ ] Main thread и правило "UI только с главного потока". RunLoop (≈ Looper)
- [ ] **GCD**: `DispatchQueue.main`, global queues, serial vs concurrent, `sync` vs `async`, deadlock на `main.sync`, `DispatchGroup`. Будет во всём старом коде
- [ ] `OperationQueue` — на уровне "знаю, что это", если нет в проекте
- [ ] **Swift Concurrency**: `async/await`, `Task`, `TaskGroup`, `async let`, отмена, `actor`, `@MainActor`, `Sendable`
- [ ] Swift 6 strict concurrency — ошибки компилятора про data races, как их читать и чинить
- [ ] **Default actor isolation = `MainActor`** и approachable concurrency (Swift 6.2): весь код по умолчанию на main actor, фон — явно через `nonisolated` / `@concurrent`. Отсюда: `DispatchQueue.main.async` внутри такого кода лишний
- [ ] `AsyncSequence` / `AsyncStream` (≈ Flow)
- [ ] Combine (≈ RxJava/Flow) на уровне чтения: `AnyPublisher`, `PassthroughSubject`, `.sink`, `AnyCancellable`
- [ ] WWDC21: [Meet async/await](https://developer.apple.com/videos/play/wwdc2021/10132/), [Explore structured concurrency](https://developer.apple.com/videos/play/wwdc2021/10134/), [Protect mutable state with actors](https://developer.apple.com/videos/play/wwdc2021/10133/)

## Этап 6. Сеть и данные (1 неделя)

- [ ] `URLSession` + `async/await`, `Codable` + `JSONDecoder` (`CodingKeys`, стратегии ключей и дат)
- [ ] Moya (≈ Retrofit): endpoints через `TargetType`, поверх Alamofire
- [ ] Хранение: `UserDefaults` (≈ SharedPreferences), **Keychain** для токенов, `FileManager`
- [ ] БД (когда понадобится): SwiftData / Core Data / GRDB
- [ ] App Transport Security (только HTTPS по умолчанию)

## Этап 7. Инструменты, сборка, релиз (3–5 дней, дальше по ходу)

- [ ] Отладка: breakpoints, LLDB (`po`, `p`, `v`), условные и symbolic breakpoints, View Hierarchy Debugger (≈ Layout Inspector)
- [ ] Instruments: Time Profiler, Allocations, Leaks
- [ ] **Подпись**: certificate, provisioning profile, bundle id, team, entitlements, automatic vs manual signing. Как это устроено в команде (часто fastlane match)
- [ ] TestFlight, App Store Connect, review в App Store, phased release
- [ ] Краши: symbolication, dSYM, Crashlytics
- [ ] SPM: добавить пакет, локальные пакеты для модуляризации. CocoaPods — если в проекте (`pod install`, работаем через `.xcworkspace`)
- [ ] Почему `.pbxproj` конфликтует при мёрже и как с этим живут (XcodeGen / Tuist)

## Этап 8. Архитектура и тесты (по ходу работы)

- [ ] **Coordinator**: `Route` enum + обработка `Action`, кто владеет навигацией и создаёт ViewModel
- [ ] Другие популярные в iOS архитектуры: MVC (Apple's "Massive View Controller"), MVVM + Coordinator, VIPER, Clean Swift (VIP), TCA. Общая теория уже есть в [common](/common/patterns/architecture/About.md)
- [ ] DI: обычно вручную через инициализаторы или Swinject / Factory / Needle. Hilt-аналога "по умолчанию" нет
- [ ] Модуляризация через SPM-пакеты или фреймворки
- [ ] Тесты: XCTest и Swift Testing (`@Test`, `#expect`), моки через протоколы, UI-тесты XCUITest, snapshot тесты

## Этап 9. Углубление (после 2–3 месяцев)

- [ ] Читать Objective-C: легаси, `@objc`, bridging header, selectors, `NSObject`, KVO
- [ ] Runtime: method dispatch (static / vtable / message), `final`, почему protocol extensions не переопределяются
- [ ] Rendering: responder chain, hit testing, Core Animation, offscreen rendering, причины лагов скролла
- [ ] Swift глубже: copy-on-write своими руками, memory layout, generics specialization, result builders, macros
- [ ] Подготовка к собесу по iOS (вопросы — в [ресурсах](resources.md#собеседования))

---

## Практика параллельно

- **Двойник:** взять маленькую фичу, которую я уже делал на Android (список с пагинацией, экран деталей, авторизация), и написать её на iOS. Так видно разницу на знакомой задаче
- **Pet-проект** на SwiftUI + async/await + SPM — для закрепления того, чего нет на работе
- После каждого этапа — короткий конспект сюда же в `ios/`

## Примерный тайминг

| Неделя | Что |
|---|---|
| 1 | Этапы 0–2, первые мелкие баги |
| 2–4 | Этап 3 (UI), задачи по вёрстке |
| 5–6 | Этапы 4–6, полноценные небольшие фичи |
| 7–8 | Этапы 7–8, фичи целиком с сетью и тестами |
| 3 мес+ | Этап 9, углубление |
