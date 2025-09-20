
### 🔹 Characteristics

- **Preemptive version of SJF**.
- At any time, the CPU is allocated to the process with the **shortest remaining burst time**.
- If a new process arrives with smaller remaining time than the current one → **preemption happens**.
- Optimal for **minimum average turnaround time**.
- Like SJF, may cause **starvation** of long processes.

---

### 🔹 Example Dataset

|Process|Arrival Time (AT)|Burst Time (BT)|
|---|---|---|
|P1|0|5|
|P2|1|3|
|P3|2|8|
|P4|3|6|

---

### 🔹 Step 1: Execution Timeline

- **t=0** → P1 starts (rem=5).
- **t=1** → P2 arrives (BT=3 < P1’s rem=4) → preempt.
- Run P2 (1–4).
- **t=4** → P1(rem=4), P3(8), P4(6). Smallest = P1.
- Run P1 (4–8).
- **t=8** → Remaining: P3(8), P4(6). Smallest = P4.
- Run P4 (8–14).
- **t=14** → Run P3 (14–22).

---

### 🔹 Step 2: Gantt Chart

```kotlin
| P1 | P2 | P1 | P4 |   P3   | 
0    1    4    8    14       22
```
---

### 🔹 Step 3: Compute Parameters

- **Completion Time (CT):**  
    P1=8, P2=4, P4=14, P3=22

- **Turnaround Time (TAT = CT − AT):**  
    P1 = 8 − 0 = 8  
    P2 = 4 − 1 = 3  
    P3 = 22 − 2 = 20  
    P4 = 14 − 3 = 11

- **Waiting Time (WT = TAT − BT):**  
    P1 = 8 − 5 = 3  
    P2 = 3 − 3 = 0  
    P3 = 20 − 8 = 12  
    P4 = 11 − 6 = 5

- **Response Time (RT = First CPU − AT):**  
    P1 = 0 − 0 = 0  
    P2 = 1 − 1 = 0  
    P3 = 14 − 2 = 12  
    P4 = 8 − 3 = 5


---

### 🔹 Step 4: Averages

- Avg TAT = (8 + 3 + 20 + 11) / 4 = **10.5**
- Avg WT = (3 + 0 + 12 + 5) / 4 = **5.0**
- Avg RT = (0 + 0 + 12 + 5) / 4 = **4.25**

---

✅ **Summary of SRTF**

- Improves **response time** compared to non-preemptive SJF.
- Still optimal for **avg turnaround**.
- Implementation is more complex (requires dynamic remaining-time check).
- Can cause **starvation** of long processes.