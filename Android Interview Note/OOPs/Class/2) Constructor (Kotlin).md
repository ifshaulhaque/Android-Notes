Kotlin has a more modern and concise approach.

# 🔹 Primary Constructor

Declared in class header.

```kotlin
class Student(val name: String, val age: Int)
```

This automatically:
- Creates properties
- Assigns values
- Generates constructor

---

## 🔍 What is inside primary constructor body?

Primary constructor cannot contain code directly.

To execute code during initialization, use `init` block:

```kotlin
class Student(val name: String, val age: Int) {

    init {
        println("Student Created")
    }
}
```

The `init` block works like a constructor body.

---

# 🔹 Secondary Constructor

Used when you need multiple constructors.

```kotlin
class Student {
    var name: String
    var age: Int

    constructor(name: String, age: Int) {
        this.name = name
        this.age = age
    }
}
```

---

# Default Constructor in Kotlin

If no constructor is defined:

```kotlin
class Student
```

Kotlin provides:

```kotlin
Student()
```

If properties have default values:

```kotlin
class Student(val name: String = "Unknown", val age: Int = 0)
```

You can call:

```kotlin
Student()
Student("Rahul")
Student("Rahul", 20)
```

---

# Private Constructor in Kotlin

```kotlin
class Singleton private constructor() {

    companion object {
        val instance = Singleton()
    }
}
```

Or easier:

```kotlin
object Singleton
```

The `object` keyword automatically:
- Makes constructor private
- Creates single instance
- Implements Singleton pattern

---

# 🔹 Copy Constructor in Kotlin

Kotlin does not use traditional copy constructor.

Instead, **data classes** provide `copy()` function automatically.

```kotlin
data class Student(val name: String, val age: Int)
```

Usage:

```kotlin
val s1 = Student("Rahul", 20)
val s2 = s1.copy(age = 25)
```

This is cleaner than Java.

---

# 🔹 Key Differences: Java vs Kotlin Constructors

|Feature|Java|Kotlin|
|---|---|---|
|Default constructor|Auto provided|Auto provided|
|Copy constructor|Manual|`copy()` in data class|
|Multiple constructors|Yes|Yes (secondary)|
|Constructor body|Inside constructor|`init` block|
|Singleton|Private constructor|`object` keyword|

---

# 🔥 Interview-Oriented Points

1. Constructor is not inherited.
2. Constructor cannot be abstract.
3. Constructor can be overloaded.
4. If you define any constructor, Java will NOT provide default constructor.
5. Private constructor prevents object creation.
6. In Kotlin, primary constructor is part of class header.
7. Data class automatically provides copy method.