!!после ревью структуры папок ссылки сбились

---

# Ссылки в Kotlin

[файлик](/android/jvm/lowLevelConcepts/ram_arc.md)

Их всего 4 штуки:

- srtong reference
- soft reference будет удален, как нужна  память
- weack reference будет удален, как все ссылки на него = null
- phantom reference всегда будет возвращать null

## HashMap под капотом 

[hashMap файлик](/android/jvm/collections/hashDataStructures/hashmap.md)
**как работает под капотом** -> [hashSet капотом](/android/jvm/collections/hashDataStructures/hashmap.md)

Есть контейнеры, с помощью хэша объекта и мат. функции вычисляется, в какой контейнер класть объект, также с геттером, поэтому скорость CRUD = O(1)

