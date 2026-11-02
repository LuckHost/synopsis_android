# Ресурсы

## Официальное (главное)

- [The Swift Programming Language](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/) — книга по Swift, начать с A Swift Tour
- [Apple Developer Documentation](https://developer.apple.com/documentation/) — UIKit, SwiftUI, Foundation
- [SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui) — официальные туториалы с проектами
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) — пролистать, чтобы знать, за что отклоняют

## WWDC сессии (смотреть обязательно)

- [ARC in Swift: Basics and beyond (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10216/)
- [Meet async/await in Swift (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10132/)
- [Explore structured concurrency in Swift (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10134/)
- [Protect mutable state with Swift actors (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10133/)
- [Swift concurrency: Behind the scenes (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10254/)
- [Demystify SwiftUI (WWDC21)](https://developer.apple.com/videos/play/wwdc2021/10022/)
- [Discover Observation in SwiftUI (WWDC23)](https://developer.apple.com/videos/play/wwdc2023/10149/)
- [Embrace Swift generics (WWDC22)](https://developer.apple.com/videos/play/wwdc2022/110352/)
- Каждый год смотреть "What's new in Swift" / "What's new in SwiftUI"

## Курсы и книги

- **Stanford CS193p** — бесплатный курс по SwiftUI на YouTube, хорош для системного понимания
- [Hacking with Swift](https://www.hackingwithswift.com/) (Paul Hudson) — 100 Days of SwiftUI, быстрые ответы "how to ..."
- **Kodeco** (бывший raywenderlich): Swift Apprentice, SwiftUI Apprentice, Concurrency by Tutorials, Modern Concurrency in Swift, Core Data by Tutorials — эти книги рекомендует роадмап KTS
- **objc.io**: Advanced Swift, Thinking in SwiftUI — когда база уже есть
- [Swift is like Kotlin](https://nilhcem.com/swift-is-like-kotlin/) — шпаргалка синтаксиса бок о бок

## Блоги

- [SwiftLee](https://www.avanderlee.com/) — концентрированно про Swift Concurrency, Xcode, практику
- [Swift by Sundell](https://www.swiftbysundell.com/)
- [Point-Free](https://www.pointfree.co/) — продвинуто, авторы TCA

## Русскоязычное

- Telegram: iOS Good Reads, чат SwiftBook
- SwiftBook — курсы по Swift для начинающих на YouTube

## Собеседования

- [vyachesIavskiy/iOS-Interview-Questions](https://github.com/vyachesIavskiy/iOS-Interview-Questions)
- [Подготовка к собеседованию на iOS разработчика](https://habr.com/ru/articles/726388/) — 40 вопросов с ответами. В комментариях справедливо замечают, что не хватает памяти, responder chain, hit test, autolayout, side table. Есть неточности в примерах (`guard let`, `HEADER` вместо `HEAD`)

---

## Что говорят на хабре (выжимка)

### [Как стать iOS-разработчиком? — KTS](https://habr.com/ru/companies/kts/articles/734166/)
**+28, 97 закладок** — самый структурированный роадмап из найденных, на него опирался больше всего

- Порядок: общая база (английский, CS, Git) → Swift → Xcode (отладчик, lldb, memory graph, view debugger) → UIKit и SwiftUI → pet-проект → deep dive (жизненный цикл, push, deeplinks, сеть, хранение) → архитектура → зависимости → тесты → многопоточность (GCD, Operations, Swift Concurrency)
- **"НЕ учимся работать с Interface Builder и Storyboard"** — в вакансиях вёрстка кодом
- Для UIKit начинать с anchors, без SnapKit/PinLayout
- Учить **оба** фреймворка
- Автор в комментариях: на собесах простые сортировки спрашивают заметно реже, чем **разницу value и reference types**; коммуникация часто решает
- Комментаторы: "в реальных проектах все по-разному, без большей части можно жить, но для роста учить нужно"

### [Бесплатная программа обучения](https://habr.com/ru/articles/890036/)
+5, 34 закладки, обновлена в 2026 — только бесплатные YouTube-курсы, рассчитано на 8–10 месяцев с нуля. Для меня слишком долго и с азов, но порядок тот же: Swift → UIKit → SwiftUI → HIG, Git, REST, MVVM, Core Data

### [Android-разработчик погружается в iOS через KMP — МТС](https://habr.com/ru/companies/ru_mts/articles/923366/)
**+32** — полезно, если на работе KMP

- Корутины и Flow из Swift напрямую неудобны: нужны обёртки, `@MainActor`, `Task`, cold Flow ведёт себя неожиданно при нескольких подписчиках, при неаккуратной отмене — утечки
- На iOS модуль подключается не одной строкой в Gradle, а собранным `.xcframework` через SPM/Podfile
- Xcode кэширует сборку неправильно — периодически чистить DerivedData
- SwiftUI концептуально близок к Compose — проблем с UI у андроидщика не было

### [Опыт Android и iOS в одном проекте — NFCKEY](https://habr.com/ru/companies/nfckey/articles/797683/)
+4, но пара наблюдений полезна

- SwiftUI быстрее в разработке, но **у Apple нет "официальной" архитектуры** — каждый делает по-своему (MVVM в UIKit, упрощённый MV в SwiftUI)
- В РФ в вакансиях **UIKit всё ещё популярнее**, SwiftUI больше на Западе

### Общий вывод
Все сходятся: Swift → UI (оба фреймворка, но в первую очередь тот, что на проекте) → платформа → многопоточность и память. Готовых роадмапов именно "для андроидщика" на хабре почти нет — отсюда упор на [маппинг](iosVsAndroid.md#что-на-что-похоже) и [грабли](nonObvious.md)
