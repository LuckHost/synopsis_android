# GridLayout

Похож на [TableLayout](TableLayout.md), но куда удобнее

Можно растягивать элементы на несколько клеток, не нужно указывать элемент для каждой ячейки 

```xml
<GridLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:columnCount="2"
    android:rowCount="2">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Элемент 1" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Элемент 2" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Элемент 3" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Элемент 4" />
</GridLayout>
```