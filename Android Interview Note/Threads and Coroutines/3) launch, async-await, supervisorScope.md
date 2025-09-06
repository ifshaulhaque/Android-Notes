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

---
### Ways to Catch Exceptions in Coroutines

```kotlin
GlobalScope.launch {
    try {
        val data = fetchFromNetwork()
        println("Got: $data")
    } catch (e: Exception) {
        println("Caught exception: ${e.message}")
    }
}
```

```kotlin
val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught with handler: ${exception.message}")
}

GlobalScope.launch(handler) {
    throw RuntimeException("Boom!")
}
```

### Coroutine Exception Handler + Dispatchers

```kotlin
val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught exception: ${exception.message}")
}

GlobalScope.launch(Dispatchers.IO + handler) {
    println("Running on: ${Thread.currentThread().name}")
    throw RuntimeException("Boom!") // will be caught by handler
}
```

---
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

If we did this with **plain threads**, it would look more like:

```kotlin
Thread {
    // Start another background thread for fetch
    val worker = Thread {
        result = fetchFromNetwork() // blocking call
    }

    worker.start()
    worker.join() // wait for worker to finish (blocks!)

    println("Got: $result")
}.start()
```

Differences:
- We must manage threads manually.
- `join()` actually **blocks** the current thread, unlike `await()` which just suspends.
- Threads are heavy (MBs of memory, OS-managed). Coroutines are lightweight.

