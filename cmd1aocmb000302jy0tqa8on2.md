---
title: "Kotlin Scope functions"
seoTitle: "Understanding Kotlin's Scope Functions"
seoDescription: "Explore Kotlin's scope functions `let`, `run`, `with`, `apply`, and `also` for improved code readability and concise object manipulation"
datePublished: Sun Jul 13 2025 06:29:38 GMT+0000 (Coordinated Universal Time)
cuid: cmd1aocmb000302jy0tqa8on2
slug: kotlin-scope-functions-870475a32f05
canonical: https://medium.com/@mohammedkhudair57/kotlin-scope-functions-870475a32f05
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1752065088149/619e18de-04fb-4289-97bc-252941aa397f.png
tags: android-development, android, kotlin, let, apply, run, scope-function, with, kotlin-scope-function, also

---

> **“<mark>It took me days and several cups of coffee to craft this story, so it’s a comprehensive guide packed with examples for each scope function. By the end, you’ll be able to use them with confidence</mark>”**

# **Overview**

Kotlin offers a powerful feature known as **scope functions**, which enhance code readability and maintainability by simplifying object manipulation within a defined scope. These functions provide an elegant way to access and modify objects without explicitly referencing their names.

## **What Are Scope Functions?**

Well, Kotlin offers five main scope functions — `let`, `run`, `with`, `apply`, and `also`—to execute code within an object's context. When invoked on an object, they allow access to it within their block without using its name. These functions vary in how they provide object access and their return values, making them useful for diverse scenarios.

Please take a look at this diagram here. Don’t worry, we’ll go over each one.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752065084245/26d1b332-3c96-49f9-a345-81b5570f6970.png align="left")

## What is the point of Scope functions?

✅ **Cleaner** ✅ **Safer ✅ More Focuse**

**Scope functions in Kotlin** simplify object handling by letting you operate within a defined scope without repeating the object’s name.  
 They boost readability and maintainability by grouping related operations and reducing boilerplate code.  
 While they don’t `add new features`, they make your code cleaner, concise, and more expressive.

---

# Get Started with the Functions

### Let

The `let` function in Kotlin allows you to perform operations on an object and then return the result of those operations. Inside the `let` block, you can access the object using `it` (or a custom name you choose). This is useful for transforming objects or performing calculations on them without affecting the original object.

It’s also excellent for chaining calls or when you want to introduce a local variable with a limited scope inside `let`

**Note:** The common use case of `let`

`let` Is your go-to scope function when you need to perform operations on a nullable object and want to ensure those operations only execute if the object is not null, by using the safe call operator `?.`

**The context object** is available as an argument `it`.

**The return value** is the lambda result.

#### **Example 1**

**Basic example use case**

```kotlin
fun main() {

        val books = listOf("Atomic Habits", "Feel Good Productivity", "Show Your Work")

        val bookInfo = books.let { it ->
            // 'it' is the 'books' list here
            val numberOfBooks = it.size
            val firstBookTitle = it.firstOrNull() 

            "You have $numberOfBooks books. The first one is \"$firstBookTitle\"."
            // Let returns this descriptive string
        }
        println(bookInfo)
    }

// Output: You have 3 books. The first one is "Atomic Habits".
```

We use`let` to perform calculations or retrieve specific information *about* the list and return that information.

#### Example 2:

**For a nullable use case. Using** `**?.let**` **for Safe Execution**

```kotlin
fun main() {
    val movieTitle: String? = "   Avengers: Endgame   "

// If movieTitle is not null, the lambda inside let executes.
    val formattedTitle = movieTitle?.let { title ->
        println("Original title: \"$title\"")
        title.uppercase()
            .trim()
            .subSequence(0, 15)//Extracts just the first 15 characters.
    }
    println("Formatted title: \"$formattedTitle\"")
}

// Output: Original title: "   Avengers: Endgame   "
//         Formatted title: "AVENGERS: ENDGA"
```

To perform actions on a non-null object, use the safe call operator `?.` and call `let` .

#### Example 3

**Real-world Scenario**:

You can take a look at this exam here. We update the user profile UI using `Jetpack Compose` components.

