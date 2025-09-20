### 🔹 Characteristics

1. **Multiple queues with different priorities and scheduling algorithms** (like MLQ).
2. **Processes can move between queues** based on their CPU usage behavior:
    - CPU-bound processes → moved to lower-priority queues
    - I/O-bound or interactive processes → may stay in higher-priority queues
3. **Preemption across queues:**
    - If a process arrives in a higher-priority queue, it can preempt lower-priority queues.
4. Goal: **responsive scheduling** while preventing starvation.

---

### 🔹 Example Setup

Suppose we have 3 queues:

|Queue|Scheduling|Time Quantum|
|---|---|---|
|Q1|RR|2|
|Q2|RR|4|
|Q3|FCFS|–|

Processes:

|Process|AT|BT|Initial Queue|
|---|---|---|---|
|P1|0|5|Q1|
|P2|1|8|Q1|
|P3|2|4|Q2|
|P4|3|3|Q3|

**Rules:**

- Processes exceeding their time quantum in a queue **move to the next lower queue**.
- Higher-priority queues always execute before lower ones.

---

### 🔹 Step 1: Execute Q1 (RR q=2)

1. **t=0–2:** P1 executes (rem=3) → still in Q1? → moved to back?
2. **t=2–4:** P2 executes (rem=6) → move to back of Q1
3. **t=4–6:** P1 executes remaining 2 → rem=1 → moves to Q2?
4. **t=6–8:** P2 executes remaining 4 → moves to Q2

---

### 🔹 Step 2: Execute Q2 (RR q=4)

1. Add **new arrivals from Q1**: P1(rem=1), P2(rem=4), existing P3(BT=4)
2. Execute in RR fashion (q=4):
    - P1: 8–9 → finishes
    - P2: 9–13 → rem=0 → finishes
    - P3: 13–17 → finishes

---

### 🔹 Step 3: Execute Q3 (FCFS)

- Only P4 is left → **t=17–20** → finishes

---

### 🔹 Step 4: Gantt Chart (Approximate)

```kotlin
0    2    4    6    8    9   13   17    20
| P1 | P2 | P1 | P2 | P1 | P2 | P3 | P4 |
```

> Note: In actual GATE, exact remapping depends on **how many times the process exceeds its time quantum**, but this gives a clear conceptual flow.

---

### 🔹 Step 5: Key Points for GATE

1. MLFQ is **dynamic** → processes move between queues based on CPU burst.
2. Preemption **between queues** is always allowed (higher queue interrupts lower).
3. Avoid starvation using **aging** (increase priority of waiting processes).
4. Typically, GATE asks **execution order / Gantt chart / Avg TAT & WT**.

---

✅ **MLFQ Summary**

- Combines **priority + RR + aging** for balanced performance.
- **Better for interactive systems** than static MLQ.
- Harder to compute manually, so focus on **conceptual understanding** and small examples.