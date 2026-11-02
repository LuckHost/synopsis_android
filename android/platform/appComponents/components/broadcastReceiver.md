# Broadcast Receiver

Позволяет реагировать на состояния вне приложения 

К примеру на отсутствие сети или низкого заряда батареи 

```kotlin
class BatteryReceiver : BroadcastReceiver() {
  override fun onReceive(p0: Context?, p1: Intent?) {

  }
}
```

##### Сообщение системе ожидаемого события

Для начала нужна регистрация компонента в системе

Но не обязательно объявлять в манифесте 
Даже рекомендуется не объявлять, так как они нужны не во все время приложения 

**Способы**
- **Статический** \ регистарция в манифесте
- **Димнамический** \ регистарция программно

Также Broadcast Receiver можно разделить на два подкласса:

- **Упорядоченные** Broadcast Receiver. Упорядоченные и синхронные трансляции. Трансляция с наивысшим приоритетом выполняется первой, затем остальные. Если у двух трансляций одинаковый приоритет, то они выполняются в произвольном порядке.
- **Нормальные** Broadcast Receiver. Нормальные трансляции неупорядоченные и асинхронные. У трансляций нет никакого приоритета, они выполняются в случайном порядке. 

---

# НЕ РЕКОМЕНДУЕТСЯ

В современных Android-приложениях использование **`BroadcastReceiver`** для отслеживания состояния сети больше не рекомендуется, особенно начиная с Android 7.0 (API 24). Вместо этого Android предоставляет более эффективные и современные API, такие как **`ConnectivityManager`** с **`NetworkCallback`**, которые обеспечивают более гибкую и точную обработку изменений состояния сети.

### Почему `BroadcastReceiver` не рекомендуется?

- **Ограничения с Android 7.0**: В Android 7.0 и выше была введена оптимизация батареи, из-за которой `BroadcastReceiver`, зарегистрированные в манифесте, не могут получать некоторые системные широковещательные сообщения (в том числе `android.net.conn.CONNECTIVITY_CHANGE`).
  
- **`NetworkCallback` более эффективен**: Использование `ConnectivityManager.NetworkCallback` позволяет отслеживать изменения сети на более низком уровне и с меньшей задержкой по сравнению с `BroadcastReceiver`, что делает его более предпочтительным.

### Когда использовать `BroadcastReceiver`?

- **Если приложение должно реагировать на другие системные события**: Если вам нужно обрабатывать не только изменения состояния сети, но и другие системные события, такие как включение/выключение режима самолета или другие события, связанные с широковещательными уведомлениями, тогда можно использовать `BroadcastReceiver`.
  
- **Совместимость с более старыми устройствами**: Если вы поддерживаете устройства с API уровня ниже 21 (Android 5.0), то для них всё ещё может быть уместно использовать `BroadcastReceiver`, хотя рекомендуется мигрировать на новые API.

### Как использовать `BroadcastReceiver` (если нужно)

Если всё же требуется использовать `BroadcastReceiver`, то вот пример того, как это можно сделать:

1. **Добавить в `AndroidManifest.xml`:**

```xml
<receiver android:name=".NetworkChangeReceiver" android:enabled="true">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
    </intent-filter>
</receiver>
```

2. **Создать класс `NetworkChangeReceiver`:**

```kotlin
class NetworkChangeReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context?, intent: Intent?) {
        context?.let {
            val isConnected = isNetworkAvailable(it)
            // Реагировать на изменение подключения
            if (isConnected) {
                Toast.makeText(it, "Internet is available", Toast.LENGTH_SHORT).show()
            } else {
                Toast.makeText(it, "Internet is lost", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun isNetworkAvailable(context: Context): Boolean {
        val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
        val activeNetwork = connectivityManager.activeNetworkInfo
        return activeNetwork?.isConnectedOrConnecting == true
    }
}
```

> **Важно**: Этот подход будет работать до Android 7.0, но с ограничениями. Для устройств с более новыми версиями Android рекомендуется использовать подход с **`ConnectivityManager`** и **`NetworkCallback`**, как показано в предыдущих примерах.

### Заключение

Использование **`BroadcastReceiver`** для отслеживания изменений сети устарело, и его следует избегать в пользу **`NetworkCallback`** от **`ConnectivityManager`**. Этот подход является более точным, энергоэффективным и полностью поддерживается в современных версиях Android.