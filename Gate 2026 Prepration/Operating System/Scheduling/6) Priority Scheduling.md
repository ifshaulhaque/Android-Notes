In this algorithm, each process is assigned a **priority number**.

- The CPU is allocated to the process with the **highest priority** (usually the lowest number = highest priority, but convention may differ).
- It can be **Preemptive** or **Non-Preemptive**.

---

### **Rules**

1. Processes with **higher priority** get CPU first.
2. If two processes have the same priority, then FCFS order is followed.
3. **Preemptive Priority Scheduling**: If a new process arrives with higher priority than the currently running one, it preempts the CPU.
4. **Non-Preemptive Priority Scheduling**: Once a process starts, it cannot be stopped until it finishes, even if a higher priority process arrives.

---

### **Parameters**

We calculate:

- **Waiting Time (WT)** = Turnaround Time – Burst Time
- **Turnaround Time (TAT)** = Completion Time – Arrival Time
- **Response Time (RT)** = First CPU Allocation – Arrival Time
- **Average values** are taken over all processes.

---

### **Example** (Non-Preemptive Priority Scheduling)

|Process|Arrival Time (AT)|Burst Time (BT)|Priority|
|---|---|---|---|
|P1|0|7|2|
|P2|2|4|1|
|P3|4|1|3|

👉 (Here, smaller number = higher priority)

---

#### **Step 1: Execution Order**

- At time 0 → P1 arrives → only P1 executes.
- At time 2 → P2 arrives (priority 1, higher than P1’s 2). **But since this is NON-preemptive, P1 continues**.
- P1 completes at time 7.
- Now, between P2 (priority 1) and P3 (priority 3), → **P2 is chosen**.
- Then P3 executes last.

**Execution order:** P1 → P2 → P3

---

```kotlin
0       7        11    12
|-------|---------|-----|
   P1        P2      P3
```

---
#### **Step 2: Completion Time (CT)**

- P1: 0 → 7 → CT = 7
- P2: 7 → 11 → CT = 11
- P3: 11 → 12 → CT = 12

---

#### **Step 3: Turnaround Time (TAT = CT – AT)**

- P1: 7 – 0 = 7
- P2: 11 – 2 = 9
- P3: 12 – 4 = 8

---

#### **Step 4: Waiting Time (WT = TAT – BT)**

- P1: 7 – 7 = 0
- P2: 9 – 4 = 5
- P3: 8 – 1 = 7

---

#### **Step 5: Averages**

- Avg TAT = (7 + 9 + 8) / 3 = **8.0**
- Avg WT = (0 + 5 + 7) / 3 = **4.0**

---

✅ **Summary:**

- Simple and efficient when priorities are clear.
- Problem: **Starvation** (low-priority processes may wait indefinitely if higher-priority ones keep coming).
- **Solution**: Aging (increase priority of waiting processes over time).

---

### Example (Preemptive Priority Scheduling)

|Process|Arrival Time (AT)|Burst Time (BT)|Priority|
|---|---|---|---|
|P1|0|7|2|
|P2|2|4|1|
|P3|4|1|3|

👉 Rule: **Lower number = higher priority**  
👉 Preemptive: if a higher priority process arrives, it will interrupt.

---

### Step 1: Execution order

- **t=0 to 2** → Only **P1** runs (remaining BT = 5).
- **t=2** → P2 arrives (priority 1, higher than P1’s priority 2) → P1 is **preempted**, **P2 executes**.
- **t=2 to 6** → P2 runs fully (BT=4).
- **t=6** → P2 finishes. Now we have P1 (remaining 5) and P3 (arrived at t=4, priority 3).  
    → P1 (priority 2) is higher than P3 (priority 3).
- **t=6 to 11** → P1 executes remaining 5 units.
- **t=11 to 12** → P3 runs last.

**Execution order:** P1 → P2 → P1 → P3

---

### Step 2: Gantt Chart

```kotlin
0   2    6       11     12
|---|----|-------|------|
 P1   P2    P1      P3
```

---

### Step 3: Completion Time (CT)

- P1: finishes at **11**
- P2: finishes at **6**
- P3: finishes at **12**

---

### Step 4: Turnaround Time (TAT = CT – AT)

- P1: 11 – 0 = 11
- P2: 6 – 2 = 4
- P3: 12 – 4 = 8

---

### Step 5: Waiting Time (WT = TAT – BT)

- P1: 11 – 7 = 4
- P2: 4 – 4 = 0
- P3: 8 – 1 = 7

---

### Step 6: Averages

- Avg TAT = (11 + 4 + 8) / 3 = **7.67**
- Avg WT = (4 + 0 + 7) / 3 = **3.67**

---

✅ **Summary (Preemptive Priority Scheduling):**

- Processes with higher priority can preempt lower priority ones.
- Ensures quick response for urgent processes.
- Problem: **Starvation** for low priority processes.
- Solution: **Aging** (increase priority of waiting processes gradually).