
### **1. Enum classes**

- Used when you have a **fixed set of constants** (like states, options, directions).
- Each enum constant is a **singleton object**.
- Good for things like days of the week, traffic light colors, etc.
- All enum values are instances of the **same type**.

```kotlin
enum class Direction {
    NORTH, SOUTH, EAST, WEST
}

fun move(dir: Direction) {
    when (dir) {
        Direction.NORTH -> println("Moving up")
        Direction.SOUTH -> println("Moving down")
        Direction.EAST  -> println("Moving right")
        Direction.WEST  -> println("Moving left")
    }
}
```

### **2. Sealed classes**

- Used when you want a **restricted class hierarchy**.
- A sealed class can have **different subclasses** (each with their own properties).
- Useful for representing **different variants of a concept**.
- Helps model **state machines** or **results** (like `Success`, `Error`, `Loading`).

```kotlin
sealed class Result {
    data class Success(val data: String) : Result()
    data class Error(val exception: Exception) : Result()
    object Loading : Result()
}

fun handle(result: Result) {
    when (result) {
        is Result.Success -> println("Got data: ${result.data}")
        is Result.Error   -> println("Error: ${result.exception}")
        Result.Loading    -> println("Loading...")
    }
}
```


### **Key differences**

| Feature        | Enum                      | Sealed class                          |
| -------------- | ------------------------- | ------------------------------------- |
| Variants       | All of **same type**      | Can have **different data and types** |
| Extensibility  | Only fixed constants      | Fixed set of subclasses               |
| State modeling | Simple, same-shaped data  | Complex, different-shaped data        |
| Use case       | Directions, modes, colors | Results, events, UI states            |

👉 A rule of thumb:

- If every option is just a **name/constant**, go for **enum**.
- If options need to **carry different data**, go for **sealed**.