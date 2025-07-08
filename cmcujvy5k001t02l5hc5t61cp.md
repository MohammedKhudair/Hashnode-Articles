---
title: "MVVM Architectural Pattern in Android"
seoTitle: "Understanding MVVM in Android Development"
seoDescription: "MVVM enhances Android app development with better code organization, testability, and maintainability, leading to scalable applications"
datePublished: Tue Jul 08 2025 13:13:06 GMT+0000 (Coordinated Universal Time)
cuid: cmcujvy5k001t02l5hc5t61cp
slug: mvvm-architectural-pattern-in-android
canonical: https://medium.com/@mohammedkhudair57/mvvm-architectural-pattern-in-android-617b12537d4c
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1751981885185/28d75e11-6f18-4749-9ac4-e1df1542c981.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1751981983769/42206702-3e20-40da-a38b-26a626859b86.png
tags: android-app-development, mvvm, kotlin, android-studio, jetpack-compose, architecture-design

---

## Introduction

MVVM is a recognized software design pattern that enhances code maintainability by separating the user interface (View) from the brainy business logic (Model) through an intermediary component called the ViewModel. This approach improves testability and reusability. MVVM overcomes the drawbacks of MVP and MVC, providing a clean and structured codebase. It ensures a clear understanding of crucial logic components in Android applications, facilitating feature management.

With MVVM, understanding what's happening in our Android app is a breeze, making it a snap to add or remove features. Plus, it's a team player during Unit Testing, ensuring our code gets all the attention it deserves without interference from other classes. Go, MVVM! 🚀✨.

## What is MVVM?

"In simple terms, MVVM stands for Model-View-ViewModel. Its primary goal is to enhance the clarity and comprehensibility of our code. MVVM champions testability, maintainability, reusability, and scalability, making it a powerful approach for developing robust and user-friendly applications."

### Main Components

* **Model:** represents the data and business logic of the application, typically does not have any direct knowledge of the user interface.
    
* **View:** This layer represents the user interface elements. It focuses on displaying data and handling user interactions without any logic or knowledge about the data itself.
    
* **ViewModel:** Acts as a bridge between the View and the Model. It exposes data to the View through a mechanism like StateFlow, KotlinFlow, or a similar one. It also manages the lifecycle of the data and ensures the View only receives relevant updates.
    

### Some Of Its Key Features

* **Separation of Concerns:** Here each component has a specific role and responsibility, this enhances reusability and readability, where developers can focus on each component.
    
* **Testability:** Each layer can be tested independently, leading to more robust and reliable apps.
    
* **Maintainability:** MVVM promotes clean and maintainable code by organizing responsibilities into distinct components.
    
* **Scalability:** It is easier to add features or modify existing ones without disrupting the entire application.
    

### How does the MVVM work?

"The View binds to data exposed by the ViewModel, the ViewModel handles events initiated by the View and communicates with the Model, the Model processes events and manages data storage or retrieval, and the updated data flows back to the View through the ViewModel."

