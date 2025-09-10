
### 1. **Nested class (default)**

- It **does not** hold a reference to the outer class.
- Think of it as a **static class in Java**.
- It can’t access outer class members directly.

```kotlin
class Outer {
    private val outerValue = "I'm outer"

    class Nested {
        fun printMessage() = println("Hello from Nested")
    }
}

fun main() {
    val nested = Outer.Nested()
    nested.printMessage()  // ✅ works
    // nested can't see outerValue
}
```

### 2. **Inner class (`inner` keyword)**

- It **does** hold a reference to the outer class.
- It can access outer class members, even private ones.
- Similar to a **non-static inner class in Java**.

```kotlin
class Outer {
    private val outerValue = "I'm outer"

    inner class Inner {
        fun printMessage() = println("Accessing outer: $outerValue")
    }
}

fun main() {
    val outer = Outer()
    val inner = outer.Inner()
    inner.printMessage()  // ✅ Accessing outer: I'm outer
}
```

🔑 **Key takeaway:**

- Use **nested** when the inner class doesn’t need anything from the outer.
- Use **inner** when the inner class needs to work with outer class properties or functions.