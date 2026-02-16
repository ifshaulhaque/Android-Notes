A **constructor** is a special member of a class used to initialize objects.
- It runs **when an object is created**
- It initializes **instance variables**
- It has the **same name as the class (Java)**
- It does **not have a return type**

# Types of Constructors in Java

## 1️⃣ Default Constructor (Implicit Constructor)

If you do **not write any constructor**, Java automatically provides one.

```java
class Student {
    String name;
    int age;
}
```

Internally Java provides:

```java
Student() {
    super();   // calls Object class constructor
}
```

### 🔍 What is inside the default constructor body?

The default constructor:
- Calls `super()` (parent class constructor)
- Initializes variables with default values:

|Type|Default Value|
|---|---|
|int|0|
|boolean|false|
|Object|null|
|double|0.0|

Now your question:

> What if there is no parent of the class?

# 🔹 In Java, Every Class Has a Parent

Even if you **don’t explicitly extend any class**, Java automatically makes your class extend:

👉 java.lang.Object

So this:

```java
class Student {
}
```

Is actually treated by the compiler as:

```java
class Student extends Object {
}
```

# 🔹 So What Happens Internally?

When you write:

```java
class Student {
    Student() {
    }
}
```

The compiler automatically inserts:

```java
class Student extends Object {
    Student() {
        super();   // calls Object()
    }
}
```

So even if you think there is **no parent**, the real parent is:

```java
java.lang.Object
```

# 🔹 Why Does Java Do This?

Because `Object` is the **root class of Java hierarchy**.

All classes inherit common methods from `Object`, like:
- `toString()`
- `equals()`
- `hashCode()`
- `getClass()`
- `wait()`
- `notify()`

That’s why every object in Java can use these methods.

---

# 🔹 Important Rule About `super()`

1. `super()` is automatically inserted as the first line in every constructor.
2. If the parent class does not have a no-argument constructor, you must explicitly call a valid parent constructor.

Example:

```java
class Parent {
    Parent(String name) {}
}

class Child extends Parent {
    Child() {
        super(); // ❌ ERROR
    }
}
```

This will give compile-time error because `Parent` does not have a no-arg constructor.

Correct version:

```java
class Child extends Parent {
    Child() {
        super("Rahul"); // ✅ correct
    }
}
```

# 🔥 Short Version for Interviews

👉 Even if a class does not explicitly extend anything, it implicitly extends `java.lang.Object`.

👉 Therefore, `super()` always calls the constructor of `Object` class.

👉 In Java, there is no such thing as a class without a parent.

---

## 2️⃣ No-Argument Constructor

You explicitly create it:

```java
Student() {
    name = "Unknown";
    age = 0;
}
```

---

## 3️⃣ Parameterized Constructor

Used to initialize values during object creation.

```java
Student(String name, int age) {
    this.name = name;
    this.age = age;
}
```

---

## 4️⃣ Copy Constructor (User Defined in Java)

Java does NOT provide built-in copy constructors like C++.

A **copy constructor** is a constructor that creates a new object by copying values from another object of the same class.

```java
class Student {
    String name;
    int age;

    // Copy Constructor
    Student(Student other) {
        this.name = other.name;
        this.age = other.age;
    }
}
```

Usage:

```java
Student s1 = new Student("Rahul", 20);
Student s2 = new Student(s1);
```

# 🔷 Why Do We Create Copy Constructor Inside the Class?

### ❓ Why not copy outside the class?

Example of copying outside:

```java
Student s2 = new Student();
s2.name = s1.name;
s2.age = s1.age;
```

### 🚫 Problem:

If variables are **private**, they cannot be accessed outside the class.

```java
class Student {
    private String name;
    private int age;
}
```

Now this will cause ERROR:

```java
s2.name = s1.name;   // ❌ not accessible
```

### ✅ That’s why copy constructor is inside the class:

- It can access **private variables**
- Maintains **encapsulation**
- Centralized object copying logic
- Cleaner and safer

---

# 🔷 Primitive vs Non-Primitive Data Types

Understanding this is very important for shallow/deep copy.

---

## 🔹 Primitive Data Types (Value Stored Directly)

These store actual values.

|Type|Example|
|---|---|
|byte|1|
|short|2|
|int|10|
|long|100L|
|float|10.5f|
|double|20.5|
|char|'A'|
|boolean|true|

👉 Primitive types are copied by value automatically.

---

## 🔹 Non-Primitive Data Types (Reference Types)

These store **memory address**, not actual object.

Examples:

- String
- Arrays
- Classes
- Objects
- Interface types
- Enums
- Collections (List, Map, Set)
- Wrapper classes (Integer, Double, etc.)

👉 When copied normally → only reference is copied (shallow copy).

---
# 🔷 Shallow Copy (Important Concept)

The copy constructor shown earlier creates a **shallow copy**.

## 🔍 What is Shallow Copy?

- Copies **primitive values directly**
- Copies **reference values (memory addresses)** for objects
- Does NOT create new objects for reference types

---

## 🔹 Example of Shallow Copy

```java
class Address {
    String city;

    Address(String city) {
        this.city = city;
    }
}

class Student {
    String name;
    Address address;

    Student(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    // Shallow Copy Constructor
    Student(Student other) {
        this.name = other.name;
        this.address = other.address;  // Reference copied
    }
}
```

Now:

```java
Address addr = new Address("Delhi");
Student s1 = new Student("Rahul", addr);
Student s2 = new Student(s1);

s2.address.city = "Mumbai";
```

### Result:

Both `s1` and `s2` will show city = "Mumbai"

Because:

```java
s1.address  ------\
                    ---> Same Address Object
s2.address  ------/
```

This is Shallow Copy.

---
# ✅ Deep Copy Example

```java
class Address {
    String city;

    Address(String city) {
        this.city = city;
    }

    // Deep copy constructor
    Address(Address other) {
        this.city = other.city;
    }
}

class Student {
    String name;
    Address address;

    Student(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    // Deep Copy Constructor
    Student(Student other) {
        this.name = other.name;
        this.address = new Address(other.address); // new object created
    }
}
```

```java
Address addr = new Address("Delhi");
Student s1 = new Student("Rahul", addr);
Student s2 = new Student(s1);

s2.address.city = "Mumbai";
```

### Result:

- s1.address.city = "Delhi"
- s2.address.city = "Mumbai"

Because:

```java
s1.address  ---> Address Object 1
s2.address  ---> Address Object 2
```

completely separate memory objects.

---

# 🔥 Interview Summary Points

1. Copy constructor is used to create object from another object.
2. We define it inside class because:
    - To access private variables
    - Maintain encapsulation
3. Shallow copy:
    - Copies primitive values
    - Copies references for objects
4. Deep copy:
    - Creates new objects for reference fields
    - Fully independent object
5. Primitive types store value directly.
6. Non-primitive types store memory reference.

---

# 5️⃣ Private Constructor

```java
class Singleton {
    private Singleton() {
    }
}
```

### 🔍 What happens if constructor is private?

- Object cannot be created outside the class
- Used in:
    - Singleton Design Pattern
    - Utility classes
    - Static-only classes

Example: Singleton pattern

```java
class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```