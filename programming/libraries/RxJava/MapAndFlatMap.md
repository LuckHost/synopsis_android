# Map and FlatMap 

[источник для map](https://habr.com/ru/articles/265269/)
[источник для flatMap](https://habr.com/ru/articles/265583/)

В RxJava мы можем изменять наши значения в потоках

#### Map

[пример на kotlin](./transformation/map/map.md)

оператор map просто изменяет каждый элемент потока и отдает их в таком виде, в котором принял

```Java
Observable.just("Hello, world!")
    .map(s -> s + " -Dan")
    .subscribe(s -> System.out.println(s));
```

Помимо этого map не обязан возвращать данные того же типа, что и принял

```Java
Observable.just("Hello, world!")
    .map(s -> s.hashCode())
    .subscribe(i -> System.out.println(Integer.toString(i)));
```

#### FlatMap

[пример на kotlin и более подробно](./transformation/map/flatMap.md)


После преобразования возвращает поток индивидуальных элементов

```Java
query("Hello, world!")
    .flatMap(urls -> Observable.from(urls))
    .subscribe(url -> System.out.println(url));
```

Вместо того, чтоб получить `List<url>`, `.subscribe` получит на вход несколько новых `Observable`, в каждом из которых будет в качестве значений в виде url.
По сути, мы просто заменили `List` на `Observable` 