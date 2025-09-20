
### 🔹 Characteristics

- **Non-preemptive**: once a process starts, it runs till completion.
- **Selection rule**: the process that arrives **earliest** is scheduled first.
- Very simple, but suffers from the **convoy effect** (short jobs wait behind long ones).

### 🔹 Example Dataset (we’ll use throughout)

|Process|Arrival Time (AT)|Burst Time (BT)|
|---|---|---|
|P1|0|5|
|P2|1|3|
|P3|2|8|
|P4|3|6|

### 🔹 Step 1: Sort by Arrival Time

Order: P1 → P2 → P3 → P4
### 🔹 Step 2: Construct Gantt Chart

- P1 starts at 0 → finishes at 5.
- P2 starts at 5 → finishes at 8.
- P3 starts at 8 → finishes at 16.
- P4 starts at 16 → finishes at 22.

```kotlin
|  P1  |  P2  |   P3   |   P4   |
0      5      8       16       22
```

### 🔹 Step 3: Compute Parameters

- **Completion Time (CT)**  
    P1=5, P2=8, P3=16, P4=22

- **Turnaround Time (TAT = CT − AT)**  
    P1 = 5 − 0 = 5  
    P2 = 8 − 1 = 7  
    P3 = 16 − 2 = 14  
    P4 = 22 − 3 = 19

- **Waiting Time (WT = TAT − BT)**  
    P1 = 5 − 5 = 0  
    P2 = 7 − 3 = 4  
    P3 = 14 − 8 = 6  
    P4 = 19 − 6 = 13

- **Response Time (RT = First CPU − AT)**  
    P1 = 0 − 0 = 0  
    P2 = 5 − 1 = 4  
    P3 = 8 − 2 = 6  
    P4 = 16 − 3 = 13

### 🔹 Step 4: Averages

- Avg TAT = (5 + 7 + 14 + 19) / 4 = **11.25**
- Avg WT = (0 + 4 + 6 + 13) / 4 = **5.75**
- Avg RT = (0 + 4 + 6 + 13) / 4 = **5.75**

✅ **Summary of FCFS**

- Very fair (serves in order of arrival).
- **Drawback:** Long processes delay all short ones (**convoy effect**).
- Easy to implement.