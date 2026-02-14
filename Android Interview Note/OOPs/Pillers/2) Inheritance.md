Inheritance allows a class (child/subclass) to acquire properties and functions of another class (parent/superclass).

It represents an **“is-a” relationship**.

Example:
- Car **is a** Vehicle
- MainActivity **is an** Activity

---

## Important Kotlin Rule

👉 **Classes are `final` by default in Kotlin**

You MUST mark a class `open` to allow inheritance.

```kotlin
open class Vehicle
```

This design prevents accidental inheritance (which was common in Java).

---

## Basic Inheritance Example

```kotlin
open class Vehicle {

    open fun start() {
        println("Vehicle started")
    }
}

class Car : Vehicle() {

    override fun start() {
        println("Car started")
    }
}
```

Usage:

```kotlin
val car = Car()
car.start()  // Car started
```

---
## Constructor Inheritance

If parent has constructor:

```kotlin
open class Vehicle(val brand: String)
```

Child must call it:

```kotlin
class Car(brand: String, val model: String) : Vehicle(brand)
```

Usage:

```kotlin
val car = Car("Tesla", "Model S")
println(car.brand)  // Tesla
println(car.model)  // Model S
```

---
## Method Overriding Rules

Parent function must be `open`:

```kotlin
open fun start()
```

Child must use `override`:

```kotlin
override fun start()
```

You can prevent further overriding using:

```kotlin
final override fun start()
```

---
## Real Android Example

In Android, classes extend base framework classes from Android SDK.

Example:

```kotlin
class MainActivity : AppCompatActivity()
```

Here:

MainActivity **inherits**:
- Lifecycle methods
- Context
- Fragment manager
- View system

You override lifecycle methods:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
}
```

---
## Calling Super

You can call parent implementation using `super`.

```kotlin
override fun start() {
    super.start()
    println("Car additional logic")
}
```

This is common in Android lifecycle:

```kotlin
override fun onResume() {
    super.onResume()
    logScreenView()
}
```

---
## Abstract Classes (Inheritance + Abstraction)

Abstract class cannot be instantiated.

```kotlin
abstract class Animal {
    abstract fun makeSound()
}
```

Child MUST implement:

```kotlin
class Dog : Animal() {
    override fun makeSound() {
        println("Bark")
    }
}
```

Used in Android:
- BaseViewModel
- BaseFragment
- BaseActivity

---

## Practical Android Use Case – BaseActivity

In large apps (like dashboards), we often create:

```kotlin
abstract class BaseActivity : AppCompatActivity() {

    fun showLoading() {
        // common loading logic
    }

    abstract fun observeViewModel()
}
```

Then:

```kotlin
class HomeActivity : BaseActivity() {

    override fun observeViewModel() {
        // specific logic
    }
}
```

Now:
- Common behavior centralized
- Specific behavior customizable

---

## Multi-Level Inheritance

```kotlin
open class Machine
open class Vehicle : Machine()
class Car : Vehicle()
```

Car inherits from both Machine and Vehicle.

But ⚠️ Deep inheritance chains are dangerous in large Android apps.

---

## Overriding Properties

Properties can also be overridden:

```kotlin
open class Shape {
    open val sides: Int = 0
}

class Triangle : Shape() {
    override val sides: Int = 3
}
```

---
## Important: Inheritance vs Composition

In modern Android development (MVVM, Clean Architecture), **composition is preferred over inheritance**.

Instead of:

```kotlin
class Car : Engine()
```

Prefer:

```kotlin
class Car(private val engine: Engine)
```

Why?

Inheritance:
- Tight coupling
- Hard to modify
- Breaks easily

Composition:
- Flexible
- Testable
- Replaceable dependencies

---

## When Should You Use Inheritance in Android?

✅ Creating base UI classes  
✅ Shared lifecycle behavior  
✅ Template method pattern  
✅ Framework extension

Avoid for:  
❌ Business logic reuse  
❌ Feature reuse  
❌ Complex domain models

---

## Common Inheritance Mistakes

### ❌ Not calling super

Breaks lifecycle.

### ❌ Making everything open

Breaks encapsulation.

### ❌ Deep class hierarchies

Hard to debug.

---

## Interview-Level Explanation

Inheritance is:

> A mechanism that allows a subclass to reuse, extend, and customize behavior of a superclass while preserving polymorphic behavior.

In Android, it is primarily used to:
- Extend framework classes
- Provide base UI components
- Implement template patterns