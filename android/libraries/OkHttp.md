# OkHttp

Лежит под капотом такой библиотеки, как Retrofit

Позволяет более гибко настривать подключение к сетям, следить за всеми этапами покдлючения и тд.

Преимущества OkHttp:

- **Гибкость**: Библиотека предоставляет больше контроля над процессом сетевого взаимодействием за счёт дополнительных функций, например, пользовательской обработки запросов и ответов.

- **Лёгкость**: OkHttp — более компактная библиотека, чем Retrofit, что позволяет минимизировать размер используемой приложением памяти.

- **Кеширование**: Библиотека имеет встроенную поддержку HTTP‑кеширования, что может повысить производительность и снизить нагрузку на сеть.

- **Аутентификация**: OkHttp предоставляет гибкий и расширяемый API аутентификации, что упрощает реализацию различных её моделей.

- **Перехватчики** (Interceptors): Это механизм, позволяющий легко настраивать запросы и ответы, а также хороший выбор для приложений, требующих расширенной обработки запросов.

- **WebSockets**: OkHttp обеспечивает встроенную поддержку WebSockets, что позволяет легко реализовать коммуникацию с сервером в режиме реального времени.


Синхронный запрос:
```kotlin
val client = OkHttpClient()

val request = Request.Builder()
    .url("https://publicobject.com/helloworld.txt")
    .build()

try {
    client.newCall(request).execute().use { response ->
        if (!response.isSuccessful) {
            throw IOException("Запрос к серверу не был успешен:" +
                    " ${response.code} ${response.message}")
        }
        // пример получения конкретного заголовка ответа
        println("Server: ${response.header("Server")}")
        // вывод тела ответа
        println(response.body!!.string())
    }
} catch (e: IOException) {
    println("Ошибка подключения: $e");
}
```


Асинхронный запрос:
```kotlin
val client = OkHttpClient()

val request = Request.Builder()
    .url("http://publicobject.com/helloworld.txt")
    .build()

client.newCall(request).enqueue(object : Callback {
    override fun onFailure(call: Call, e: IOException) {
        e.printStackTrace()
    }

    override fun onResponse(call: Call, response: Response) {
        response.use {
            if (!response.isSuccessful) {
                throw IOException("Запрос к серверу не был успешен:" +
                        " ${response.code} ${response.message}")
            }
            // пример получения всех заголовков ответа
            for ((name, value) in response.headers) {
                println("$name: $value")
            }
            // вывод тела ответа
            println(response.body!!.string())
        }
    }
})
```