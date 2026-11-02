# Bundle

Традиционный способ передачи информации между фрагментами, набор данных

В одном фрагменте кладем в тип Bundle, в другом извлекаем 

**Место отправки**
```kotlin
// creating the instance of the bundle
val bundle = Bundle()
 
// storing the string value in the bundle 
// which is mapped to key
bundle.putString("key1", "Gfg :- Main Activity")
 
// creating a intent
intent = Intent(this@MainActivity, SecondActivity::class.java)
 
// passing a bundle to the intent
intent.putExtras(bundle)
 
// starting the activity by passing the intent to it.
startActivity(intent)
```

**Место получения**
```kotlin

// getting the bundle back from the android
val bundle = intent.extras
 
// performing the safety null check
var s:String? = null
 
// getting the string back
s = bundle!!.getString("key1", "Default"))
```

[иточник кода по Bundle тык](https://www.geeksforgeeks.org/bundle-in-android-with-example/)

# SafeArgs
Рекомендуемый
Типо-безобастный способ передачи

По своей сути - обертка для Bundle 

**Создается класс для отправки**

```kotlin
@Parcelize
data class User( 
    val name : String ="", 
    val email : String= "", 
    val password : String =""
  
) : Parcelable
```

**Отправляется**

```kotlin
val name = binding.etName.text.toString() 
val email = binding.etEmail.text.toString() 
val password = binding.etPassword.text.toString() 
  
// create user object and pass the  
  // required arguments 
// that is name, email,and password 
val user = User(name,email, password) 
  
// create an action and pass the required user object to it 
// If you can not find the RegistrationDirection try to "Build project" 
val action = RegistrationDirections.actionRegistrationToDetails(user) 
  
// this will navigate the current fragment i.e  
// Registration to the Detail fragment 
findNavController().navigate( 
    action 
) 
```

**И принимается**

```kotlin
var binding = FragmentDetailsBinding.inflate(inflater, container, false) 
val view = binding.root 

// Receive the arguments in a variable 
val userDetails = args.user 
```

[источник кода по SafeArgs тык](https://www.geeksforgeeks.org/how-to-pass-data-to-destination-using-safe-args-in-android/)


---
[источник информации тык](https://www.youtube.com/watch?v=dqgEm3vkt_I)


