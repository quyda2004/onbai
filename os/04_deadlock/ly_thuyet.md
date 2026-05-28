# Deadlock

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng hai người đang ngồi ăn tối, nhưng chỉ có một đôi đũa dùng chung. Người A đang cầm đũa trái, người B đang cầm đũa phải. Cả hai đều chờ người kia đặt đũa xuống để lấy chiếc còn lại. Không ai nhường trước → cả hai ngồi chết đói mãi mãi. Đây chính là **deadlock**.

Trong máy tính: process A đang giữ resource R1 và chờ R2. Process B đang giữ R2 và chờ R1. Cả hai chờ nhau → hệ thống đóng băng.

**Livelock** khác deadlock: hai người trong hành lang cùng tránh sang một bên, rồi lại cùng tránh sang bên kia — cứ di chuyển liên tục nhưng không ai đi được. Hệ thống vẫn "chạy" nhưng không tiến triển.

**Starvation** khác deadlock: một người xếp hàng nhưng cứ bị chen ngang mãi — vẫn sống nhưng không bao giờ được phục vụ.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### 4 Điều Kiện Coffman (Điều Kiện Cần và Đủ)

Deadlock xảy ra **khi và chỉ khi** cả 4 điều kiện cùng tồn tại:

1. **Mutual Exclusion**: Ít nhất một resource phải được giữ theo kiểu exclusive (không chia sẻ được). Ví dụ: mutex, printer.

2. **Hold and Wait**: Process đang giữ ít nhất một resource trong khi chờ acquire thêm resource khác.

3. **No Preemption**: Resource không thể bị lấy cưỡng bức từ process đang giữ — chỉ được release tự nguyện.

4. **Circular Wait**: Tồn tại chuỗi vòng tròn {P0, P1, ..., Pn} trong đó P0 chờ resource của P1, P1 chờ của P2, ..., Pn chờ của P0.

### Resource Allocation Graph (RAG)

- **Node process**: hình tròn (Pi)
- **Node resource**: hình vuông (Rj), có thể có nhiều instances (chấm trong hình vuông)
- **Request edge**: Pi → Rj (process Pi đang chờ resource Rj)
- **Assignment edge**: Rj → Pi (resource Rj đang được giữ bởi Pi)

**Với single-instance resources**: Cycle trong RAG ⟺ Deadlock.

**Với multi-instance resources**: Cycle là điều kiện cần nhưng chưa đủ → cần Banker's Algorithm.

```
Single instance deadlock:
P1 → R1 → P2 → R2 → P1  (cycle = deadlock)

Multi-instance, cycle nhưng không deadlock:
P1 → R1(2 instances) ← P2
P2 → R2 → P3
R2 có 2 instances: một cho P2, một có thể cấp P3 → P3 xong → nhả R2 cho P1/P2 → không deadlock
```

### Deadlock Prevention: Phá Từng Điều Kiện

| Điều kiện phá | Cách thực hiện | Trade-off |
|--------------|----------------|-----------|
| **Mutual Exclusion** | Dùng sharable resources (read-only files) | Không khả thi với nhiều resources |
| **Hold and Wait** | Process phải request tất cả resources trước khi bắt đầu, hoặc nhả hết trước khi request thêm | Utilization thấp, starvation có thể xảy ra |
| **No Preemption** | Nếu process chờ resource không available → preempt và release tất cả resources hiện có | Chỉ khả thi với resources có thể save/restore state (CPU registers, memory) |
| **Circular Wait** | Đánh số thứ tự tất cả resource types, process chỉ được request theo thứ tự tăng dần | Cần global ordering, không linh hoạt |

**Circular Wait Prevention** là phổ biến nhất trong thực tế:
```python
# WRONG: có thể deadlock
thread_1: lock(mutex_A); lock(mutex_B)
thread_2: lock(mutex_B); lock(mutex_A)

# CORRECT: luôn lock theo thứ tự cố định
thread_1: lock(mutex_A); lock(mutex_B)  # id(A) < id(B)
thread_2: lock(mutex_A); lock(mutex_B)  # same order
```

### Deadlock Avoidance: Banker's Algorithm

**Ý tưởng**: Trước khi cấp resource, giả lập xem hệ thống có còn ở **safe state** không. Chỉ cấp nếu vẫn safe.

