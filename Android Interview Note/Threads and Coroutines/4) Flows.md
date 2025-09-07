A **Flow** in Kotlin is like a **stream of data** that is emitted over time.

Think of it like:
- **List** → gives you all the values at once.
- **Flow** → gives you values one by one, over time.

Key points about Flow:
1. **Cold** → Nothing happens until you call `collect()`.
2. **Suspendable** → Works naturally with coroutines.
3. **Operators** → You can transform flows with operators (`map`, `filter`, `take`, etc.).
4. **Error handling** → You can catch exceptions with `catch { }`.

instead of Flow you _can_ just keep a normal variable and update it when new data arrives. But let’s think carefully about what breaks if we rely only on variables.

Problems here:
1. **No automatic updates** → every observer (UI, loggers, etc.) must be _notified manually_.
2. **No history/stream** → you only know the _current_ value, not the sequence over time.
3. **No async operators** → you can’t easily do things like `filter`, `map`, `combine` on a plain variable.

### Flow / StateFlow

A Flow is like a **pipeline** that anyone can subscribe to.  
When the value changes, all collectors automatically get notified.

Advantages:
- **Automatic push** → UI always reacts to changes, no manual notification.
- **Multiple collectors** → UI + logger + analytics can all listen independently.
- **Operators** → You can easily transform (`.map { it * 1.8 + 32 }`) or combine with other flows (`combine(locationFlow, temperatureFlow)`).

### **Flow value History**
---
### 1. **Plain Flow**

A simple `flow { emit(...) }` does **not** remember past values.  
It’s _cold_: every new collector starts from the beginning of the flow.

```kotlin
val numbersFlow = flow {
    emit(1)
    emit(2)
    emit(3)
}
```

- Collector A → gets 1,2,3
- Collector B (later) → also gets 1,2,3  
    (but both don’t see each other’s emissions; they restart fresh)

So **no shared history here**.


### 2. **StateFlow**

This one always holds the **latest value** (like a sticky variable).  
But it does **not** keep _all past values_, only the most recent.

```kotlin
val temp = MutableStateFlow(25)
temp.value = 26
```

- Collector joins → it immediately sees `26` (latest).
- But it cannot see old values like `25`.

### 3. **SharedFlow**

This is where **history / replay** comes in.  
A `MutableSharedFlow` can be configured with a **replay cache** — the number of past values it remembers for new subscribers.

```kotlin
val events = MutableSharedFlow<Int>(replay = 2)

lifecycleScope.launch {
    events.emit(1)
    events.emit(2)
    events.emit(3)
}

lifecycleScope.launch {
    events.collect { println("Collector: $it") }
}
```

The second collector will instantly receive the last **2 values** (`2`, `3`) from the replay cache, even though they were emitted earlier.