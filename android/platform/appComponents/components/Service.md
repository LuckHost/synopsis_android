# Service

Отвечает за выполнение долгих фоновых операций, не требующих взаимодействия с пользователем

Такие как музыка, трекинг геолокации и тд

```kotlin
class MyService : Service() {
  override fun onBind(p0: Intent?): IBinder? {
    return null
  }

  override fun onStart(intent: Intent?, startId: Int) {
    super.onStart()
  }

  override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
    return super.onStartCommand(intent, flags, startId)
  }

  override fun onDestroy() {
    super.onDestroy()
  }
}
```

Метод `onBind`  служит для создания связи между компонентами
Метод `onStart` запускается перед основной логикой приложения 
Метод `onStartCommand` содержит основную логику сервиса 
Метод `onDestroy` выполняется при закрытии сервиса. Идеален для закрытия ресурсов и освобождения памяти


## Виды сервисов

- **Unbound**
  - **Foreground Service** \ показывает уведомления в статус баре, пока активен
  - **Background Service** \ фоновые процессы, которые не видны пользователю
- **Bound** \ позволяет другим компонентам приложения (например, Activity, Fragment или другой сервис) привязываться к нему для взаимодействия. Существует только пока к нему привязаны клиенты