**Safe state**: Tồn tại ít nhất một **safe sequence** {P1, P2, ..., Pn} mà tất cả processes có thể hoàn thành. Một sequence là safe nếu với mỗi Pi, các resources mà Pi còn cần có thể được thỏa mãn bởi resources available + resources được held bởi tất cả Pj (j < i).

**Dữ liệu cần thiết** (n processes, m resource types):
- `Max[i][j]`: maximum demand của process i với resource type j
- `Allocation[i][j]`: resources đang được held
- `Need[i][j]` = `Max[i][j]` - `Allocation[i][j]`
- `Available[j]`: resources hiện có

**Safety Algorithm** (O(n² × m)):
```
Work = Available.copy()
Finish = [False] * n

while True:
    found = False
    for i in range(n):
        if not Finish[i] and Need[i] <= Work (element-wise):
            Work += Allocation[i]
            Finish[i] = True
            found = True
    if not found:
        break

Safe iff all Finish[i] == True
```

**Resource Request Algorithm**:
```
Process Pi requests Request_i:
1. If Request_i > Need[i]: error (exceeded maximum claim)
2. If Request_i > Available: wait (resources not available)
3. Simulate: Available -= Request_i; Allocation[i] += Request_i; Need[i] -= Request_i
4. Run Safety Algorithm:
   - Safe → grant request
   - Unsafe → rollback (revert step 3), Pi phải chờ
```

### Deadlock Detection

Dùng khi không dùng prevention/avoidance. OS định kỳ chạy detection algorithm.

**Wait-for Graph** (single instance): Project RAG — chỉ giữ processes. Edge Pi → Pj nếu Pi chờ resource được giữ bởi Pj. Cycle = deadlock.

**Detection Algorithm (multi-instance)**: Tương tự Banker's nhưng dùng `Allocation` thực tế và `Request` (pending requests):
```
Work = Available.copy()
Finish[i] = True if Allocation[i] == 0, else False

while True:
    find i: not Finish[i] and Request[i] <= Work
    if found:
        Work += Allocation[i]; Finish[i] = True
    else:
        break

Deadlocked = {Pi : Finish[i] == False}
```

### Deadlock Recovery

Khi phát hiện deadlock, có 2 approaches:

**1. Process Termination:**
- Abort tất cả deadlocked processes (đơn giản nhưng mất work)
- Abort từng process một theo thứ tự ưu tiên cho đến khi deadlock được giải:
  - Priority của process
  - Thời gian đã chạy và còn cần
  - Resources đang held
  - Số processes cần abort

**2. Resource Preemption:**
- Chọn victim (minimize cost)
- Rollback process victim về safe state (cần checkpoint)
- Cẩn thận starvation: cùng một process không nên luôn bị chọn làm victim

---

## Định nghĩa chính xác

**Deadlock**: Tình trạng một tập processes không thể tiếp tục thực thi vì mỗi process đang chờ một event (thường là release resource) mà chỉ có process khác trong tập đó mới tạo ra được.

**Safe State**: Trạng thái mà hệ thống có thể allocate resources cho mỗi process theo một thứ tự nào đó và tránh deadlock. Tồn tại ít nhất một safe sequence.

**Livelock**: Các processes liên tục thay đổi trạng thái để phản ứng lẫn nhau nhưng không tiến triển.

**Starvation**: Process chờ resource vô thời hạn vì luôn có processes khác có priority cao hơn được ưu tiên.

---

## Bảng so sánh / Sơ đồ kỹ thuật

### Deadlock vs Livelock vs Starvation

| Tiêu chí | Deadlock | Livelock | Starvation |
|---------|---------|---------|-----------|
| Trạng thái processes | Blocked, không chạy | Chạy nhưng không tiến triển | Chờ mãi |
| CPU Usage | Thấp (blocked) | Cao (busy looping) | Trung bình |
| Tiến triển | Không | Không | Không |
| Nguyên nhân | Circular wait | Mutual response | Priority |
| Phát hiện | RAG cycle / Detection algo | Monitoring progress | Priority tracking |
| Giải quyết | Termination / Preemption | Randomization, backoff | Aging |

### Deadlock Prevention vs Avoidance vs Detection

