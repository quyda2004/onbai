# CPU Scheduling

---

## Giải thích cho người mới hoàn toàn

Tưởng tượng một quầy thu ngân ở siêu thị chỉ có một người phục vụ, nhưng có hàng chục khách hàng xếp hàng. Người quản lý cửa hàng phải quyết định: phục vụ theo thứ tự ai đến trước, hay ưu tiên người mua ít hàng hơn để thanh toán nhanh, hay mỗi người chỉ được phục vụ đúng 2 phút rồi đứng ra xếp hàng lại?

**CPU Scheduling** là bộ "quản lý" đó — quyết định process nào được dùng CPU tiếp theo và trong bao lâu.

**CPU Burst** là thời gian một chương trình đang thực sự chạy tính toán (như khách đang thanh toán). **I/O Burst** là thời gian chương trình chờ đọc/ghi file hoặc nhận dữ liệu từ mạng (như khách chờ nhân viên lấy hàng từ kho).

**Preemptive scheduling** (giành quyền): OS có thể "giật" CPU từ một process đang chạy để cho process khác. Như thu ngân có thể dừng phục vụ khách A giữa chừng nếu có VIP đến.

**Non-preemptive scheduling** (không giành quyền): Process giữ CPU cho đến khi tự nguyện nhả ra (xong việc hoặc đợi I/O). Ai đang được phục vụ thì phục vụ xong mới đến người khác.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### CPU Burst vs I/O Burst Pattern

Hầu hết processes xen kẽ giữa CPU bursts và I/O bursts:
- **CPU-bound process**: bursts dài, ít I/O (video encoding, scientific computing)
- **I/O-bound process**: bursts ngắn, nhiều I/O (web server, database queries)

Scheduler hoạt động ở **short-term scheduler** (dispatch) level — quyết định hàng ms.

### Các Metrics Đánh Giá

- **CPU Utilization**: % thời gian CPU bận (mục tiêu: 40–90%)
- **Throughput**: số processes hoàn thành/đơn vị thời gian
- **Turnaround Time (TAT)**: Completion time – Arrival time
- **Waiting Time (WT)**: TAT – Burst time = thời gian chờ trong ready queue
- **Response Time**: thời gian từ khi submit đến first response (quan trọng với interactive systems)

**Công thức cốt lõi:**
```
Turnaround Time  = Completion Time – Arrival Time
Waiting Time     = Turnaround Time – Burst Time
Response Time    = First CPU Time  – Arrival Time
Average WT       = (ΣWT_i) / n
```

### FCFS (First-Come, First-Served)

**Cơ chế**: Ready queue là FIFO — ai đến trước phục vụ trước, non-preemptive.

**Ví dụ tính toán:**

| Process | Arrival | Burst | Completion | TAT | WT |
|---------|---------|-------|-----------|-----|----|
| P1      | 0       | 10    | 10        | 10  | 0  |
| P2      | 1       | 5     | 15        | 14  | 9  |
| P3      | 2       | 8     | 23        | 21  | 13 |

Average WT = (0 + 9 + 13) / 3 = **7.33 ms**

**Convoy Effect**: Một CPU-bound process lớn chặn tất cả I/O-bound processes phía sau → CPU utilization tổng thể thấp vì I/O devices idle trong khi các processes nhỏ chờ.

### SJF (Shortest Job First)

**Cơ chế**: Chọn process có **burst time ngắn nhất** trong ready queue. Non-preemptive.

**Lý thuyết**: SJF tối ưu về average waiting time (có thể chứng minh bằng exchange argument).

**Vấn đề**: Không thể biết chính xác burst time tiếp theo → **xấp xỉ bằng exponential averaging**:
```
τ_(n+1) = α * t_n + (1 - α) * τ_n
```
Trong đó: `τ_n` = predicted burst, `t_n` = actual burst, `α` ∈ [0,1] (thường α = 0.5)

**Ví dụ:**

