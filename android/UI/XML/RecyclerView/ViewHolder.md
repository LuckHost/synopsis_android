# ViewHolder

Полезный паттерн, который постоянно используется в  Recycler

Позволяет сохранять данные об элементе view, сокращая время и ресурсы для постоянного поиска и создания этих элиментов

Пример
```kotlin
inner class CatViewHolder(private val binding: ItemCatBinding) : RecyclerView.ViewHolder(binding.root) {

        fun bind(cat: Cat) {
            binding.nameTextView.text = cat.name
            binding.ageTextView.text = cat.age.toString()
        }
    }
```

Не стоит перенагружать этот класс, так как он постоянно переиспользуется и достаточно быстро должен отрисовываться 