| | Prevention | Avoidance | Detection |
|---|-----------|-----------|-----------|
| Khi nào hoạt động | Design time | Runtime (trước khi cấp) | Runtime (sau khi xảy ra) |
| Overhead | Thấp (nhưng restrictive) | Cao (tính toán mỗi request) | Trung bình (định kỳ) |
| Utilization | Thấp | Trung bình | Cao |
| Cần biết trước | Max demand? Không cần | Có (Max, Available, Need) | Không cần |
| Ứng dụng | Coding conventions | Phân bổ resource critical | Database lock management |

### Resource Allocation Graph — Ví dụ Deadlock

```
      P1 ──────→ R1
      ↑           │
      │           ↓
      R2 ←────── P2

Đọc:
  P1 → R1: P1 đang chờ R1
  R1 → P2: R1 đang được giữ bởi P2
  P2 → R2: P2 đang chờ R2
  R2 → P1: R2 đang được giữ bởi P1

Cycle: P1 → R1 → P2 → R2 → P1 = DEADLOCK
```

### Banker's Algorithm — Ví dụ (3 resource types: A, B, C)

```
Processes: P0..P4
Max:           Alloc:          Need:           Available:
    A  B  C       A  B  C        A  B  C        A  B  C
P0: 7  5  3    P0:0  1  0    P0:7  4  3        3  3  2
P1: 3  2  2    P1:2  0  0    P1:1  2  2
P2: 9  0  2    P2:3  0  2    P2:6  0  0
P3: 2  2  2    P3:2  1  1    P3:0  1  1
P4: 4  3  3    P4:0  0  2    P4:4  3  1

Safety check:
Work = [3,3,2]
P1: Need=[1,2,2] ≤ [3,3,2] ✓ → Work=[3,3,2]+[2,0,0]=[5,3,2]; Finish[1]=T
P3: Need=[0,1,1] ≤ [5,3,2] ✓ → Work=[5,3,2]+[2,1,1]=[7,4,3]; Finish[3]=T
P4: Need=[4,3,1] ≤ [7,4,3] ✓ → Work=[7,4,3]+[0,0,2]=[7,4,5]; Finish[4]=T
P0: Need=[7,4,3] ≤ [7,4,5] ✓ → Work=[7,5,5]+[0,1,0]=[7,5,5]; Finish[0]=T
P2: Need=[6,0,0] ≤ [7,5,5] ✓ → Finish[2]=T

Safe sequence: <P1, P3, P4, P0, P2> → SAFE STATE
```

---

## Code mẫu

