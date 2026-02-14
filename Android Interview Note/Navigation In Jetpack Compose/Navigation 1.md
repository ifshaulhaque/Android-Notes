### Dependency

```kotlin
val nav_version = "2.9.6"  
implementation("androidx.navigation:navigation-compose:$nav_version")
```

### Key Components of Nav-Compose

Navigation in Compose is built around three core concepts that work together to manage the user journey: the Controller, the Host, and the Graph.

##### The NavController

The `NavController` is the **central API** for the Navigation Component. It is stateful, meaning it keeps track of the back stack of composables (your screens) and manages the state of each one.

- **Creation:** You must create and remember the `NavController` at a high level in your Composable hierarchy (usually in your `MainActivity` or the main screen composable) so that all screens that need it can access it.
- **Action:** It provides the methods you use to perform navigation:
    - `navController.navigate("route_name")`
    - `navController.popBackStack()`

##### The NavHost

The `NavHost` is the **container** for your composable screens. It acts as a placeholder that automatically swaps in and out the Composable UI for the current destination as you navigate.
- **Association:** Every `NavController` must be associated with a single `NavHost`.
- **Required Parameters:** It requires the `navController` and a `startDestination`.

##### The NavGraph (Routes and Destinations)

The navigation graph is the blueprint of your app's flow. It is defined within the `NavHost` lambda and maps out all possible destinations a user can navigate between.
- **Defining Destinations:** You use the `composable()` extension function to add each screen to the graph. The first argument is the screen's unique **route** (a `String`).