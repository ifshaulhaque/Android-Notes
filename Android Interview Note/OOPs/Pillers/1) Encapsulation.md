Encapsulation means **binding data + behavior together and restricting direct access to internal state**.

In simple words:

> “Control how your data is accessed and modified.”

In Android development (especially in MVVM and Clean Architecture), **encapsulation is one of the most important principles** for writing scalable and testable code.

## Why Encapsulation Matters in Android

Without encapsulation:
- Any class can change your data
- State becomes unpredictable
- Bugs become hard to trace
- Testing becomes difficult

With encapsulation:
- You control state mutation
- Logic is centralized
- UI cannot break business logic
- Your architecture becomes clean

## Access Modifiers in Kotlin (Core of Encapsulation)

Kotlin provides four visibility modifiers:

| Modifier           | Visible Where         |
| ------------------ | --------------------- |
| `public` (default) | Everywhere            |
| `private`          | Only inside the class |
| `protected`        | Class + subclasses    |
| `internal`         | Within same module    |

Example 1: Basic Encapsulation

``` kotlin
class BankAccount(private var balance: Double) {

    fun deposit(amount: Double) {
        if (amount > 0) {
            balance += amount
        }
    }

    fun withdraw(amount: Double) {
        if (amount <= balance) {
            balance -= amount
        }
    }

    fun getBalance(): Double {
        return balance
    }
}
```

Here:

- `balance` is private ❌ (cannot be changed directly)
- Controlled through methods ✅

If `balance` were public:

```kotlin
account.balance = -100000   // dangerous ❌
```

## Encapsulation with Properties in Kotlin

Kotlin gives powerful control over getters and setters.

---
### 🔹 Custom Setter Example

```kotlin
class User(name: String) {

    var name: String = name
        set(value) {
            if (value.isNotBlank()) {
                field = value
            }
        }
}
```

`field` → refers to the backing field.

```kotlin
user.name = "Amit"
println(user.name)   // Amit

user.name = ""
println(user.name)   // Still "Amit"
```

### 🔹 Read-Only Outside, Mutable Inside (Very Important in Android)

This pattern is heavily used in ViewModel.

```kotlin
class CounterViewModel {

    private var _count = 0
    val count: Int
        get() = _count

    fun increment() {
        _count++
    }
}
```

External classes:

```kotlin
viewModel.count      // ✅ can read
viewModel._count     // ❌ cannot access
```

---
## 🔥 Real Android Example — LiveData Encapsulation (Industry Standard)

In MVVM with Android Jetpack:

```kotlin
class MyViewModel : ViewModel() {

    private val _users = MutableLiveData<List<User>>()
    val users: LiveData<List<User>> = _users

    fun loadUsers() {
        _users.value = repository.getUsers()
    }
}
```

#### Why this is powerful:

|Property|Access|
|---|---|
|`_users`|Only ViewModel can modify|
|`users`|UI can only observe|

If UI could modify LiveData:
- UI could change state randomly ❌
- Business logic breaks ❌

This pattern protects your state.

---

## Encapsulation in Clean Architecture

In production apps, encapsulation is used in:
### 🔹 Repository Layer

```kotlin
interface UserRepository {
    suspend fun getUsers(): List<User>
}
```

Implementation details are hidden:

```kotlin
class UserRepositoryImpl(
    private val api: ApiService,
    private val dao: UserDao
) : UserRepository {

    override suspend fun getUsers(): List<User> {
        return api.fetchUsers()
    }
}
```

UI doesn't know:

- API details
- Database structure
- Network calls

Everything is encapsulated.

---
## Encapsulation with `private constructor`

Useful for Singleton patterns.

```kotlin
class Database private constructor() {

    companion object {
        val instance: Database by lazy {
            Database()
        }
    }
}
```

Prevents:

```kotlin
Database()   // ❌ Not allowed
```

## Encapsulation vs Data Exposure Mistake (Common Android Bug)

❌ BAD:

```kotlin
var userList = mutableListOf<User>()
```

Anyone can:

```kotlin
userList.clear()
```

✅ GOOD:

```kotlin
private val _userList = mutableListOf<User>()
val userList: List<User>
    get() = _userList
```

Now external classes cannot modify it.

---

## Benefits in Unit Testing

Encapsulation:
- Makes mocking easier
- Protects state
- Prevents unwanted mutation
- Reduces flaky tests

---

## Real Interview-Level Understanding

Encapsulation is NOT just:

> “Making variables private.”

It is:

> Designing your class so that its internal state can only be changed in a controlled and predictable way.

---

## ⚡ Advanced Concept: Immutability as Strong Encapsulation

Best practice in modern Android:
- Prefer `val` over `var`
- Use immutable lists
- Use data classes

Example:

```kotlin
data class UiState(
    val isLoading: Boolean,
    val data: List<User>
)
```

Instead of mutating fields:

```kotlin
state = state.copy(isLoading = true)
```

This is strong encapsulation + predictable state.