| Process | Arrival | Burst | Completion | TAT | WT |
|---------|---------|-------|-----------|-----|----|
| P1      | 0       | 6     | 6         | 6   | 0  |
| P2      | 0       | 8     | 21        | 21  | 13 |
| P3      | 0       | 7     | 13        | 13  | 6  |
| P4      | 0       | 3     | 3         | 3   | 0  |

Thứ tự chạy: P4(3) → P1(6) → P3(7) → P2(8)
Average WT = (0 + 13 + 6 + 0) / 4 = **4.75 ms**

### SRTF (Shortest Remaining Time First)

**Cơ chế**: Phiên bản **preemptive của SJF**. Khi process mới đến, nếu remaining burst của nó nhỏ hơn remaining burst của process đang chạy → preempt.

**Ví dụ:**

| Process | Arrival | Burst |
|---------|---------|-------|
| P1      | 0       | 8     |
| P2      | 1       | 4     |
| P3      | 2       | 9     |
| P4      | 3       | 5     |

```
Timeline:
t=0: P1 chạy (burst còn 8)
t=1: P2 đến (burst=4 < P1 remaining=7) → preempt P1, chạy P2
t=5: P2 xong. P4 đến t=3 (burst=5). P1 remaining=7. P3 remaining=9
     → Chạy P4 (nhỏ nhất trong {P1:7, P3:9, P4:5 → đã đến})
t=10: P4 xong. Chạy P1 (remaining 7)
t=17: P1 xong. Chạy P3
t=26: P3 xong.
```

| Process | Completion | TAT | WT |
|---------|-----------|-----|----|
| P1      | 17        | 17  | 9  |
| P2      | 5         | 4   | 0  |
| P3      | 26        | 24  | 15 |
| P4      | 10        | 7   | 2  |

Average WT = (9 + 0 + 15 + 2) / 4 = **6.5 ms**

### Round Robin (RR)

**Cơ chế**: Mỗi process được chạy tối đa **time quantum q** (thường 10–100 ms). Sau đó bị preempt và đưa về cuối ready queue.

**Ảnh hưởng của time quantum q:**
- **q rất lớn** → degenerates thành FCFS
- **q rất nhỏ** → context switch overhead chiếm ưu thế, throughput giảm
- **q optimal** ≈ 80% CPU bursts nên nhỏ hơn q (hầu hết jobs xong trong 1 quantum)
- **Rule of thumb**: q nên lớn hơn 80% CPU burst duration

**Ví dụ (q = 4):**

| Process | Arrival | Burst |
|---------|---------|-------|
| P1      | 0       | 10    |
| P2      | 0       | 6     |
| P3      | 0       | 4     |

```
Timeline: P1(4) → P2(4) → P3(4) → P1(4) → P2(2) → P1(2)
t:        0  4    8  12   16  20   24  28   32  34   36 38
```

| Process | Completion | TAT | WT |
|---------|-----------|-----|----|
| P1      | 38        | 38  | 28 |
| P2      | 34        | 34  | 28 |
| P3      | 16        | 16  | 12 |

**Response time tốt hơn FCFS** nhưng average WT có thể cao hơn SJF.

### Priority Scheduling

**Cơ chế**: Mỗi process có priority number. Process có priority cao nhất (số nhỏ hoặc lớn tùy convention) được chọn.

**Starvation**: Low-priority processes có thể chờ mãi nếu liên tục có high-priority processes đến.

**Aging Solution**: Tăng dần priority của process theo thời gian chờ.
```
priority(t) = initial_priority + waiting_time / aging_factor
```

Linux dùng **dynamic priority** = static priority (nice value -20..+19) + bonus dựa trên I/O behavior.

### Multilevel Queue Scheduling

Ready queue được chia thành nhiều queues riêng biệt, mỗi queue có scheduling algorithm riêng:
```
[Queue 0: Real-time]     → FIFO, priority tuyệt đối
[Queue 1: Interactive]   → Round Robin (q=8ms)
[Queue 2: Batch]         → FCFS
[Queue 3: Background]    → FCFS, priority thấp nhất
```
Process được assign cố định vào một queue khi tạo ra.

