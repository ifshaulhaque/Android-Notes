## 🔹 1. What is Scheduling?

- The OS decides **which process** runs when there are multiple ready processes.
- Objective: maximize CPU utilization, throughput, and fairness; minimize waiting time, turnaround time, response time.

---

## 🔹 2. Key Terms

- **Burst Time (BT):** Time required by a process on CPU.
- **Arrival Time (AT):** Time at which process enters ready queue.
- **Completion Time (CT):** Time when process finishes execution.
- **Turnaround Time (TAT):**
	TAT=CT−ATTAT = CT - ATTAT=CT−AT
- **Waiting Time (WT):**
    WT=TAT−BTWT = TAT - BTWT=TAT−BT
- **Response Time (RT):** First time process gets CPU – AT.

---

## 🔹 3. Scheduling Algorithms

### (a) **First Come First Serve (FCFS)**

- **Non-preemptive**.
- Processes served in order of arrival.
- Formula: Just add burst times in order.
- Example:
    - P1: BT=5, P2: BT=3, P3: BT=8
    - Gantt: | P1 | P2 | P3 |
    - Easy to compute CT, TAT, WT.

👉 Drawback: **Convoy Effect** (short processes wait for long ones).

---

### (b) **Shortest Job First (SJF)**

- **Non-preemptive** (variant of SPN).
- Select process with smallest BT.
- Optimal in terms of **minimum average waiting time**.

👉 Preemptive version = **Shortest Remaining Time First (SRTF)**.

- Preempt whenever a new shorter process arrives.

---

### (c) **Round Robin (RR)**

- **Preemptive**. Each process gets fixed **time quantum (q)**.
- After q expires, process moves to back of ready queue.
- Response time improves but context switches ↑ if q is too small.

---

### (d) **Priority Scheduling**

- Each process assigned a priority.
- Higher priority executed first.
- Can be **preemptive or non-preemptive**.
- Problem: **Starvation** → solved by **Aging** (gradually increase priority of waiting processes).

---

### (e) **Multilevel Queue Scheduling**

- Ready queue divided into multiple queues (foreground, background).
- Each queue has its own scheduling.
- Processes permanently assigned.

### (f) **Multilevel Feedback Queue**

- Like multilevel, but processes can **move between queues**.
- Example: new process starts in higher-priority queue; if it uses too much CPU, moves down.

---

## 🔹 4. Formulas Summary (MUST REMEMBER for GATE)

- TAT=CT−ATTAT = CT - ATTAT=CT−AT
- WT=TAT−BTWT = TAT - BTWT=TAT−BT
- RT=FirstCPU−ATRT = FirstCPU - ATRT=FirstCPU−AT
- **Average** values = sum / n