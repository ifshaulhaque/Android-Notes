## What is Abstraction?

Abstraction means:

> Showing only essential details and hiding complex implementation.

In simple words:  
You expose **what to do**, but hide **how it is done**.

---

## Real-World Example

When you drive a car:
- You use steering, brake, accelerator.
- You don’t know engine combustion logic.

That’s abstraction.

---

## Abstraction in Kotlin

In Kotlin, abstraction is achieved using:
1. **Abstract classes**
2. **Interfaces**

---

## Abstract Class

An abstract class:
- Cannot be instantiated
- Can contain abstract and concrete functions
- Forces child classes to implement specific behavior

---

### Example:

```kotlin
abstract class PaymentProcessor {

    abstract fun processPayment(amount: Double)

    fun validateAmount(amount: Double): Boolean {
        return amount > 0
    }
}
```

Child class:

```kotlin
class CreditCardPayment : PaymentProcessor() {

    override fun processPayment(amount: Double) {
        println("Processing credit card payment of $amount")
    }
}
```

Usage:

```kotlin
val payment: PaymentProcessor = CreditCardPayment()
payment.processPayment(500.0)
```

Here:
- Caller doesn’t know how payment works internally.
- Only knows `processPayment()`.

That’s abstraction + polymorphism.

---

## Interface (More Powerful in Kotlin)

Interfaces define behavior contract.

```kotlin
interface Engine {
    fun start()
}
```

Implementation:

```kotlin
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

Now:

```kotlin
val engine: Engine = ElectricEngine()
engine.start()
```

We don’t care which engine.  
We only care that it can `start()`.

---

# 🔥 Abstraction in Android (Real Use Cases)

Modern Android apps (MVVM + Clean Architecture) heavily rely on abstraction.

---

## Repository Pattern (Core Abstraction)

This is used in Android Jetpack architecture.

### Step 1: Define abstraction

```kotlin
interface UserRepository {
    suspend fun getUsers(): List<User>
}
```

### Step 2: Implementation

```kotlin
class UserRepositoryImpl(
    private val apiService: ApiService
) : UserRepository {

    override suspend fun getUsers(): List<User> {
        return apiService.fetchUsers()
    }
}
```

### Step 3: ViewModel uses abstraction

```kotlin
class UserViewModel(
    private val repository: UserRepository
) : ViewModel() {

    suspend fun loadUsers() {
        repository.getUsers()
    }
}
```

ViewModel:
- Does NOT know about API
- Does NOT know about database
- Only knows contract

That is powerful abstraction.

---

## Why Abstraction is Important in Android

Without abstraction:

❌ ViewModel directly calls Retrofit  
❌ UI layer knows about database  
❌ Hard to test  
❌ Tight coupling

With abstraction:

✅ Easy to mock in unit tests  
✅ Replace API with local DB easily  
✅ Cleaner architecture  
✅ Better scalability

---

## Abstraction vs Encapsulation

People confuse these.

Encapsulation → hides internal state  
Abstraction → hides internal complexity

Example:

```kotlin
interface Navigator {
    fun navigateTo(screen: Screen)
}
```

This hides navigation implementation.  
That’s abstraction.

But if Navigator internally manages a private stack,  
that’s encapsulation.

---

## Abstract Class vs Interface (Important for Interviews)

|Feature|Abstract Class|Interface|
|---|---|---|
|Multiple inheritance|❌|✅|
|Can store state|✅|❌ (no backing fields)|
|Constructors|✅|❌|
|Default methods|✅|✅|

Use abstract class when:
- You need shared state
- You need base behavior

Use interface when:
- You need contract only
- You want multiple implementations
- You want loose coupling

---

## Advanced: Abstraction with Dependency Injection

When using:
- Hilt
- Dagger
- Koin

We inject interfaces instead of concrete classes.

Example:

```kotlin
@Binds
abstract fun bindUserRepository(
    impl: UserRepositoryImpl
): UserRepository
```

Now:
- Code depends on abstraction
- Implementation can change anytime

This follows SOLID principle (Dependency Inversion).

---

## Abstraction in Testing

You can easily mock:

```kotlin
class FakeUserRepository : UserRepository {
    override suspend fun getUsers(): List<User> {
        return listOf(User("Test"))
    }
}
```

Testing becomes simple.

---

## 🔥 Interview-Level Definition

Abstraction is:

> A design principle that focuses on exposing only the necessary behavior while hiding internal implementation details using abstract classes and interfaces.

In Android:
- Repository
- UseCase
- Navigator
- DataSource
- Manager classes

All are abstractions.

---

## 🔥 Most Important Production Advice

Use abstraction when:

✅ You expect multiple implementations  
✅ You want testability  
✅ You want replaceable modules  
✅ You want clean architecture

Avoid unnecessary abstraction when:  
❌ Only one implementation ever exists  
❌ Over-engineering small apps