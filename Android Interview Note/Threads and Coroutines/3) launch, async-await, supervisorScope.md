### `launch`

- Used to **start a coroutine that doesn’t return a result**.
- It’s like saying: _“Go run this task in the background, I don’t need a return value.”_
- Example use case: updating UI, saving data in DB, logging.

```kotlin
GlobalScope.launch {     
	// runs in background     
	val data = fetchFromNetwork()     
	println("Got data: $data") 
}
```

Notice: no return value. It’s like **fire-and-forget**.

### `async / await`

- `async` starts a coroutine **that returns a `Deferred<T>`** (think of it like a "promise" to give you the value later).
- You call `.await()` on it to get the result **without blocking**.

```kotlin
GlobalScope.launch {
    val deferred = async {
        fetchFromNetwork() // suspending function
    }

    val result = deferred.await()
    println("Got: $result")
}
```

``` kotlin
┌─────────────────────────────────────────────────────────────────────────────┐
│                         GlobalScope.launch (Coroutine)                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Time  │ Main Execution Flow                   │ Concurrent Execution       │
│────────┼───────────────────────────────────────┼────────────────────────────┤
│   t0   │ ┌─ Start coroutine                    │                            │
│        │ │                                     │                            │
│   t1   │ │ val deferred = async {              │                            │
│        │ │     fetchFromNetwork() // launches  │                            │
│        │ │ }                                   │                            │
│        │ │                                     │ ┌─ async coroutine starts  │
│        │ │                                     │ │ fetchFromNetwork() begins│
│        │ │                                     │ │ (suspends if needed)     │
│        │ │                                     │ │                          │
│   t2   │ │ val result = deferred.await()       │ │                          │
│        │ │ ┌─ await() suspends current         │ │                          │
│        │ │ │ coroutine if result not ready     │ │                          │
│        │ │ │                                   │ │                          │
│        │ │ │ Coroutine SUSPENDED here          │ │ Network request ongoing  │
│        │ │ │                                   │ │ (I/O operation)          │
│        │ │ │                                   │ │                          │
│        │ │ │ Thread is RELEASED for other work │ │                          │
│        │ │ │                                   │ │                          │
│   t3   │ │ │                                   │ │ fetchFromNetwork()       │
│        │ │ │                                   │ │ completes                │
│        │ │ │                                   │ │                          │
│   t4   │ │ │ await() RESUMES                   │ │ async coroutine completes│
│        │ │ │ result becomes available          │ │                          │
│        │ │ └─                                  │ │                          │
│        │ │                                     │ │                          │
│   t5   │ │ println("Got: $result")             │                            │
│        │ │                                     │                            │
│   t6   │ └─ Coroutine completes                │                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Here’s the difference in feel:
- `launch` → “Go do this, I don’t care about the return.”
- `async/await` → “Go do this, and tell me the result when it’s ready.”

By default, coroutines don’t magically “know” where to run; you have to tell them (or they fall back to defaults).
### Threads & Dispatchers

- **`Dispatchers.Main`**  
    → Runs on the **Android Main/UI thread**.  
    → For UI updates, LiveData, Compose recomposition, etc.

- **`Dispatchers.IO`**  
    → Uses a pool of threads optimized for **blocking I/O** (network calls, database, file operations).

- **`Dispatchers.Default`**  
    → Uses a pool of threads optimized for **CPU-intensive work** (sorting, parsing JSON, big calculations).

- **`Dispatchers.Unconfined`**  
    → Starts on the current thread, then continues on the thread used by the suspending function. (Rarely used in Android).

```kotlin
GlobalScope.launch {
    val deferred = async {
        fetchFromNetwork()
    }

    val result = deferred.await()
    println("Got: $result")
}
```

- `GlobalScope.launch { ... }`  
    → Since you didn’t specify a dispatcher, it defaults to **`Dispatchers.Default`**.  
    → So this code will **not** run on the main thread; it’ll run on a background thread from the Default pool.

- The `async { ... }` inherits the context from its parent (`launch`).  
    → So `fetchFromNetwork()` also runs on the same `Default` thread pool.

You just give it its own dispatcher:

```kotlin
GlobalScope.launch(Dispatchers.Main) {
    // Parent coroutine on Main
    println("Parent on: ${Thread.currentThread().name}")

    val deferred = async(Dispatchers.IO) {
        println("Child async on: ${Thread.currentThread().name}")
        fetchFromNetwork() // heavy work on IO pool
    }

    val result = deferred.await() // suspends Main thread coroutine, but doesn't block
    println("Got: $result on ${Thread.currentThread().name}")
}
```