```kotlin
@Composable
fun UserProfileScreen(viewModel: ProfileViewModel) {
    val user by viewModel.currentUser.collectAsState()

    Column(modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        user?.let { userData ->
            // User is not null - show profile
            AsyncImage(
                model = userData.avatarUrl ?: R.drawable.default_avatar,
                contentDescription = "Profile picture"
            )
            Text(
                text = userData.displayName,
                style = MaterialTheme.typography.headlineMedium
            )

            userData.bio?.let { bio ->
                Text(
                    text = bio,
                    style = MaterialTheme.typography.bodyMedium,
                )
            }
            
            userData.membership?.let {
                Badge(
                    containerColor = when (it.tier) {
                        "PREMIUM" -> MaterialTheme.colorScheme.primary
                        "PRO" -> MaterialTheme.colorScheme.tertiary
                        else -> MaterialTheme.colorScheme.secondary
                    }
                ) {
                    Text(it.tier)
                }
            }
            
        } ?: ShowGuestUi()  // User is null - show guest UI
    }
}
```

### **With**

The `with` function in Kotlin is used to execute a block of code within the context of an object, similar to other scope functions. However, unlike `let` or `apply`, `**with**` **is NOT an extension function**. Instead, you pass the object you want to work with as an argument directly into `with(yourObject) { ... }`.

**Why and When to Use** `**with**`**?**

We typically use `with`when:

1. **You want to perform multiple operations or access many properties on a single object.** Instead of writing `object.property1`, `object.methodA()`, `object.property2`, you can just write `property1`, `methodA()`, `property2` inside the `with` block, making your code much more concise and readable. It's like saying, "`With *this* object, do all these things.`"
    
2. **You don’t need to use the** `**with**` **function's return value for subsequent operations.** While `with` *does* return the result of its last expression, its primary purpose isn't usually about transforming the object and returning a new value (like `let`). It's more about grouping actions on an object.
    

**The context object** is available as a receiver `this`.

**The return value** is the lambda result.

#### Example 1

Send email Ex

```kotlin
class Email {
    var to: String = ""
    var subject: String = ""
    var body: String = ""
    var isUrgent: Boolean = false
fun send() {
        println("Sending email to: $to \n Subject: $subject \n Body: $body")
    }
}
fun main() {
    val email = Email()
    
    with(email) {
        to = "customer@example.com"
        subject = "Order Confirmation"
        body = "Thank you for your purchase!"
        isUrgent = false
        send()
    }
}
/* Output: Sending email to: customer@example.com 
           Subject: Order Confirmation 
           Body: Thank you for your purchase!
*/
```

#### Example 2

Imagine you want to prepare an object with many properties.

```kotlin
data class Person(
    var id: Long = 0,
    var name: String = "",
    var email: String = "",
    var phone: String = "",
    var address: String = "",
    var dateCreated: Long = 0,
    var lastModified: Long = 0,
    var isActive: Boolean = true
)

fun main() {
    val newPerson = Person()

    val result = with(newPerson) {
        id = 0
        name = "Bob Smith"
        email = "bob@example.com"
        phone = "555-987-6543"
        address = "456 Oak Ave, Othertown, OT 67890"
        dateCreated = System.currentTimeMillis()
        lastModified = System.currentTimeMillis()
        isActive = true

//      Lambda result, if returning something is necessary.
        "${saveToFavDatabase(newPerson)} \n Successfully saved"

    }

    println(result)
}

fun saveToFavDatabase(newPerson: Person) = newPerson


/*
 Output:
 Person(id=0, name=Bob Smith, email=bob@example.com, phone=555-987-6543, address=456 Oak Ave, Othertown, OT 67890, dateCreated=1747916450453, lastModified=1747916450453, isActive=true).
 Successfully saved
*/
```

#### Example 3

**Real-world Scenario**: Setting Up AlertDialog

```kotlin
class DialogHelper(private val context: Context) {
    
    fun showDeleteConfirmationDialog(itemName: String, onConfirm: () -> Unit) {
        val builder = AlertDialog.Builder(context)
        
        with(builder) {
            setTitle("Delete Item")
            setMessage("Are you sure you want to delete '$itemName'?")
            setIcon(R.drawable.ic_warning)
            setCancelable(false)
            setPositiveButton("Delete") { dialog, _ ->
                onConfirm()
                dialog.dismiss()
            }
            setNegativeButton("Cancel") { dialog, _ ->
                dialog.dismiss()
            }
            create().show()
        }
    }
}
```

### Run