```python
import threading
import time
import random

# ============================================================
# 1. Banker's Algorithm
# ============================================================

def is_safe_state(available, allocation, need, n, m):
    """
    Kiểm tra safe state.
    Returns (is_safe, safe_sequence)
    """
    work = available[:]
    finish = [False] * n
    safe_sequence = []
    
    while True:
        found = False
        for i in range(n):
            if not finish[i]:
                # Kiểm tra Need[i] <= Work (element-wise)
                if all(need[i][j] <= work[j] for j in range(m)):
                    # Process i có thể hoàn thành
                    for j in range(m):
                        work[j] += allocation[i][j]
                    finish[i] = True
                    safe_sequence.append(i)
                    found = True
        
        if not found:
            break
    
    is_safe = all(finish)
    return is_safe, safe_sequence


def request_resources(process_id, request, available, allocation, need, max_demand, n, m):
    """
    Banker's Algorithm — Resource Request
    Returns True nếu request được chấp thuận
    """
    # Bước 1: Kiểm tra request không vượt need
    if any(request[j] > need[process_id][j] for j in range(m)):
        raise ValueError(f"P{process_id} exceeded its maximum claim!")
    
    # Bước 2: Kiểm tra resources available
    if any(request[j] > available[j] for j in range(m)):
        print(f"P{process_id}: Resources not available, must wait.")
        return False
    
    # Bước 3: Giả lập cấp phát
    available_copy = available[:]
    allocation_copy = [row[:] for row in allocation]
    need_copy = [row[:] for row in need]
    
    for j in range(m):
        available_copy[j] -= request[j]
        allocation_copy[process_id][j] += request[j]
        need_copy[process_id][j] -= request[j]
    
    # Bước 4: Kiểm tra safe state
    safe, sequence = is_safe_state(available_copy, allocation_copy, need_copy, n, m)
    
    if safe:
        # Commit thay đổi
        for j in range(m):
            available[j] = available_copy[j]
            allocation[process_id][j] = allocation_copy[process_id][j]
            need[process_id][j] = need_copy[process_id][j]
        print(f"P{process_id}: Request GRANTED. Safe sequence: {['P'+str(x) for x in sequence]}")
        return True
    else:
        print(f"P{process_id}: Request DENIED (would cause unsafe state).")
        return False


# Test Banker's Algorithm
print("=" * 60)
print("Banker's Algorithm Demo")
print("=" * 60)

n, m = 5, 3  # 5 processes, 3 resource types (A, B, C)

max_demand = [
    [7, 5, 3],  # P0
    [3, 2, 2],  # P1
    [9, 0, 2],  # P2
    [2, 2, 2],  # P3
    [4, 3, 3],  # P4
]
allocation = [
    [0, 1, 0],  # P0
    [2, 0, 0],  # P1
    [3, 0, 2],  # P2
    [2, 1, 1],  # P3
    [0, 0, 2],  # P4
]
available = [3, 3, 2]

# Tính Need matrix
need = [[max_demand[i][j] - allocation[i][j] for j in range(m)] for i in range(n)]

safe, seq = is_safe_state(available, allocation, need, n, m)
print(f"Initial state: {'SAFE' if safe else 'UNSAFE'}")
if safe:
    print(f"Safe sequence: {['P'+str(x) for x in seq]}")

# P1 request [1, 0, 2]
print("\nP1 requests [1, 0, 2]:")
request_resources(1, [1, 0, 2], available, allocation, need, max_demand, n, m)

# P4 request [3, 3, 0] — should be denied
print("\nP4 requests [3, 3, 0]:")
request_resources(4, [3, 3, 0], available, allocation, need, max_demand, n, m)


# ============================================================
# 2. Dining Philosophers Problem
# ============================================================

print("\n" + "=" * 60)
print("Dining Philosophers — Deadlock-free Solution (Resource Ordering)")
print("=" * 60)

N_PHILOSOPHERS = 5

class Philosopher(threading.Thread):
    def __init__(self, pid, left_fork, right_fork):
        super().__init__()
        self.pid = pid
        self.left_fork = left_fork
        self.right_fork = right_fork
        self.daemon = True
    
    def run(self):
        for _ in range(3):  # Mỗi triết gia ăn 3 lần
            self.think()
            self.eat()
    
    def think(self):
        time.sleep(random.uniform(0.01, 0.05))
    
    def eat(self):
        # RESOURCE ORDERING SOLUTION: philosopher N-1 lấy right fork trước
        # Tất cả philosopher khác lấy left fork trước
        # → Phá Circular Wait condition
        if self.pid == N_PHILOSOPHERS - 1:
            first, second = self.right_fork, self.left_fork
        else:
            first, second = self.left_fork, self.right_fork
        
        first.acquire()
        second.acquire()
        
        print(f"  Philosopher {self.pid} is eating")
        time.sleep(random.uniform(0.01, 0.03))
        
        second.release()
        first.release()


forks = [threading.Lock() for _ in range(N_PHILOSOPHERS)]
philosophers = [
    Philosopher(i, forks[i], forks[(i + 1) % N_PHILOSOPHERS])
    for i in range(N_PHILOSOPHERS)
]

for p in philosophers:
    p.start()
for p in philosophers:
    p.join(timeout=5)

print("All philosophers finished without deadlock!")


# ============================================================
# 3. Deadlock Detection — Wait-for Graph (đơn giản)
# ============================================================

def detect_deadlock_wait_for_graph(wait_for):
    """
    Phát hiện cycle trong wait-for graph bằng DFS.
    wait_for: dict {process: [processes it's waiting for]}
    Returns: (has_deadlock, deadlocked_processes)
    """
    WHITE, GRAY, BLACK = 0, 1, 2
    color = {p: WHITE for p in wait_for}
    deadlocked = set()
    
    def dfs(node, path):
        color[node] = GRAY
        path.add(node)
        for neighbor in wait_for.get(node, []):
            if neighbor not in color:
                color[neighbor] = WHITE
            if color[neighbor] == GRAY:
                # Cycle found — tất cả nodes trong path là deadlocked
                deadlocked.update(path)
                return True
            if color[neighbor] == WHITE:
                if dfs(neighbor, path):
                    return True
        color[node] = BLACK
        path.discard(node)
        return False
    
    for process in list(wait_for.keys()):
        if color[process] == WHITE:
            dfs(process, set())
    
    return len(deadlocked) > 0, deadlocked


# Test deadlock detection
print("\n" + "=" * 60)
print("Deadlock Detection — Wait-for Graph")
print("=" * 60)

# Scenario 1: Deadlock
wait_for_deadlock = {
    "P1": ["P2"],
    "P2": ["P3"],
    "P3": ["P1"],   # Cycle: P1 → P2 → P3 → P1
    "P4": ["P2"],
}
has_dl, dl_procs = detect_deadlock_wait_for_graph(wait_for_deadlock)
print(f"Scenario 1 (has cycle): deadlock={has_dl}, processes={dl_procs}")

# Scenario 2: No deadlock
wait_for_no_deadlock = {
    "P1": ["P2"],
    "P2": ["P3"],
    "P3": [],
}
has_dl, dl_procs = detect_deadlock_wait_for_graph(wait_for_no_deadlock)
print(f"Scenario 2 (no cycle): deadlock={has_dl}, processes={dl_procs}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Deadlock Prevention — Dùng khi:**
- Có thể impose global resource ordering (lock ordering trong code)
- Số lượng resources và patterns rõ ràng tại design time
- Ví dụ: Java synchronized blocks, database row locking order

**Deadlock Avoidance (Banker's) — Dùng khi:**
- Maximum resource needs của processes có thể khai báo trước
- Số processes và resources tương đối nhỏ và stable
- Ví dụ: một số RTOS (Real-Time OS), resource scheduling trong HPC

**Deadlock Detection — Dùng khi:**
- Deadlock xảy ra ít, overhead prevention/avoidance quá cao
- Có thể chấp nhận occasional recovery cost
- Ví dụ: Database systems (detect lock cycle, abort transaction)

**Không nên làm gì — Trường hợp thực tế:**
- Nhiều OS (kể cả Linux, Windows) không handle deadlock cho user processes — chỉ đảm bảo kernel không deadlock. User processes phải tự handle.

---

## Lỗi thường gặp (Common Pitfalls)

- **Cycle ≠ Deadlock với multi-instance resources**: Với RAG có multi-instance, cycle là cần thiết nhưng chưa đủ. Phải dùng detection algorithm.
- **Banker's cần Maximum demand trước**: Không thực tế trong nhiều trường hợp — processes không biết trước maximum họ cần.
- **Nhầm Deadlock với Livelock**: Deadlock → processes blocked, CPU idle. Livelock → processes chạy liên tục nhưng không tiến triển (CPU cao).
- **Lock ordering phải nhất quán toàn bộ codebase**: Chỉ cần một chỗ lock sai thứ tự là có thể deadlock.
- **Nested locks nguy hiểm**: Càng nhiều locks hold đồng thời, càng dễ deadlock. Minimize lock hold time.
- **Dining philosophers naive solution**: Nếu tất cả triết gia cùng lúc cầm fork trái → tất cả chờ fork phải mãi mãi.

---

## Câu hỏi phỏng vấn hay gặp

**Cơ bản:**
1. 4 điều kiện Coffman là gì? Phải có đủ cả 4 không?
2. Phân biệt deadlock, livelock, starvation bằng ví dụ thực tế.
3. Vẽ Resource Allocation Graph cho scenario deadlock đơn giản.

**Trung bình:**
4. Giải thích Banker's Algorithm. Safe state là gì?
5. Deadlock Prevention vs Detection vs Avoidance — khi nào dùng cái nào?
6. Trong code thực tế, cách nào phổ biến nhất để tránh deadlock?
7. Dining Philosophers — giải pháp resource ordering hoạt động thế nào?

**Nâng cao:**
8. Database systems xử lý deadlock thế nào? (Detection + transaction abort)
9. Priority inheritance giải quyết priority inversion (liên quan deadlock) thế nào?
10. Distributed deadlock detection khác single-system thế nào? (Chandy-Misra algorithm)
11. Nếu bạn review code và thấy 2 mutexes luôn được acquire cùng nhau, bạn kiểm tra gì?
12. Describe một deadlock bug bạn đã gặp (hoặc giả sử) và cách debug + fix.
