## LiveData

**LiveData** — это класс, который является частью Android Jetpack и представляет собой контейнер данных, который позволяет наблюдать за изменениями данных в рамках жизненного цикла Android-компонентов (например, `Activity` или `Fragment`).

### Основные особенности LiveData:
- **Привязан к жизненному циклу:** LiveData автоматически управляет подписчиками в зависимости от жизненного цикла компонента (например, если `Activity` или `Fragment` уничтожаются, LiveData автоматически удаляет соответствующие наблюдатели).
- **Однократная отправка данных:** LiveData по умолчанию отправляет данные только активным наблюдателям. Это значит, что новые подписчики получают последнее значение, только если они становятся активными (например, `onResume`).
- **Нет backpressure:** LiveData не поддерживает backpressure (управление потоком данных), и она всегда хранит только одно значение.
- **Ориентирован на работу с UI:** LiveData обычно используется в архитектуре MVVM для передачи данных между ViewModel и UI-компонентами.

### Пример использования LiveData:
```kotlin
val data: LiveData<String> = MutableLiveData()

// Подписка на изменения
data.observe(this, Observer { newValue ->
    // Обновить UI
})
```

### Виды

[источник](https://nuancesprog.ru/p/18560/)

#### SingleLiveEvent

SingleLiveEvent  —  это подкласс LiveData, предназначенный для обработки событий в приложениях Android. Он решает проблему повторного срабатывания событий при изменении конфигурации (например, при повороте экрана) в процессе использования LiveData.

SingleLiveEvent позволяет обеспечить отправку событий только один раз и избежать проблем с дублированием событий, обрабатываемых наблюдателями.

Пример 
```kotlin
class SingleLiveEvent<T> : MutableLiveData<T>() {
    private val mPending = AtomicBoolean(false)
    @MainThread
override fun observe(owner: LifecycleOwner, observer: Observer<in T>) {
        if (hasActiveObservers()) {
            Log.w(TAG, "Multiple observers registered but only one will be notified of changes.")
        }
        // Наблюдение за внутренним MutableLiveData
        super.observe(owner) { t ->
            if (mPending.compareAndSet(true, false)) {
                observer.onChanged(t)
            }
        }
    }
    @MainThread
    override fun setValue(t: T?) {
        mPending.set(true)
        super.setValue(t)
    }
}
```

#### MediatorLiveData

MediatorLiveData  —  это еще один подкласс LiveData, который позволяет объединить несколько источников LiveData в один. Это может быть полезно, когда нужно наблюдать за несколькими источниками данных и реагировать на обновления любого из них.

Пример
```kotlin
val mediatorLiveData = MediatorLiveData<String>()
val source1: LiveData<String> = MutableLiveData()
val source2: LiveData<String> = MutableLiveData()

mediatorLiveData.addSource(source1) { s ->
    mediatorLiveData.value = s
}

mediatorLiveData.addSource(source2) { s ->
    mediatorLiveData.value = s
}

mediatorLiveData.observe(this, Observer { s ->
    Log.d(TAG, "onChanged: $s")
})
```