### Multilevel Feedback Queue (MLFQ)

Cải tiến của Multilevel Queue: **process có thể di chuyển giữa các queues** dựa trên behavior.

**Quy tắc MLFQ:**
1. Nếu Priority(A) > Priority(B) → A chạy
2. Nếu Priority(A) = Priority(B) → Round Robin
3. Process mới bắt đầu ở **queue cao nhất** (priority cao)
4. Nếu process dùng hết time quantum → **giảm priority** (xuống queue thấp hơn)
5. Nếu process yield CPU trước khi hết quantum (I/O) → **giữ hoặc tăng priority**
6. **Priority boost** định kỳ: tất cả process lên queue cao nhất (tránh starvation)

MLFQ tự động phân biệt CPU-bound vs I/O-bound processes:
- I/O-bound: thường ở queue cao (priority cao, response time tốt)
- CPU-bound: dần xuống queue thấp (quantum lớn hơn, throughput tốt hơn)

Linux scheduler: **CFS (Completely Fair Scheduler)** dùng red-black tree theo virtual runtime.

---

## Định nghĩa chính xác

**CPU Scheduling**: Hoạt động của OS chọn process tiếp theo được dispatch lên CPU từ ready queue, nhằm tối ưu các metrics như CPU utilization, throughput, turnaround time, waiting time, response time.

**Preemptive Scheduling**: Scheduler có thể thu hồi CPU từ process đang chạy bất kỳ lúc nào (thường do timer interrupt hoặc higher-priority process sẵn sàng).

**Non-preemptive Scheduling**: CPU chỉ được giải phóng khi process tự nguyện relinquish (terminate hoặc block on I/O).

---

## Bảng so sánh / Sơ đồ kỹ thuật

### So sánh tất cả Scheduling Algorithms

| Algorithm | Preemptive | Starvation | Context Switch Overhead | Average WT | Tốt khi nào |
|-----------|-----------|-----------|------------------------|-----------|-------------|
| FCFS | Không | Không | Thấp | Cao (convoy) | Batch, burst uniform |
| SJF | Không | Có (long jobs) | Thấp | Tối ưu (non-preemptive) | Biết trước burst time |
| SRTF | Có | Có (long jobs) | Cao | Tối ưu (preemptive) | Biết burst, tối thiểu WT |
| Round Robin | Có | Không | Trung bình | Cao nếu q lớn | Time-sharing, interactive |
| Priority | Có/Không | Có (low priority) | Trung bình | Trung bình | Real-time, phân loại rõ |
| MLFQ | Có | Không (aging) | Cao | Tốt | General purpose OS |

### Gantt Chart — FCFS Ví dụ

```
Process:  |  P1  |  P2  |  P3  |
Time:    0     10     15     23
```

### Gantt Chart — Round Robin (q=4) Ví dụ

```
Process:  | P1 | P2 | P3 | P1 | P2 | P1 |
Time:    0   4   8  12  16  20  22  26
```

### Trạng thái Process (State Diagram)

```
          admit
New ─────────────→ Ready ←──────────── Running
                     │       I/O done /   │    │
                     │       event occurs  │    │ preempt /
              dispatch│                   │    │ time quantum
              (scheduler)                 │    ↓
                     └──────────────→ Running ──→ Terminated
                                          │
                                     I/O / event
                                          ↓
                                        Waiting
```

---

## Code mẫu