The `run` function in Kotlin is a versatile scope function that essentially **combines the functionalities of** `**with**` **and** `**let**`. We can also use it on nullable objects, while `with` cannot.

**Why and When to Use** `**run**`**?**

`run` is particularly useful in two main scenarios:

1. **When you want to perform operations on an object and then compute a return value.** It's perfect for when your block both "initializes objects and computes the return value" or simply performs multiple steps and yields a final result.
    
2. **When you need to execute a block of code where you don’t have a receiver object, but you still want to get a result.** In this case, you can call `run` directly without a receiver (`run { ... }`). It acts as a non-extension function block that immediately executes and returns the result of its last expression, often used for setting up temporary variables or executing a complex computation and returning its outcome.
    

**The context object** is available as a receiver `this`.

**The return value** is the lambda result.

#### Example 1

Initialize an order using `run`

```kotlin
data class Order(
    var id: String = "0",
    var item: String? = "0",
    var quantity: Int? = 0,
    var status: String = "Pending"
)

fun main() {
    val newOrder = Order()

    // Here, we use this or the object's property itself, and you can invoke it on a nullable object as well.
    val orderConfirmationMessage = newOrder?.run {
        id = "M123"
        item = "Monitor"
        quantity = 2
        status = "Confirmed"

        "Order ${id} for $quantity $item(s) is $status! Expect delivery soon."
    }

    println(orderConfirmationMessage)
}

// Output: Order M123 for 2 Monitor(s) is Confirmed! Expect delivery soon.
```

#### Example 2

Using `run` as a **non-extension** **function** to calculate the discount.

```kotlin
fun main() {
    val basePrice = 150.0
    val loyaltyPoints = 750

    val finalPrice = run {
        // Variables declared within `run` stay local and do not extend to the main function's scope.

        val discountRate = when {
            loyaltyPoints >= 1000 -> 0.15 // 15% discount for 1000+
            loyaltyPoints >= 500 -> 0.10 // 10% discount for 500-999
            else -> 0.0 // No discount
        }

        val discountAmount = basePrice * discountRate
        val priceAfterDiscount = basePrice - discountAmount

        // The last expression is the return value of the 'run' block
        priceAfterDiscount
    }

    println("Base Price: $basePrice")
    println("Loyalty Points: $loyaltyPoints")
    println("Final Price after discount: $finalPrice")
}
/* Output:
 Base Price: 150.0
Loyalty Points: 750
Final Price after discount: 135.0     */
```

#### Example 3

**Real-world Scenario**: Formatting Product Details.

```kotlin
// Our simple data class for a product
data class Product(val name: String, val price: Double, val inStock: Int)

@Composable
fun ProductDisplayCard(product: Product) {
    val displayDetails = product.run {
        // We can directly access 'name', 'price', and 'inStock' without 'product.'.

     val stockStatusText =
        if (inStock > 0) "In Stock: $inStock units"
        else "Out of Stock!"

        // Format the price to two decimal places
        val formattedPrice = "%.2f".format(price)

        // The last expression in the 'run' block
        "Name: $name\nPrice: $$formattedPrice\nStatus: $stockStatusText"
    }

    Column(
        modifier = Modifier.padding(16.dp) 
            .background(MaterialTheme.colorScheme.surfaceContainerHigh, MaterialTheme.shapes.medium) // Card background
            .padding(16.dp)
    ) {
        Text(
            text = displayDetails,
            style = MaterialTheme.typography.bodyLarge,
        )
    }
}

@Composable
fun ProductDisplayCardPreview() {
    MaterialTheme {
        Column {
            ProductDisplayCard(product = Product("Smartwatch", 199.99, 15))
            ProductDisplayCard(product = Product("Bluetooth Speaker", 49.50, 0))
            ProductDisplayCard(product = Product("Gaming Headset", 75.00, 30))
        }
    }
}
```

### Apply

The `apply` function is a scope function that **returns the original object** after executing a block of code on it. Think of it as saying `take this object, apply these changes to it, then give me back the same object`.`**apply**` **doesn’t return a lambda expression, if we assign it to a variable, it will be the object itself.**

`Apply` **is the perfect fit when:**

* **Configuring Objects:** You want to set up multiple properties or call several initialization methods on an object. It’s like saying, “**Apply these settings to this object.**”
    
