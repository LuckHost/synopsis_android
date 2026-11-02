# ConstraintLayout

[источник](https://developer.alexanderklimov.ru/android/layout/constraintlayout.php)

`ConstraintLayout` является наследником `ViewGroup` и местами похож на `RelativeLayout`, но более продвинут. Код разметки в XML-представлении:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="ru.alexanderklimov.as22.MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Hello World!"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:layout_constraintLeft_creator="1"
        tools:layout_constraintRight_creator="1" />

</android.support.constraint.ConstraintLayout>
```

В основном используется для создания xml разметки в редакторе

Есть полезная функция выравнивания
![image](images/constraint_layout.gif)

Помимо этого в ConstraintLayout есть элементы, которые помогают создавать сложные интерфесы

- Group
- Guideline
- Barrier
- Chains

#### 1. Group

Помогает группировать элементы

```xml
<androidx.constraintlayout.widget.Group
    android:id="@+id/groupExample"
    ... />

    <TextView
    ... />
    <TextView
    ... />
    <TextView
    ... />
```

```kotlin
findViewById<Group>(R.id.groupExample).visibility = View.VISIBLE
```

#### 2. Guideline

Помогает выравнивать элементы относительно определенной линии, которая может быть горизонтальной, либо вертикальной

```xml
<androidx.constraintlayout.widget.Guideline
    android:id="@+id/guidelineVertical"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_percent="0.5" />

<TextView
    android:id="@+id/textView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Aligned with Guideline"
    app:layout_constraintStart_toStartOf="@id/guidelineVertical"
    app:layout_constraintTop_toTopOf="parent" />
```

Здесь `TextView` выравнивается по вертикальной линии, расположенной посередине ширины контейнера 

#### 3. Barrier

Динамическая граница, которая учитывает размеры нескольких элементов

```xml
<TextView
    android:id="@+id/textViewA"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Short Text"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent" />

<TextView
    android:id="@+id/textViewB"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="A much longer piece of text"
    app:layout_constraintTop_toBottomOf="@id/textViewA"
    app:layout_constraintStart_toStartOf="parent" />

<androidx.constraintlayout.widget.Barrier
    android:id="@+id/startBarrier"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:barrierDirection="end"
    app:constraint_referenced_ids="textViewA,textViewB" />

<Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Aligned with Barrier"
    app:layout_constraintStart_toEndOf="@id/startBarrier"
    app:layout_constraintTop_toTopOf="parent" />

```
Кнопка выравнивается по границе, которая учитывает оба `TextView`

#### 4. Chains

Помогает равномерно распределить пространство между элементами. Могут быть вертикальные, горизонтальные, с разными стилями(`spread`, `spread_inside`, `packed`)

```xml
<TextView
    android:id="@+id/textView1"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="Text 1"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toStartOf="@id/textView2"
    app:layout_constraintHorizontal_chainStyle="spread" />

<TextView
    android:id="@+id/textView2"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="Text 2"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toEndOf="@id/textView1"
    app:layout_constraintEnd_toStartOf="@id/textView3" />

<TextView
    android:id="@+id/textView3"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="Text 3"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toEndOf="@id/textView2"
    app:layout_constraintEnd_toEndOf="parent" />

```

Здесь три `TextView` равномерно распределяются по горизонтали, заполняя доступное пространство.