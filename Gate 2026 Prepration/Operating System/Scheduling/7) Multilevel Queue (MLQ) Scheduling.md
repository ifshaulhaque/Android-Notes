### Characteristics

1. **Processes are divided into multiple queues** based on type or priority.
    - Example:
        - Queue 1 → Interactive/Foreground processes
        - Queue 2 → Batch/Background processes
2. **Each queue can have its own scheduling algorithm**.
    - Queue 1 → Round Robin (RR)
    - Queue 2 → FCFS
3. **Priority between queues:**
    - Higher-priority queues are executed first.
    - Lower-priority queues get CPU only if higher queues are empty.
4. **Non-preemptive between queues**, usually (some variants allow inter-queue preemption).
5. **Processes cannot move between queues** in standard MLQ.

---

### 🔹 Example

Suppose we have the following processes:

|Process|AT|BT|Queue|
|---|---|---|---|
|P1|0|4|Q1 (RR q=2)|
|P2|1|5|Q1 (RR q=2)|
|P3|2|6|Q2 (FCFS)|
|P4|3|3|Q2 (FCFS)|

**Queue Priority:** Q1 > Q2 (foreground > background)

---

### 🔹 Step 1: Execute Higher Priority Queue (Q1) first

- **Time quantum = 2** for Q1 → Round Robin:

1. **t=0–2:** P1 runs (rem=2)
2. **t=2–4:** P2 runs (rem=3)
3. **t=4–6:** P1 runs remaining 2 → finishes
4. **t=6–8:** P2 runs remaining 3 → finishes

---

### 🔹 Step 2: Execute Lower Priority Queue (Q2) next (FCFS)

- **t=8–14:** P3 runs → finishes at t=14
- **t=14–17:** P4 runs → finishes at t=17

---

### 🔹 Step 3: Gantt Chart

```kotlin
0    2    4    6    8       14   17
| P1 | P2 | P1 | P2 |  P3   | P4 |
```

---

### 🔹 Step 4: Completion Times (CT)

- P1 = 6
- P2 = 8
- P3 = 14
- P4 = 17

---

### 🔹 Step 5: Turnaround Time (TAT = CT – AT)

- P1 = 6 – 0 = 6
- P2 = 8 – 1 = 7
- P3 = 14 – 2 = 12
- P4 = 17 – 3 = 14

---

### 🔹 Step 6: Waiting Time (WT = TAT – BT)

- P1 = 6 – 4 = 2
- P2 = 7 – 5 = 2
- P3 = 12 – 6 = 6
- P4 = 14 – 3 = 11

---

### 🔹 Key Points for GATE

1. **Each queue can have different algorithms** → make sure you calculate **intra-queue scheduling** first.
2. **Queue priority is strict** → no process in a lower queue runs if a higher queue has ready processes.
3. **No inter-queue movement** in standard MLQ.
4. Often asked **Avg TAT, WT, or Gantt chart** questions.