* **Chaining Operations:** Because `apply` returns the context object itself; it's excellent for `fluent APIs` or for continuing a chain of operations where you need to modify an object in place, and then pass *that same modified object* along.
    
* **Code Blocks That Don’t Return a Value:** If the main purpose of your block is to perform side effects (like setting properties) and you don’t need a specific return value from the block itself
    

**The context object** is available as a receiver `this`.

**The return value** is the `object itself`.

#### Example 1

Configuring an object

```kotlin
data class Movie(var title: String = "", var director: String = "", var releaseYear: Int = 0, var genre: String = "")

fun main() {
    val favoriteMovie = Movie().apply {
//Now, Thse type of favoritMovie, is the Movie() object
        title = "Inception"
        director = "Christopher Nolan"
        releaseYear = 2010
        genre = "Sci-Fi"
    }
    println(favoriteMovie)

// Output: Movie(title=Inception, director=Christopher Nolan, releaseYear=2010, genre=Sci-Fi)
```

#### Example 2

You can use `apply` in multiple call chains for more complex processing.

```kotlin
data class Movie(var title: String = "", var director: String = "", var releaseYear: Int = 0, var genre: String = "")

fun main() {
    val favoriteMovie = Movie().apply {
        title = "Inception"
        director = "Christopher Nolan"
        releaseYear = 2010
        genre = "Sci-Fi"

    }.apply { // This second `apply` receives the *modified* object from the first `apply`

        saveToFavoriteDatabase(this) // `this` means the object receiver (Movie)
        println("My Favorite Movie is Saved!")
         // Other code goes here...
    }
}

fun saveToFavoriteDatabase(favoriteMovie: Movie) {
    // Simulate saving the movie to a database
}
// Output: My Favorite Movie Saved!
```

#### Example 3

Real-World Scenario: **Style Configuration**

```kotlin
@Composable
fun StyledText() {
    val headlineStyle = TextStyle().apply {
        fontSize = 24.sp
        fontWeight = FontWeight.Bold
        color = MaterialTheme.colorScheme.primary
        letterSpacing = 1.2.sp
        lineHeight = 32.sp
        fontFamily = FontFamily.Serif
    }
    
    val bodyStyle = MaterialTheme.typography.bodyLarge.apply {
        color = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.8f)
        lineHeight = 24.sp
    }
    
    Column {
        Text(
            text = "Custom Headline",
            style = headlineStyle
        )
        Text(
            text = "This is body text with custom styling applied.",
            style = bodyStyle
        )
    }
}
```

### Also

The `also` function in Kotlin is primarily used for **performing side effects** on an object, where the side effect needs access to the object itself, or when you don’t want to shadow the `this` reference from an outer scope.

**Think of it as:** “and also do the following with the object, but don’t mess with the flow”.

**The context object** is available as an argument `it`.

**The return value** is the object itself.

#### Example 1

Modifying a Book List with Logging

```kotlin
fun main() {
    val bookTitles = mutableListOf("Clean Code", "Refactoring", "The Pragmatic Programmer")
    bookTitles
        .also { println("Before adding new book: $it") }
        .add("Domain-Driven Design")

    println("After adding new book: $bookTitles")
}
// Output:
// Before adding new book: [Clean Code, Refactoring, The Pragmatic Programmer]
// After adding new book: [Clean Code, Refactoring, The Pragmatic Programmer, Domain-Driven Design]
```

#### Example 2

Logging And Debugging

```kotlin
data class User(var id: Int=0, var name: String="", var email: String="")
fun main() {
    val user = User().apply {
        id = 1
        name = "Mohammed"
        email = "example@gmail.com"
    }.also {
        println("User created: ${it.name} id: ${it.id}")
        logUserCreation(it)
        validateUser(it)
    }
}

    fun logUserCreation(user: User) = println("`LOG` New user registered: ${user.email}")

    fun validateUser(user: User) {
        require(user.email.contains("@")) { "Invalid email format" }
        println("`VALIDATION` User ${user.id} is valid")
    }

// Output:
// User created: Mohammed id: 1
// `LOG` New user registered: example@gmail.com
// `VALIDATION` User 1 is valid
```

#### Example 3

**Real-World Scenario:** Chaining Example: Creating, Logging, and Saving a Book

