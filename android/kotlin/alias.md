# Alias 

[источник](https://www.geeksforgeeks.org/kotlin-types-aliases/)

Помогает в случае, если у нас есть два класса с одинаковыми именами, импортирвоать один из них (или просто создать) с другим именем 

`typealias course = com.gfg_practice.apps.courses `

```kotlin
// here we define a type alias named Login_details
// which will take two strings as parameters
typealias Login_detials = Pair <String, String>
fun main() {
    // we are setting two users but we don't
    // have to define Pair each time while using it
    val my_details = Login_detials("Username1", "Password1")
    //instead we can directly use our type alias Credentials.
    val my_details2 = Login_detials("Username2", "Password2")
 
    println(my_details)
    println(my_details2)
}
```