```python
from collections import deque

# ============================================================
# CPU Scheduling Simulator
# ============================================================

class Process:
    def __init__(self, pid, arrival, burst, priority=0):
        self.pid = pid
        self.arrival = arrival
        self.burst = burst
        self.remaining = burst  # Dùng cho preemptive
        self.priority = priority
        self.completion = 0
        self.first_response = -1  # Lần đầu lên CPU

    @property
    def turnaround(self):
        return self.completion - self.arrival

    @property
    def waiting(self):
        return self.turnaround - self.burst


def print_results(processes, algo_name):
    print(f"\n{'='*60}")
    print(f"Algorithm: {algo_name}")
    print(f"{'PID':<6} {'Arrival':<8} {'Burst':<7} {'Completion':<12} {'TAT':<6} {'WT':<5}")
    for p in sorted(processes, key=lambda x: x.pid):
        print(f"{p.pid:<6} {p.arrival:<8} {p.burst:<7} {p.completion:<12} {p.turnaround:<6} {p.waiting:<5}")
    avg_tat = sum(p.turnaround for p in processes) / len(processes)
    avg_wt = sum(p.waiting for p in processes) / len(processes)
    print(f"Average TAT: {avg_tat:.2f} | Average WT: {avg_wt:.2f}")


def fcfs(processes):
    """First-Come, First-Served"""
    procs = sorted(processes, key=lambda p: p.arrival)
    current_time = 0
    for p in procs:
        # Nếu CPU idle, nhảy đến thời điểm process arrive
        if current_time < p.arrival:
            current_time = p.arrival
        current_time += p.burst
        p.completion = current_time
    print_results(procs, "FCFS")


def sjf_non_preemptive(processes):
    """Shortest Job First — Non-preemptive"""
    procs = [Process(p.pid, p.arrival, p.burst) for p in processes]
    remaining = list(procs)
    current_time = 0
    completed = []
    
    while remaining:
        # Lấy các processes đã arrive
        available = [p for p in remaining if p.arrival <= current_time]
        if not available:
            # CPU idle: nhảy đến process sớm nhất
            current_time = min(p.arrival for p in remaining)
            continue
        # Chọn process có burst ngắn nhất
        shortest = min(available, key=lambda p: p.burst)
        remaining.remove(shortest)
        current_time += shortest.burst
        shortest.completion = current_time
        completed.append(shortest)
    
    print_results(completed, "SJF (Non-preemptive)")


def round_robin(processes, quantum):
    """Round Robin với time quantum"""
    procs = [Process(p.pid, p.arrival, p.burst) for p in processes]
    # Reset remaining
    for p in procs:
        p.remaining = p.burst
    
    queue = deque()
    current_time = 0
    remaining_procs = sorted(procs, key=lambda p: p.arrival)
    idx = 0  # Index vào remaining_procs (sorted by arrival)
    
    # Thêm processes đã arrive vào queue
    while idx < len(remaining_procs) and remaining_procs[idx].arrival <= current_time:
        queue.append(remaining_procs[idx])
        idx += 1
    
    gantt = []
    while queue or idx < len(remaining_procs):
        if not queue:
            # CPU idle
            current_time = remaining_procs[idx].arrival
            queue.append(remaining_procs[idx])
            idx += 1
        
        p = queue.popleft()
        
        # Ghi nhận response time
        if p.first_response == -1:
            p.first_response = current_time
        
        # Chạy tối đa 1 quantum
        run_time = min(quantum, p.remaining)
        gantt.append(f"P{p.pid}({run_time})")
        current_time += run_time
        p.remaining -= run_time
        
        # Thêm processes mới arrive trong khi đang chạy
        while idx < len(remaining_procs) and remaining_procs[idx].arrival <= current_time:
            queue.append(remaining_procs[idx])
            idx += 1
        
        if p.remaining > 0:
            queue.append(p)  # Chưa xong, đưa về cuối queue
        else:
            p.completion = current_time
    
    print_results(procs, f"Round Robin (q={quantum})")
    print(f"Gantt: {' → '.join(gantt)}")


# ============================================================
# Test
# ============================================================
processes = [
    Process("P1", arrival=0, burst=10),
    Process("P2", arrival=1, burst=5),
    Process("P3", arrival=2, burst=8),
    Process("P4", arrival=3, burst=3),
]

fcfs([Process(p.pid, p.arrival, p.burst) for p in processes])
sjf_non_preemptive(processes)
round_robin(processes, quantum=4)

# ============================================================
# Minh họa Aging để tránh Starvation
# ============================================================
print("\n" + "="*60)
print("Priority Aging Simulation:")

class PriorityProcess:
    def __init__(self, pid, priority):
        self.pid = pid
        self.priority = priority
        self.wait_time = 0
        self.base_priority = priority

aging_procs = [
    PriorityProcess("P_high", priority=1),   # Priority cao
    PriorityProcess("P_low",  priority=10),  # Priority thấp → có thể starvation
]

# Mỗi time unit, P_high chạy, P_low chờ và được aging
AGING_RATE = 1  # Giảm priority (số) 1 đơn vị mỗi 2 time units chờ
for t in range(1, 11):
    runner = min(aging_procs, key=lambda p: p.priority)
    for p in aging_procs:
        if p != runner:
            p.wait_time += 1
            # Aging: giảm priority number (nghĩa là tăng độ ưu tiên)
            p.priority = max(1, p.base_priority - p.wait_time // 2)
    print(f"t={t}: {runner.pid} chạy | P_low priority = {aging_procs[1].priority} (waited {aging_procs[1].wait_time})")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**FCFS — Dùng khi:**
- Batch processing systems, jobs có burst time tương đương nhau
- Đơn giản, không cần overhead

**FCFS — Không dùng khi:**
- Interactive systems (response time quan trọng)
- Mix CPU-bound và I/O-bound processes (convoy effect)

**SJF/SRTF — Dùng khi:**
- Batch systems biết trước hoặc ước lượng được burst time
- Muốn tối thiểu average waiting time

**SJF/SRTF — Không dùng khi:**
- Long-running processes quan trọng (starvation risk)
- Không thể ước lượng burst time

**Round Robin — Dùng khi:**
- Time-sharing systems, interactive users
- Response time quan trọng hơn throughput
- Fairness giữa các processes

**MLFQ — Dùng khi:**
- General-purpose OS (Linux, Windows, macOS)
- Mix workloads (interactive + batch + background)

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm Waiting Time với Response Time**: WT = tổng thời gian ở ready queue; Response Time = khoảng thời gian đến lần đầu lên CPU. Với non-preemptive: WT = Response Time.
- **SRTF tính sai khi processes arrive giữa chừng**: Phải kiểm tra preemption tại mỗi arrival event.
- **Round Robin với q=1**: Mỗi unit time context switch → overhead lớn, throughput thấp. q không nên quá nhỏ.
- **SJF không loại trừ starvation**: Process burst dài có thể chờ vô thời hạn nếu liên tục có process burst ngắn arrive.
- **Aging giải quyết starvation nhưng cần tune rate**: Aging quá nhanh → high-priority jobs bị ảnh hưởng; quá chậm → low-priority vẫn starve.
- **MLFQ gaming**: Process có thể yield CPU ngay trước khi hết quantum để giữ priority cao → OS hiện đại tính accounting time, không chỉ quantum.

---

## Câu hỏi phỏng vấn hay gặp

**Cơ bản:**
1. Phân biệt preemptive và non-preemptive scheduling. Cho ví dụ.
2. Tính waiting time và turnaround time cho FCFS với 3 processes cho trước.
3. Tại sao SJF tối ưu về average waiting time?
4. Convoy effect là gì? Xảy ra với algorithm nào?

**Trung bình:**
5. Round Robin với q nhỏ và q lớn ảnh hưởng như thế nào đến performance?
6. Priority scheduling bị starvation — aging giải quyết thế nào?
7. Giải thích MLFQ. Process CPU-bound và I/O-bound sẽ ở queue nào?
8. Tính SRTF cho bộ processes {P1: arrival=0, burst=8}, {P2: arrival=1, burst=4}.

**Nâng cao:**
9. Linux CFS (Completely Fair Scheduler) hoạt động như thế nào? Tại sao dùng red-black tree?
10. Real-time scheduling (Rate Monotonic, EDF) khác scheduling thông thường thế nào?
11. Multiprocessor scheduling thêm complexity gì? Processor affinity là gì?
12. Nếu bạn thiết kế scheduler cho web server, bạn optimize theo metric nào và tại sao?
