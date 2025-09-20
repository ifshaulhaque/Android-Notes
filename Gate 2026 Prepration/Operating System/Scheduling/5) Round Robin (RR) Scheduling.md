### 🔹 Characteristics

- **Preemptive** scheduling.
- Each process gets a fixed **time quantum (q)**.
- If a process **finishes before q**, CPU switches to next.
- If a process **needs more than q**, it’s preempted → moved to back of ready queue.
- **Fair & responsive**, widely used in **time-sharing systems**.

---

### 🔹 Example Dataset

We use the same processes with **q = 3**:

|Process|AT|BT|
|---|---|---|
|P1|0|5|
|P2|1|3|
|P3|2|8|
|P4|3|6|

---

### 🔹 Step 1: Execution Order

- **t=0–3:** P1 executes for 3 → remaining=2.
- **t=3–6:** P2 executes for 3 → finishes (BT=3).
- **t=6–9:** P3 executes for 3 → remaining=5.
- **t=9–12:** P4 executes for 3 → remaining=3.
- **t=12–14:** P1 executes remaining 2 → finishes.
- **t=14–17:** P3 executes for 3 → remaining=2.
- **t=17–20:** P4 executes remaining 3 → finishes.
- **t=20–22:** P3 executes remaining 2 → finishes.

---

### 🔹 Step 2: Gantt Chart

```kotlin
| P1 | P2 | P3 | P4 | P1 | P3 | P4 | P3 | 
0    3    6    9    12   14   17   20   22
```

---

### 🔹 Step 3: Compute Parameters

- **Completion Time (CT):**  
    P1=14, P2=6, P3=22, P4=20

- **Turnaround Time (TAT = CT − AT):**  
    P1 = 14 − 0 = 14  
    P2 = 6 − 1 = 5  
    P3 = 22 − 2 = 20  
    P4 = 20 − 3 = 17

- **Waiting Time (WT = TAT − BT):**  
    P1 = 14 − 5 = 9  
    P2 = 5 − 3 = 2  
    P3 = 20 − 8 = 12  
    P4 = 17 − 6 = 11

- **Response Time (RT = First CPU − AT):**  
    P1 = 0 − 0 = 0  
    P2 = 3 − 1 = 2  
    P3 = 6 − 2 = 4  
    P4 = 9 − 3 = 6


---

### 🔹 Step 4: Averages

- Avg TAT = (14 + 5 + 20 + 17) / 4 = **14.0**
- Avg WT = (9 + 2 + 12 + 11) / 4 = **8.5**
- Avg RT = (0 + 2 + 4 + 6) / 4 = **3.0**

---

✅ **Summary of RR**

- Good **response time** → ideal for **interactive systems**.
- Performance depends heavily on **quantum size (q):**
    - If q too large → behaves like **FCFS**.
    - If q too small → too many **context switches** (overhead ↑).