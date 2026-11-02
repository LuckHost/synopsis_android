# DiffUtil

[основной источник](https://startandroid.ru/ru/blog/504-primer-ispolzovanija-android-diffutil.html)
[второй источник](https://habr.com/ru/companies/redmadrobot/articles/460673/)

Класс, который позволяет обновлять список RecycleView, не пересобирая его полностью

Посмотрим на практике
```Java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.activity_main);
 
   
   // ...
 
   List<Product> productList = new LinkedList<>();
   productList.add(new Product(1, "Name1", 100));
   productList.add(new Product(2, "Name2", 200));
   productList.add(new Product(3, "Name3", 300));
   productList.add(new Product(4, "Name4", 400));
   productList.add(new Product(5, "Name5", 500));
 
   adapter.setData(productList);
   adapter.notifyDataSetChanged();
 
}
```

А также добавим логи

```Java
@Override
public void onBindViewHolder(ProductHolder holder, int position) {
   Log.d(TAG, "bind, position = " + position);
   holder.bind(data.get(position));
}
```

Напишем метод для кнопки 
```Java
void onUpdateClick() {
   List<Product> productList = new LinkedList<>();
   productList.add(new Product(1, "Name1", 100));
   productList.add(new Product(2, "Name21", 200));
   productList.add(new Product(3, "Name3", 300));
   productList.add(new Product(4, "Name4", 400));
   productList.add(new Product(5, "Name5", 501));
 
   adapter.setData(productList);
   adapter.notifyDataSetChanged();
}
```

Посмотрим логи после нажатия

```
bind, position = 0
bind, position = 1
bind, position = 2
bind, position = 3
bind, position = 4
```

Как видно, каждый из элементов был перерисован, хоть изменилось всего два

Есть выход, можно перерисовывать элементы точечно

```Java
void onUpdateClick() {
    List<Product> productList = new LinkedList<>();
    productList.add(new Product(1, "Name1", 100));
    productList.add(new Product(2, "Name21", 200));
    productList.add(new Product(3, "Name3", 300));
    productList.add(new Product(4, "Name4", 400));
    productList.add(new Product(5, "Name5", 501));
 
    adapter.setData(productList);
    adapter.notifyItemChanged(1);
    adapter.notifyItemChanged(4);
}
```

Смотрим вывод

```
bind, position = 1
bind, position = 4
```

Теперь мы перерисовали все вручную
Но не всегда удается узнать, какие именно элементы были изменены, например, когда мы работаем с внешним источником данных
Для этого существует DiffUtil. Он сравнивает два набора данных и выяснит, где произошло изменение, после чего с помощью notify оптимально обновит адаптер

Реализуем наш DiffUtil

```Java
public class ProductDiffUtilCallback extends DiffUtil.Callback {
 
   private final List<Product> oldList;
   private final List<Product> newList;
 
   public ProductDiffUtilCallback(List<Product> oldList, List<Product> newList) {
       this.oldList = oldList;
       this.newList = newList;
   }
 
   @Override
   public int getOldListSize() {
       return oldList.size();
   }
 
   @Override
   public int getNewListSize() {
       return newList.size();
   }
 
   @Override
   public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
       Product oldProduct = oldList.get(oldItemPosition);
       Product newProduct = newList.get(newItemPosition);
       return oldProduct.getId() == newProduct.getId();
   }
 
   @Override
   public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
       Product oldProduct = oldList.get(oldItemPosition);
       Product newProduct = newList.get(newItemPosition);
       return oldProduct.getName().equals(newProduct.getName())
               && oldProduct.getPrice() == newProduct.getPrice();
   }
}
```

Разница между `areItemsTheSame` и `areContentsTheSame` в том, что DiffUtil сначала сравнивает элементы (`areItemsTheSame`), а потом, если они равны (это один и тот же элемент), сравнивает их содержимое (`areContentsTheSame`). Смысл сравнивать содержимое разынх элментов?

Теперь проверим работу

```Java
void onUpdateClick() {
    List<Product> productList = new LinkedList<>();
    productList.add(new Product(1, "Name1", 100));
    productList.add(new Product(2, "Name21", 200));
    productList.add(new Product(3, "Name3", 300));
    productList.add(new Product(4, "Name4", 400));
    productList.add(new Product(5, "Name5", 501));
 
    ProductDiffUtilCallback productDiffUtilCallback = 
            new ProductDiffUtilCallback(adapter.getData(), productList);
    DiffUtil.DiffResult productDiffResult = DiffUtil.calculateDiff(productDiffUtilCallback);
 
    adapter.setData(productList);
    productDiffResult.dispatchUpdatesTo(adapter);
}
```

Вывод логов
```
bind, position = 1
bind, position = 4
```

Все прекрасно работает

Помимо этого существует getChangePayload и DiffUtil.DiffResult calculateDiff (DiffUtil.Callback cb, boolean detectMoves)

На данный момент их предназаначение описываться не будет 