```kotlin
val book = Book().apply {
    title = "Clean Code"
    author = "Robert C. Martin"
    pages = 464
    isAvailable = true
}.also {
    println("Book created: '${it.title}' by ${it.author}")
    bookRepository.save(it) // Save to database or in-memory store
    analytics.trackEvent("BookAdded", mapOf("title" to it.title, "pages" to it.pages))
}
```

It’s crucial that `bookRepository.save(it)` receives **the same** `**Book**` **instance** created by `apply`. That’s the key advantage of using `also,`It allows you to perform actions on the exact same object without modifying or breaking the chain.

#### Example 4

**Real-World Scenario**: Fetching Game Data with `also`

```kotlin
val game = gameApi.fetchGameDetails(gameId)
    .also { logger.info("Fetched game data: ${it.title}") }       // Log the game
    .also { gameCache.save(it.id, it) }                            // Cache it
    .also { analytics.trackEvent("GameViewed", mapOf("id" to it.id, "title" to it.title)) } // Track
```

## Bonus

### takeIf and takeUnless

Beyond the main scope functions, Kotlin’s standard library offers `takeIf` and `takeUnless` for embedding state checks directly within call chains.

* `**takeIf**`: Returns the object if it **satisfies** a given condition (predicate); otherwise, it returns `null`. It acts as a filter for a single object.
    
* `**takeUnless**`: It’s the opposite logic of `takeIf` , returns the object if it **does not satisfy** a given condition (predicate); otherwise, it returns `nul`.
    

In both, the object is available as the lambda argument (`it`).

```kotlin
fun main() {
    val number = Random.nextInt(100)

    val evenOrNull = number.takeIf { it % 2 == 0 }
    val oddOrNull = number.takeUnless { it % 2 == 0 }
    println("even: $evenOrNull, odd: $oddOrNull")
}
// Output:
// even: 48, odd: null. 
Your answer would mostly be different.
```

**Note**

> When chaining other functions after `takeIf` and `takeUnless`, don't forget to perform a null check or use a safe call (`?.`) because their return value is nullable.

```kotlin
fun main() {
    val str = "Hello"
    val caps = str.takeIf { it.isNotEmpty() }?.uppercase()
    //val caps = str.takeIf { it.isNotEmpty() }.uppercase() //compilation error
    println(caps)
}
// Output:
// HELLO
```

---

# When to Use What

It’s very common to feel confused by scope functions at first. The trick is to focus on **what you want to do with the object** and **what you expect back** from the block.

*Here’s a simple guide to help you choose.*

## **Ask Yourself These Questions:**

**1**. Are you just setting up/configuring an object, and want the *same object back*?

**Use** `apply`: This is its primary job.

**2**. Are you performing a *side effect* (like logging, debugging, adding to a list) *with* the object, but don’t want to change the main flow or the object’s type?

**Use** `also`: It's perfect for non-intrusive operations.

**3**. Is the object *nullable*, and you want to perform actions *only if it’s not null*? OR, do you want to *transform* the object into a *different kind of result*?

**Use** `let`: Great for null-safety and when the output of the block is a new value.

**4**. Do you need to perform multiple operations on an object (like `with`), **AND** you also want to *compute and return a specific value* from that block, like `let` ?

**Use** `run` It's a combination of `with` and `let` (returns a result).

**5**. Are you doing a bunch of things *with an object you already have*, but don’t intend to chain it further, and maybe you want a simple return from the block?

**Use** `with`: This is a standalone function where you pass the object as an argument.

> Simplified overview diagram

![Simplified overview diagram Image](https://cdn.hashnode.com/res/hashnode/image/upload/v1752065085972/0c9f10c3-cf1f-4e5b-83df-e9eac433704f.png align="left")

# Conclusion

And that’s our deep dive into Kotlin’s **powerful scope functions**!

You now have the key concepts of **Scope Functions.** Put them into practice, and feel free to revisit this guide anytime you need a refresher

So, **next time** you’re crafting Kotlin code, remember the unique strengths of each scope function. Experiment with them, and you’ll likely discover new ways to make your code cleaner and more effective.

> **See every challenge as a puzzle waiting to be solved. Enjoy the process!**

---

## Follow me on:

[X](https://x.com/mohammedkhuda19) | [LinkedIn](https://www.linkedin.com/in/mohammed-khudair-4797b8177/) | [YouTube](https://www.youtube.com/@AndroidDojo) | [Instagram](https://www.instagram.com/androiddojo64/)