# Обработчик нажатий

[источник здесь тык](https://ru.stackoverflow.com/questions/423286/Как-добавить-обработчик-нажатия-на-элемент-в-recyclerview)

Для начала, почему же google не реализовала интерфейс `onItemClickListener` в своем новом виджете `RecyclerView`.
Объясняется это тем, что данный интерфейс был весьма не совершенен и, в частности, создавал определенные трудности для обработки кликов на вложенных элементах айтема, также были определенные проблемы с получением реальной позиции и некоторые другие.
Чтобы избавить себя от подобных проблем google решила доверить реализацию этого сомнительного действия самому программисту ... что же, какие у нас есть варианты.

В первую очередь нужно понять, какого экшена мы чаще всего ожидаем от клика по элементу списка?
Как правило это переход к другой активити со значением кликнутой позиции (каких то связанных с этой позицией данных адаптера и тп).

Реализовать такое простое действие можно просто повешав слушатель прямо в адаптере. Здесь у нас есть доступ к текущей позиции и всем данным адаптера:

```Java
 @Override
 public void onBindViewHolder(ItemHolder holder, int position) {

            holder.someView.setOnClickListener (new View.OnClickListener() {
            @Override
            public void onClick(View v) {
               // action on click
            }
        });
  }
```

Однако IP696 в своем комментарии был прав, вешать сложную логику (если такая вдруг потребовалась для обработки клика по айтему) в адаптер не разумно.

Следующий вариант - положить на доводы google и реализовать интерфейс onItemClicklListener() самостоятельно.
При такой реализации мы можем передать "на сторону" через интерфейс множество полезных вещей: позицию, данные адаптера по кликнутому айтему, view айтема и тд. , но лишаем себя удобной возможности простым способом обрабатывать клики на дочерних элементах, как это можно было сделать в первом варианте и о чем предупреждал нас google. Кроме того, фактически, как правило, нет никакой прямой необходимости обрабатывать клик по айтему на "внешней" стороне. Пример:

```Java
public class ListAdapterHolder extends RecyclerView.Adapter<ListAdapterHolder.ViewHolder> {

    OnItemClickListener mItemClickListener;

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent , int viewType) {
        ...
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder , int position) {
        ...
    }

    public class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {

        public ViewHolder(View view) {
            super(view);               
            view.setOnClickListener(this);
             ...
            //здесь можно повесить слушатель и на отдельные виджеты в айтеме
        }

        @Override
        public void onClick(View v) {
            if (mItemClickListener != null) {
                mItemClickListener.onItemClick(v, getAdapterPosition());
            }
        }

    }

    public interface OnItemClickListener {
        public void onItemClick(View view , int position);
    }

    public void SetOnItemClickListener(final OnItemClickListener mItemClickListener) {
        this.mItemClickListener = mItemClickListener;
    }
}
```