![This diagram illustrates communications between the components.](https://cdn.hashnode.com/res/hashnode/image/upload/v1751979718589/54cac85b-4b22-4b9f-9993-dafd5c5a98a1.png align="left")

> This diagram illustrates communications between the components.

## Getting started with MVVM

Before we get started, it would be awesome if you're familiar with some cool tools like Coroutine, Hilt, and StateFlow that make our MVVM pattern journey smoother. In this article, I'll be using Jetpack Compose. Don't worry if you haven't explored Jetpack Compose yet - you can easily apply the same ideas to an Activity or Fragment.

Now let's set up the necessary components. Let's kickstart the journey.

**Project setup** for our ViewModel add the following dependencies to your app-level `build.gradle` file.

```kotlin
dependencies {
... other dependencies...
 implementation ("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.2")
}
```

If you are using Hilt, make sure to add these dependencies. In app-level `build.gradle` file.

```kotlin
plugins {
 ... other plugins...
    id("com.google.dagger.hilt.android")
    kotlin("kapt")
}

... other components ...

dependencies {
... other dependencies...
  implementation("com.google.dagger:hilt-android:2.49")
  kapt("com.google.dagger:hilt-android-compiler:2.44")
}
```

In project-level `build.gradle` file.

```kotlin
plugins {
   ... other plugins...
    id("com.google.dagger.hilt.android") version "2.44" apply false

}
```

If you’re not familiar with setting up Hilt, no problem! Check out this simple guide from the [official Hilt doc](https://developer.android.com/training/dependency-injection/hilt-android#setup). It’s simple!👍

### Define Project Structure

Define the project structure based on the separation of concern. Typically, you will have separate packages of the components

![Image description](https://cdn.hashnode.com/res/hashnode/image/upload/v1751979719803/549359e2-edc6-4e99-a4d0-9e66419e1933.png align="left")

Here’s a glimpse of how I’ve organized my package structure.

### Let’s start with the “data layer”

The data layer is considered as our **Model Component**.

```kotlin
data class Game(
    val id: Int,
    val title: String,
    val rating: Float,
    val review: String
)
```

This is our Game class that holds the data info.

```kotlin
class GameRepository @Inject constructor() {

     suspend fun getGames(): List<Game> {
         // Fake data fetching, replace this with your actual data source
         delay(3000)
        return listOf(
            Game(1, "The Witcher 3: Wild Hunt", 4.8f, "A masterpiece of storytelling and open-world design."),
            Game(2, "Red Dead Redemption 2", 4.9f, "An epic tale of life in America at the dawn of the modern age."),
            Game(3, "Assassin's Creed Valhalla", 4.5f, "Explore a dynamic and beautiful open world set against the brutal backdrop of England's Dark Ages."),
            Game(4, "Cyberpunk 2077", 4.2f, "An open-world action-adventure story set in Night City, a megalopolis obsessed with power, glamour, and body modification."),
           Game(5, "The Legend of Zelda: Breath of the Wild", 4.7f, "Step into a world of discovery, exploration, and adventure in The Legend of Zelda: Breath of the Wild."),
           
           // Add more games as needed
        )
    }
}
```

“Here, we simulate fetching data from a server or local database just to simplify things and focus on the MVVM concept.”

### UI Layer

The ViewModel Component plays a crucial role as part of the MVVM, where we handle events and reduce the view state. The ViewModel acts as a middleman between the View and the Model.

```kotlin
@HiltViewModel
class GameViewModel @Inject constructor(
    private val repository: GameRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(GameUiState(loading = true))
    val uiState: StateFlow<GameUiState> get() = _uiState

    fun getGameList() {
        _uiState.value = GameUiState(loading = true)
        viewModelScope.launch {
            try {
                val games = repository.getGames()
                _uiState.value = GameUiState(loading = false,games = games)
            } catch (e: Exception) {
                _uiState.value = GameUiState(loading = false, error =e.message?:"Network error" )
            }

        }
    }
    // Other methods business goes here..
}
```

let’s dive into the **View Component** elements: Implement the View component responsible for rendering the UI and observing the Model’s state changes.

**GameUiState** This represents the view state on the screen, whether it’s loading or success fetching the data or an error occurs.

```kotlin
data class GameUiState (
    val loading :Boolean = false,
    val games: List<Game> = emptyList(),
    val error: String? = null
)
```

**In the MainActivity** class, I’ve adopted Jetpack Compose, I put the UI design in a separate file

> GameScreen

```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    private val viewModel by viewModels<GameViewModel>()
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MVVM_PatternProjectTheme {
                // A surface container using the 'background' color from the theme
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    GameScreen(viewModel = viewModel)
                }
            }
        }
    }
}
```

**GameScreen file**

```kotlin
// Be careful to add this import for items.
import androidx.compose.foundation.lazy.items

@Composable
fun GameScreen(viewModel: GameViewModel) {
    val gameUiState by viewModel.uiState.collectAsState()


    LaunchedEffect(viewModel) {
        viewModel.getGameList()
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        when {
            gameUiState.loading -> {
                // Display a loading indicator
                Box(
                    modifier = Modifier.fillMaxSize(),
                    contentAlignment = Alignment.Center
                ) {
                    CircularProgressIndicator(color = Color.Blue)
                }
            }

            gameUiState.error != null -> {
                // Display an error message
                Box(
                    modifier = Modifier.fillMaxSize(),
                    contentAlignment = Alignment.Center
                ) {
                    val errorMessage = gameUiState.error
                    Text(text = "Error: $errorMessage", color = Color.Red, fontSize = 18.sp)
                }
            }

            else -> {
                // Display the list of games
                GameList((gameUiState.games))
            }
        }
    }

}

@Composable
fun GameList(games: List<Game>) {
    LazyColumn {
        items(games) { game ->
            GameListItem(game = game)
        }
    }
}

@Composable
fun GameListItem(game: Game) {
    // Display a game item
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(8.dp)
            .shadow(4.dp, RoundedCornerShape(8.dp))
    ) {
        Column(
            modifier = Modifier.padding(8.dp)
        ) {

            Text(
                text = game.title,
                style = TextStyle(
                    fontSize = 18.sp,
                    fontWeight = FontWeight.Bold
                )
            )
            Text(
                text = game.review,
                modifier = Modifier.padding(4.dp)
            )
            Text(
                text = " Rating: ${game.rating}",
                color = Color.Gray,
                style = TextStyle(
                    fontWeight = FontWeight.Bold
                )
            )
        }
    }
}
```

Here, we’re crafting the UI for

> GameScreen

where we observe data from the ViewModel and manage loading states, errors, and display the games list.

**If you’re still using views it will look something like this:**

```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    private val viewModel:GameViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

      // Observe the state
       lifecycleScope.launchWhenStarted {
           viewModel.uiState.clolect { uiState ->
                    render(uiState)
              }

        // Launch getGamelist fun to update uiState.
        viewModel.getGameList()
    }

    override fun render(state: GameUiState) {
        // Update UI based on the state
        if (state.loading) {
            // Show loading indicator
        } else if (state.error != null) {
            // Show error message
        } else {
            // Display the list of games
            val gameList = state.games.map { it.title }
            // Update UI with gameList
        }
    }
}
```

**Here’s the result:**

![Image description](https://cdn.hashnode.com/res/hashnode/image/upload/v1751979721247/d440473e-a075-49d4-bca3-2024e52dd203.png align="left")

**Congratulations** 🎉🎆

![Image description](https://cdn.hashnode.com/res/hashnode/image/upload/v1751979723581/89b2ce88-4922-4bec-9e63-fac3dd5d1560.png align="left")

Remember, the adoption of MVVM is a journey, not a sprint. Start with small, manageable steps, and gradually integrate MVVM into your projects.

> “Discover the joy of exploring! Every adventure is a chance to find something wonderful, learn something new, and transform obstacles into stepping stones toward a future filled with unexpected victories and triumphs.”

“Stay curious, stay driven, and watch your skills soar. Happy coding!”