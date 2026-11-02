# Layots 

[источник](https://developer.alexanderklimov.ru/android/theory/layout.php)

Существует несколько стандартных типов разметок:

- FrameLayout
- LinearLayout
- TableLayout
- RelativeLayout
- GridLayout
- SwipeRefreshLayout
- ConstraintLayout
- CoordinatorLayout

Все описываемые разметки являются подклассами **ViewGroup** и наследуют свойства, определённые в классе **View**.

### Комбинирование

Компоновка ведёт себя как элемент управления и их можно группировать. Расположение элементов управления может быть вложенным. Например, вы можете использовать RelativeLayout в LinearLayout и так далее. Но будьте осторожны: слишком большая вложенность элементов управления вызывает проблемы с производительностью.