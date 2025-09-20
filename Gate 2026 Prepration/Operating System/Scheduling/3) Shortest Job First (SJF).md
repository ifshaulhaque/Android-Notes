
### 🔹 Characteristics

- **Non-preemptive**: Once a process starts, it runs till it finishes.
- Select the process with **shortest burst time** among the ready processes.
- Optimal in terms of **average waiting time**.
- Problem: **Starvation** (if short jobs keep coming, long ones may wait forever).

---

### 🔹 Example Dataset

| Process | Arrival Time (AT) | Burst Time (BT) |
| ------- | ----------------- | --------------- |
| P1      | 0                 | 5               |
| P2      | 1                 | 3               |
| P3      | 2                 | 8               |
| P4      | 3                 | 6               |

---

### 🔹 Step 1: Execution Order

- **t=0** → Only P1 available → run P1 (0–5).
- **t=5** → Ready: P2 (3), P3 (8), P4 (6). Smallest = P2.
- Run P2 (5–8).
- **t=8** → Remaining: P3 (8), P4 (6). Smallest = P4.
- Run P4 (8–14).
- **t=14** → Run P3 (14–22).

---

### 🔹 Step 2: Gantt Chart

```kotlin
|  P1  |  P2  |  P4  |   P3   | 
0      5      8      14       22
```
---

### 🔹 Step 3: Compute Parameters

- **Completion Time (CT):**  
    P1=5, P2=8, P4=14, P3=22

- **Turnaround Time (TAT = CT − AT):**  
    P1 = 5 − 0 = 5  
    P2 = 8 − 1 = 7  
    P3 = 22 − 2 = 20  
    P4 = 14 − 3 = 11

- **Waiting Time (WT = TAT − BT):**  
    P1 = 5 − 5 = 0  
    P2 = 7 − 3 = 4  
    P3 = 20 − 8 = 12  
    P4 = 11 − 6 = 5

- **Response Time (RT = First CPU − AT):**  
    P1 = 0 − 0 = 0  
    P2 = 5 − 1 = 4  
    P3 = 14 − 2 = 12  
    P4 = 8 − 3 = 5


---

### 🔹 Step 4: Averages

- Avg TAT = (5 + 7 + 20 + 11) / 4 = **10.75**
- Avg WT = (0 + 4 + 12 + 5) / 4 = **5.25**
- Avg RT = (0 + 4 + 12 + 5) / 4 = **5.25**

---

✅ **Summary of SJF (Non-preemptive)**

- **Best average WT & TAT** among non-preemptive.
- **Starvation possible** for long jobs.
- Implementation requires knowledge of **burst times** (difficult in practice).