```kotlin
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Main Thread Execution                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Time  │ Main Thread                         │ Worker Thread                │
│────────┼─────────────────────────────────────┼──────────────────────────────┤
│   t0   │ ┌─ Thread { ... }.start()           │                              │
│        │ │                                   │                              │
│   t1   │ │ val worker = Thread {             │                              │
│        │ │     result = fetchFromNetwork()   │                              │
│        │ │ }                                 │                              │
│        │ │                                   │                              │
│   t2   │ │ worker.start()                    │ ┌─ Worker thread starts      │
│        │ │                                   │ │ fetchFromNetwork() begins  │
│        │ │                                   │ │ (BLOCKING I/O call)        │
│        │ │                                   │ │                            │
│   t3   │ │ worker.join()                     │ │                            │
│        │ │ ┌─ MAIN THREAD BLOCKED ⚡         │ │                            │
│        │ │ │ Thread frozen, cannot do        │ │                            │
│        │ │ │ any other work                  │ │                            │
│        │ │ │                                 │ │                            │
│        │ │ │ CPU cycles wasted waiting       │ │ Network request ongoing    │
│        │ │ │                                 │ │ (I/O operation)            │
│        │ │ │                                 │ │                            │
│        │ │ │                                 │ │                            │
│   t4   │ │ │                                 │ │ fetchFromNetwork()         │
│        │ │ │                                 │ │ completes                  │
│        │ │ │                                 │ │                            │
│        │ │ │                                 │ │ Worker thread finishes     │
│        │ │ │                                 │ │                            │
│        │ │ │ worker.join() returns           │ │                            │
│        │ │ └─ Main thread RESUMES            │ │                            │
│        │ │                                   │                              │
│   t5   │ │ println("Got: $result")           │                              │
│        │ │                                   │                              │
│   t6   │ └─ Outer thread completes           │                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### What “blocking” means in threads

When a **Thread** calls `.join()` or does a blocking call (like `sleep`, `network.read()`):
- The **thread itself is stuck** until the work finishes.
- While stuck, the thread can’t do anything else — it’s literally frozen, waiting.
- Since threads are expensive (each takes memory & OS resources), blocking is wasteful.

### What “suspending” means in coroutines

When a coroutine calls `await()`:
- The coroutine is **suspended**, but the **thread is released** back to the pool.
- That thread can now go handle other coroutines or tasks.
- Later, when the result is ready, the coroutine is resumed (possibly on the same or another thread).

---

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

---

If you want **two results back** (say `user` and `orders`), then `async` is the tool — because both can run at the same time, and you can `await` their results later. Example:

```kotlin
coroutineScope {
    val userDeferred = async { fetchUser() }
    val ordersDeferred = async { fetchOrders() }

    val user = userDeferred.await()
    val orders = ordersDeferred.await()
    println("Got $user and $orders")
}
```

Both `fetchUser()` and `fetchOrders()` start immediately and run in parallel. Then you `await` to combine them.

👉 So:
- `launch` = fire and forget
- `async/await` = run tasks in parallel **and** get results

Let’s step into **failure handling** next:

🔹 `coroutineScope` vs `supervisorScope`
- In a **normal `coroutineScope`**, if one child coroutine fails → **all siblings are cancelled**.
- In a **`supervisorScope`**, one child failing does **NOT cancel the others**.

That’s what you were thinking about: keeping multiple tasks alive even if one fails.

👉 Imagine this case:
- `fetchUser()` succeeds
- `fetchOrders()` fails

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         Scenario 1: fetchUser() completes first                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ Enter coroutineScope     │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing             │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ COMPLETES ✓      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ user value obtained    │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ COMPLETES ✓           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ orders value obtained  │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("Got $user and $orders") │             │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ coroutineScope completes │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         Scenario 2: fetchOrders() completes first                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ Enter coroutineScope     │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing             │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │                  │ │ COMPLETES ✓           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ (still waiting for user)│ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t5   │ │ │                         │ │ COMPLETES ✓      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ user value obtained    │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t6   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Immediate              │ │                  │ │                       │
│        │ │ │ (orders already ready)  │ │                  │ │                       │
│        │ │ └─ orders value obtained  │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("Got $user and $orders") │             │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ coroutineScope completes │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         Scenario 3: fetchUser() fails                               │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ Enter coroutineScope     │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing             │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ FAILS ❌         │ │                       │
│        │ │ │                         │ │ (Exception)      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │ CANCELLED ⚡          │
│        │ │ │ THROWS EXCEPTION ⚡      │ │                  │ │ (structured conc.)   │
│        │ │ └─ Exception propagates   │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ❌ ordersDeferred.await() never reached        │ │                       │
│        │ │ ❌ println() never reached                     │ │                       │
│        │ │                           │ │                  │ │                       │
│   t6   │ └─ coroutineScope FAILS     │ │                  │ │                       │
│        │     with exception          │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    Scenario 4: fetchOrders() fails after fetchUser() succeeds       │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ Enter coroutineScope     │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing             │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ COMPLETES ✓      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ user value obtained    │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ FAILS ❌              │
│        │ │ │                         │ │                  │ │ (Exception)           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ THROWS EXCEPTION ⚡     │ │                  │ │                       │
│        │ │ └─ Exception propagates   │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ ❌ println() never reached                     │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ coroutineScope FAILS     │ │                  │ │                       │
│        │     with exception          │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

In a **regular `coroutineScope`**:
- If `fetchOrders()` throws an exception → the **whole scope is cancelled**, so even though `fetchUser()` finished, you never get to use it.
- The error will bubble up, and yes — you’d need a `try/catch` around your scope to handle it.

### `supervisorScope`

Here’s the twist:

- If you wrap those `async` calls inside a **`supervisorScope`**, then:
    - `fetchOrders()` failing does **not cancel** `fetchUser()`.
    - You can still get the successful result from `userDeferred.await()`.

```kotlin
supervisorScope {
    val userDeferred = async { fetchUser() }
    val ordersDeferred = async { fetchOrders() } // fails

    val user = try { userDeferred.await() } catch (e: Exception) { null }
    val orders = try { ordersDeferred.await() } catch (e: Exception) { null }

    println("User: $user, Orders: $orders")
}
```

So:
- **`coroutineScope` → fail fast (all siblings cancelled)**
- **`supervisorScope` → fail isolated (siblings keep running)**

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                 Scenario 1: Both operations succeed (supervisorScope)               │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ supervisorScope {        │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing            │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ COMPLETES ✓      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ user value obtained    │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ COMPLETES ✓           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ orders value obtained  │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("User: $user, Orders: $orders") │      │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ } // completes           │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│              Scenario 2: fetchOrders() fails, fetchUser() succeeds                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ supervisorScope {        │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing            │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ COMPLETES ✓      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ user value obtained    │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ FAILS ❌              │
│        │ │ │                         │ │                  │ │ (Exception)           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ THROWS but CATCHED 🛡️   │ │                  │ │                       │
│        │ │ └─ orders = null          │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("User: $user, Orders: null") │         │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ } // completes           │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│              Scenario 3: fetchUser() fails, fetchOrders() succeeds                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ supervisorScope {        │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing            │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ FAILS ❌         │ │                       │
│        │ │ │                         │ │ (Exception)      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ THROWS but CATCHED 🛡️   │ │                  │ │                       │
│        │ │ └─ user = null            │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ COMPLETES ✓           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ RESUMES                 │ │                  │ │                       │
│        │ │ └─ orders value obtained  │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("User: null, Orders: $orders") │       │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ } // completes           │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

```kotlin
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                 Scenario 4: Both operations fail (supervisorScope)                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Time  │ Main Coroutine              │ fetchUser()        │ fetchOrders()           │
│────────┼─────────────────────────────┼────────────────────┼─────────────────────────┤
│   t0   │ ┌─ supervisorScope {        │                    │                         │
│        │ │                           │                    │                         │
│   t1   │ │ async { fetchUser() }     │ ┌─ starts          │                         │
│        │ │ → launched                │ │ executing        │                         │
│        │ │                           │ │                  │                         │
│   t2   │ │ async { fetchOrders() }   │ │                  │ ┌─ starts               │
│        │ │ → launched                │ │                  │ │ executing            │
│        │ │                           │ │                  │ │                       │
│   t3   │ │ userDeferred.await()      │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (user not ready)        │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t4   │ │ │                         │ │ FAILS ❌         │ │                       │
│        │ │ │                         │ │ (Exception)      │ │                       │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ userDeferred.await()    │ │                  │ │                       │
│        │ │ │ THROWS but CATCHED 🛡️   │ │                  │ │                       │
│        │ │ └─ user = null            │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t5   │ │ ordersDeferred.await()    │ │                  │ │                       │
│        │ │ ┌─ Suspends               │ │                  │ │                       │
│        │ │ │ (orders not ready)      │ │                  │ │                       │
│        │ │ │                         │ │                  │ │                       │
│   t6   │ │ │                         │ │                  │ │ FAILS ❌              │
│        │ │ │                         │ │                  │ │ (Exception)           │
│        │ │ │                         │ │                  │ │                       │
│        │ │ │ ordersDeferred.await()  │ │                  │ │                       │
│        │ │ │ THROWS but CATCHED 🛡️   │ │                  │ │                       │
│        │ │ └─ orders = null          │ │                  │ │                       │
│        │ │                           │ │                  │ │                       │
│   t7   │ │ println("User: null, Orders: null") │          │ │                       │
│        │ │                           │ │                  │ │                       │
│   t8   │ └─ } // completes           │ │                  │ │                       │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```
