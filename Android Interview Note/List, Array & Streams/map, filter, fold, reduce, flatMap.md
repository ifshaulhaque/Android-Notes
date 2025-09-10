### 1. **map**
- Transforms each element in a collection into another form.

```kotlin
val numbers = listOf(1, 2, 3)
val squares = numbers.map { it * it }  
println(squares) // [1, 4, 9]
```

👉 In Android: formatting a list of API response DTOs into UI models.

### 2. **filter**
- Keeps only elements that match a condition.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val evens = numbers.filter { it % 2 == 0 }
println(evens) // [2, 4]
```

👉 In Android: filtering a list of users who are online.


### 3. **fold**
- Accumulates a value starting from an **initial seed**.

```kotlin
val numbers = listOf(1, 2, 3, 4)
val sum = numbers.fold(0) { acc, value -> acc + value }
println(sum) // 10
```

👉 In Android: building a string from multiple lines.


### 4. **reduce**
- Similar to fold, but doesn’t take an initial value (starts with the first element).

```kotlin
val numbers = listOf(1, 2, 3, 4)
val sum = numbers.reduce { acc, value -> acc + value }
println(sum) // 10
```

👉 In Android: aggregating values like total price of cart items.  
⚠️ Use `fold` if the list might be empty (because `reduce` crashes on empty lists).


### 5. **flatMap**
- Applies a function that returns a **list**, then flattens the result.

```kotlin
val words = listOf("Hi", "Kotlin")
val chars = words.flatMap { it.toList() }
println(chars) // [H, i, K, o, t, l, i, n]
```

👉 In Android: turning a list of categories → into a flat list of all items.


🔑 **Quick mental model**:
- `map` → transform
- `filter` → keep some
- `fold/reduce` → collapse into one
- `flatMap` → map + flatten