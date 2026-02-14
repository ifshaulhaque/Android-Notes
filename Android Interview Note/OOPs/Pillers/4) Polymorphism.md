
# What is Polymorphism?

Polymorphism means:

> **“One interface, many implementations.”**

The word comes from:
- **Poly** → many
- **Morph** → forms

It allows a single reference type to represent different object types.

---

## Why Polymorphism is Important in Android?

Polymorphism allows:

✅ Flexible architecture  
✅ Clean MVVM  
✅ Easy testing  
✅ Replaceable components  
✅ Scalable feature design

It’s heavily used in:
- Repository pattern
- RecyclerView adapters
- Navigation systems
- State handling
- Dependency Injection

---

## Types of Polymorphism in Kotlin

There are **two main types**:
 - Compile-time Polymorphism (Static)  
 - Runtime Polymorphism (Dynamic)

---

## Compile-Time Polymorphism (Method Overloading)

Same function name, different parameters.

```kotlin
class Calculator {

    fun add(a: Int, b: Int): Int {
        return a + b
    }

    fun add(a: Double, b: Double): Double {
        return a + b
    }
}
```

Compiler decides which function to call.

---

## Runtime Polymorphism (Method Overriding)

This is the **most important type** in Android.

Parent reference → Child object.

---

### Basic Example

```kotlin
open class Animal {
    open fun makeSound() {
        println("Animal sound")
    }
}

class Dog : Animal() {
    override fun makeSound() {
        println("Bark")
    }
}
```

Usage:

```kotlin
val animal: Animal = Dog()
animal.makeSound()   // Bark
```

Even though type is `Animal`,  
actual behavior depends on `Dog`.

This is runtime polymorphism.

---

# 🔥 Real Android Example (Repository Pattern)

Using abstraction from Android Jetpack architecture.

---

### Step 1: Interface

```kotlin
interface UserRepository {
    suspend fun getUsers(): List<User>
}
```

---

### Step 2: Multiple Implementations

```kotlin
class RemoteUserRepository : UserRepository {
    override suspend fun getUsers(): List<User> {
        return api.fetchUsers()
    }
}

class LocalUserRepository : UserRepository {
    override suspend fun getUsers(): List<User> {
        return database.getUsers()
    }
}
```

---

### Step 3: Polymorphic Usage

```kotlin
class UserViewModel(
    private val repository: UserRepository
)
```

Now ViewModel doesn’t care:
- Remote?
- Local?
- Fake for testing?

It just calls:

```kotlin
repository.getUsers()
```


This is pure polymorphism.

---

## Polymorphism with Interfaces

```kotlin
interface Engine {
    fun start()
}

class PetrolEngine : Engine {
    override fun start() {
        println("Petrol engine started")
    }
}

class ElectricEngine : Engine {
    override fun start() {
        println("Electric engine started")
    }
}
```

Usage:

```kotlin
fun startCar(engine: Engine) {
    engine.start()
}
```

Now:

```kotlin
startCar(PetrolEngine())
startCar(ElectricEngine())
```

Same function → different behavior.

---

## Polymorphism in RecyclerView (Very Important)

Different view types:

```kotlin
abstract class DashboardItem {
    abstract fun getType(): Int
}

class HeaderItem : DashboardItem() {
    override fun getType() = 0
}

class ContentItem : DashboardItem() {
    override fun getType() = 1
}
```

Adapter works on:

```kotlin
List<DashboardItem>
```

But actual items are:
- HeaderItem
- ContentItem

This is polymorphism.

---

## Polymorphism + Sealed Classes (Modern Kotlin Way)

Better approach in many cases:

```kotlin
sealed class UiState {

    object Loading : UiState()

    data class Success(val data: List<User>) : UiState()

    data class Error(val message: String) : UiState()
}
```

Usage:

```kotlin
when (state) {
    is UiState.Loading -> showLoading()
    is UiState.Success -> showData(state.data)
    is UiState.Error -> showError(state.message)
}
```

Here:
- One parent type
- Multiple forms
- Controlled hierarchy

This is structured polymorphism.

---

## Polymorphism vs Inheritance

Inheritance → mechanism  
Polymorphism → behavior

Inheritance enables polymorphism.

But polymorphism can also be achieved via interfaces.

---

## Polymorphism in Dependency Injection

When using:
- Hilt
- Dagger
- Koin

You bind implementation to abstraction.

Example:

```kotlin
@Binds
abstract fun bindRepository(
    impl: RemoteUserRepository
): UserRepository
```

Now:
- ViewModel receives UserRepository
- Implementation can change without affecting code

That is polymorphism at architecture level.

---

## Common Mistakes

❌ Using `if-else` instead of polymorphism  
❌ Tight coupling to concrete classes  
❌ Overusing inheritance

Instead of:

```kotlin
if (type == "PETROL") { ... }
```

Use:

```kotlin
engine.start()
```

Cleaner. Scalable.

---

## 🔥 Interview-Level Definition

Polymorphism is:

> The ability of a parent reference to hold different child objects and execute the overridden behavior at runtime.

In Android, polymorphism enables:
- Clean architecture
- Replaceable modules
- Testable code
- Scalable UI systems

---

## 🔥 Very Important Concept

Polymorphism + Abstraction + Dependency Injection  
= Clean Architecture