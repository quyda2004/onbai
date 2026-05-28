# Synchronization

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng hai nhân viên ngân hàng cùng lúc xử lý tài khoản của bạn có 1,000,000 đồng. Khách A gửi thêm 500,000, khách B rút 200,000.

Nếu không có kiểm soát: cả hai cùng đọc số dư = 1,000,000, rồi:
- Nhân viên A ghi 1,000,000 + 500,000 = 1,500,000
- Nhân viên B ghi 1,000,000 - 200,000 = 800,000

Tùy ai ghi sau, kết quả cuối là 1,500,000 hoặc 800,000 — đều sai! Kết quả đúng phải là 1,300,000.

Đây là **race condition** — khi kết quả phụ thuộc vào thứ tự thực thi không được kiểm soát.

**Mutex (khóa)** là chìa khóa phòng tài liệu chỉ có một bản sao. Chỉ ai có chìa mới được vào. Khi xong việc thì trả chìa lại để người khác dùng.

**Semaphore** giống như bãi đậu xe có giới hạn chỗ. Có đúng N vé cho N chỗ. Khi vào thì lấy 1 vé, khi ra thì trả lại. Khi hết vé thì chờ.

**Monitor** giống như phòng họp có quy tắc: chỉ một người được vào tại một thời điểm, và người vào có thể "ngủ" chờ một điều kiện cụ thể trong khi tự động nhường phòng cho người khác.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Race Condition: Ví dụ counter++ với 2 Threads

```python
# counter++ thực ra là 3 assembly instructions:
# LOAD  R1, counter   # Đọc counter vào register
# ADD   R1, 1         # Tăng
# STORE counter, R1   # Ghi lại

# Thread 1                    Thread 2
# LOAD  R1 = 0               ...
# ADD   R1 = 1               ...
# (context switch)           LOAD  R2 = 0   ← đọc giá trị cũ!
# ...                        ADD   R2 = 1
# STORE counter = 1          STORE counter = 1  ← ghi đè!
# Kết quả: counter = 1 (đúng ra phải là 2)
```

Đây là **lost update** — một trong các update bị mất.

### Critical Section Requirements

Bất kỳ giải pháp đúng nào cho critical section phải thỏa mãn 3 điều kiện:

1. **Mutual Exclusion**: Chỉ một process được ở trong critical section tại một thời điểm.
2. **Progress**: Nếu không có process nào trong CS và một số process muốn vào, chỉ các processes "không trong remainder section" mới được tham gia quyết định, và quyết định này không thể trì hoãn vô thời hạn.
3. **Bounded Waiting**: Phải tồn tại giới hạn số lần process khác được vào CS trước khi request của process đang chờ được chấp thuận (không starvation).

### Peterson's Solution (Software Solution, 2 Processes)

```python
# Shared variables:
flag = [False, False]  # flag[i] = True: process i muốn vào CS
turn = 0               # Đến lượt ai

def process(i):
    j = 1 - i  # Process còn lại
    while True:
        # Entry section
        flag[i] = True      # Tôi muốn vào
        turn = j            # Nhưng tôi nhường lượt cho j
        while flag[j] and turn == j:  # Chờ nếu j muốn vào VÀ đến lượt j
            pass
        
        # Critical Section
        # ... critical work ...
        
        # Exit section
        flag[i] = False     # Tôi không còn muốn vào nữa
        
        # Remainder section
```

Chứng minh đúng: ME (không thể cả hai vào CS), Progress, Bounded Waiting.
**Hạn chế**: Chỉ cho 2 processes; yêu cầu memory ordering (cần `volatile`/memory barrier trên modern CPUs do CPU reordering).

### Hardware Solutions

**Test-and-Set (TAS)**:
```c
// Atomic operation — không thể bị interrupt giữa chừng
bool test_and_set(bool *target) {
    bool rv = *target;
    *target = true;   // Set to true atomically
    return rv;
}

// Mutex implementation dùng TAS:
bool lock = false;
void acquire() {
    while (test_and_set(&lock));  // Spin until lock = false (old value)
}
void release() {
    lock = false;
}
```

**Compare-and-Swap (CAS)** — phổ biến hơn:
```c
// Atomic: chỉ swap nếu current value == expected
int compare_and_swap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected)
        *value = new_value;
    return temp;  // Trả về old value
}

// Lock-free counter increment:
void atomic_increment(int *counter) {
    int old, new;
    do {
        old = *counter;
        new = old + 1;
    } while (compare_and_swap(counter, old, new) != old);
    // Retry nếu someone else modified counter
}
```

CAS là nền tảng của **lock-free data structures** (Java `AtomicInteger`, C++ `std::atomic`).

### Mutex (Lock)

**Owner concept**: Mutex chỉ được release bởi thread đã acquire nó. Khác với semaphore (bất kỳ thread nào cũng có thể signal).

**Recursive (reentrant) mutex**: Cùng một thread có thể acquire nhiều lần mà không deadlock. Cần counter để biết đã acquire bao nhiêu lần.

```python
import threading

mutex = threading.Lock()
recursive_mutex = threading.RLock()  # Reentrant lock

# Non-reentrant: deadlock nếu cùng thread acquire hai lần
def bad_function():
    with mutex:
        with mutex:  # DEADLOCK trên threading.Lock()
            pass

# Reentrant: OK
def good_function():
    with recursive_mutex:
        with recursive_mutex:  # OK với RLock
            pass
```

### Semaphore: Counting vs Binary

**Semaphore** có hai atomic operations:
- **P() / wait() / acquire()**: Giảm value. Nếu value < 0 → block.
- **V() / signal() / release()**: Tăng value. Nếu có process đang chờ → wake one up.

```python
# Binary Semaphore (value = 0 hoặc 1): hoạt động như Mutex
# Nhưng khác: bất kỳ thread nào cũng có thể signal!

# Counting Semaphore: giới hạn số concurrent access
semaphore = threading.Semaphore(3)  # Cho phép tối đa 3 threads đồng thời

# Signaling pattern (không có trong Mutex):
# Thread A:          Thread B:
# ... do work ...    sem.acquire()  ← chờ A
# sem.release()      ... continue ...
```

**Producer-Consumer với Semaphore**:
```python
empty = threading.Semaphore(BUFFER_SIZE)  # Số ô trống
full = threading.Semaphore(0)              # Số ô có dữ liệu
mutex = threading.Lock()                  # Bảo vệ buffer

def producer():
    while True:
        item = produce()
        empty.acquire()   # Chờ có ô trống
        with mutex:
            buffer.append(item)
        full.release()    # Báo có item mới

def consumer():
    while True:
        full.acquire()    # Chờ có item
        with mutex:
            item = buffer.pop(0)
        empty.release()   # Giải phóng ô trống
        consume(item)
```

### Monitor: High-Level Synchronization

**Monitor** là high-level construct: một class có mutual exclusion built-in. Chỉ một thread được thực thi bên trong monitor tại một thời điểm.

**Condition Variables**:
- `wait()`: Release monitor lock, put thread vào wait queue. Khi woken up, reacquire lock.
- `signal()`: Wake up một thread đang wait trên condition (Hoare semantics: signal wakes và chạy ngay)
- `notify()` / `signal_all()`: Wake up tất cả (Mesa semantics: cần recheck condition sau khi woken)

**Mesa semantics** (Java, Python) — phổ biến trong practice:
```python
# Phải dùng while, không dùng if (spurious wakeup)
with condition:
    while not condition_met():  # WHILE, không phải IF
        condition.wait()
    # ... proceed ...
```

### Mutex vs Semaphore vs Monitor

| Tiêu chí | Mutex | Binary Semaphore | Counting Semaphore | Monitor |
|---------|-------|-----------------|-------------------|---------|
| Owner concept | Có (chỉ owner release) | Không | Không | Có (implicit) |
| Signaling | Không | Có | Có | Có (condition var) |
| Initial value | Unlocked | 0 hoặc 1 | N | Unlocked |
| Deadlock nếu double acquire | Có | Có | Có thể | Không (recursive) |
| Abstraction level | Low | Low | Low | High |
| Language support | pthread_mutex, threading.Lock | sem_wait/post | Semaphore(N) | synchronized (Java) |
| Use case | Mutual exclusion | Signaling/sync | Resource counting | Complex sync |

### Classical Problems

#### Producer-Consumer (Bounded Buffer)
Đã trình bày ở trên với semaphore. Key insight: cần 3 primitives: empty semaphore, full semaphore, mutex.

#### Readers-Writers Problem

- **Writers** cần exclusive access (không thể đọc và ghi đồng thời)
- **Readers** có thể concurrent với nhau

```python
read_count = 0
read_mutex = threading.Lock()    # Bảo vệ read_count
write_mutex = threading.Lock()   # Exclusive write access

def reader():
    with read_mutex:
        read_count += 1
        if read_count == 1:      # First reader locks out writers
            write_mutex.acquire()
    
    # === Read data (critical section) ===
    
    with read_mutex:
        read_count -= 1
        if read_count == 0:      # Last reader releases
            write_mutex.release()

def writer():
    with write_mutex:            # Exclusive access
        # === Write data ===
```

**Vấn đề**: Writer starvation — nếu readers liên tục arrive, writer chờ mãi.
**Giải pháp**: Khi writer đang chờ, không cho readers mới vào (priority to writers).

#### Dining Philosophers (5 triết gia, 5 dĩa)

**Naive deadlock**: Tất cả cầm dĩa trái → tất cả chờ dĩa phải mãi mãi.

**Solutions**:
1. **Resource ordering**: Triết gia số 4 lấy dĩa phải trước (phá circular wait)
2. **Asymmetric**: Odd triết gia lấy left trước, even lấy right trước
3. **Arbitrator (Waiter)**: Mutex tổng thể — triết gia phải xin phép waiter cả hai dĩa
4. **Chandy/Misra**: Dùng message passing

### Spinlock vs Blocking Lock

| | Spinlock | Blocking Lock (Mutex) |
|--|---------|----------------------|
| Cơ chế | Busy-wait (CPU spinning) | Block thread (put to sleep) |
| CPU usage khi chờ | Cao (100%) | Thấp (context switch) |
| Context switch | Không | Có |
| Overhead | Thấp nếu lock held ngắn | Cao (context switch ~microseconds) |
| Dùng khi | Lock held rất ngắn, multicore | Lock held lâu, single core |
| Ví dụ | Kernel interrupt handlers | User-space mutex |

**Rule of thumb**: Nếu lock hold time < context switch time (~1-10μs) → spinlock. Ngược lại → blocking lock.

**Adaptive mutex** (Solaris, Linux futex): Spin một vài lần rồi block nếu holder đang chạy trên CPU khác.

### Priority Inversion: Mars Pathfinder Bug

**Scenario**:
- Task High (H): priority cao, cần resource R
- Task Medium (M): priority trung bình, không cần R
- Task Low (L): priority thấp, đang giữ R

Khi H cần R → H bị block chờ L release. Nhưng M preempts L (vì M > L). Kết quả: H phải chờ M xong, M xong mới L chạy tiếp, L release R, H mới chạy được.

**H bị block bởi M qua L — mặc dù H có priority cao hơn M!**

Đây là **priority inversion**. Mars Pathfinder 1997 bị reset nhiều lần vì bug này.

**Priority Inheritance**: Khi H chờ R mà L đang giữ, tạm thời tăng priority của L lên bằng H. Sau khi L release R → priority L trả về bình thường.

**Priority Ceiling**: Mỗi resource R có ceiling priority = max priority của tất cả tasks có thể acquire R. Khi acquire R, task được nâng priority lên ceiling.

---

## Định nghĩa chính xác

**Race Condition**: Situation where the outcome of a program depends on the relative timing or interleaving of multiple threads/processes accessing shared data.

**Critical Section**: Đoạn code truy cập shared resources (shared memory, files, devices) mà chỉ một process/thread được thực thi tại một thời điểm.

**Mutex (Mutual Exclusion Lock)**: Synchronization primitive cho phép chỉ một thread sở hữu lock tại một thời điểm; chỉ owner mới có thể release.

**Semaphore**: Biến nguyên không âm với hai atomic operations P (wait/decrement) và V (signal/increment), dùng để kiểm soát access vào shared resources.

**Monitor**: High-level synchronization construct gồm shared data, procedures operating on that data, và synchronization mechanism (condition variables) — với implicit mutual exclusion.

**Priority Inversion**: Tình trạng high-priority task bị block gián tiếp bởi low-priority task do medium-priority task preempting low-priority task đang giữ shared resource.

---

## Bảng so sánh / Sơ đồ kỹ thuật

### Semaphore Operations — State Diagram

```
Semaphore(value=1):

Thread A calls P():          Thread B calls P():
value = 1 → 0 (OK, proceed) value = 0 → -1 (BLOCK B)

Thread A calls V():
value = -1 → 0 (wake up B)
                             Thread B resumes
```

### Producer-Consumer — Semaphore State

```
Buffer size = 3, initially empty:

empty = 3 (slots available)
full  = 0 (items available)

Producer produces item:   empty.P() → 2; add to buffer; full.V() → 1
Consumer consumes item:   full.P()  → 0; remove from buffer; empty.V() → 3
```

### Readers-Writers — Interaction

```
State: 0 readers, 0 writers
  Reader1 enters: read_count=1, acquire write_mutex
  Reader2 enters: read_count=2 (no mutex needed)
  Writer1 tries:  write_mutex → BLOCKED (readers active)
  Reader1 exits:  read_count=1
  Reader2 exits:  read_count=0, release write_mutex
  Writer1:        Acquires write_mutex, writes
```

---

## Code mẫu

```python
import threading
import time
import random
from collections import deque

# ============================================================
# 1. Race Condition Demo
# ============================================================

print("=" * 60)
print("1. Race Condition Demo")
print("=" * 60)

counter = 0
N_ITERATIONS = 100000

def increment_unsafe():
    global counter
    for _ in range(N_ITERATIONS):
        counter += 1  # Non-atomic! (read-modify-write)

# Chạy không an toàn
counter = 0
t1 = threading.Thread(target=increment_unsafe)
t2 = threading.Thread(target=increment_unsafe)
t1.start(); t2.start()
t1.join(); t2.join()
print(f"Unsafe counter:  expected={2*N_ITERATIONS}, got={counter} ({'WRONG' if counter != 2*N_ITERATIONS else 'OK'})")

# Chạy an toàn với Lock
counter = 0
lock = threading.Lock()

def increment_safe():
    global counter
    for _ in range(N_ITERATIONS):
        with lock:
            counter += 1

t1 = threading.Thread(target=increment_safe)
t2 = threading.Thread(target=increment_safe)
t1.start(); t2.start()
t1.join(); t2.join()
print(f"Safe counter:    expected={2*N_ITERATIONS}, got={counter} ({'OK' if counter == 2*N_ITERATIONS else 'WRONG'})")


# ============================================================
# 2. Producer-Consumer với Semaphore
# ============================================================

print("\n" + "=" * 60)
print("2. Producer-Consumer với Semaphore")
print("=" * 60)

BUFFER_SIZE = 5
buffer = deque()
buffer_mutex = threading.Lock()
empty_slots = threading.Semaphore(BUFFER_SIZE)  # Ban đầu: 5 slots trống
full_slots = threading.Semaphore(0)              # Ban đầu: 0 items

def producer(pid, n_items):
    for i in range(n_items):
        item = f"P{pid}-item{i}"
        time.sleep(random.uniform(0.01, 0.05))
        
        empty_slots.acquire()       # Chờ có slot trống
        with buffer_mutex:
            buffer.append(item)
            print(f"  [Producer {pid}] Produced {item}, buffer size={len(buffer)}")
        full_slots.release()        # Báo có item mới

def consumer(cid, n_items):
    for _ in range(n_items):
        full_slots.acquire()        # Chờ có item
        with buffer_mutex:
            item = buffer.popleft()
            print(f"  [Consumer {cid}] Consumed {item}, buffer size={len(buffer)}")
        empty_slots.release()       # Giải phóng slot
        time.sleep(random.uniform(0.02, 0.06))

threads = [
    threading.Thread(target=producer, args=(1, 5)),
    threading.Thread(target=producer, args=(2, 5)),
    threading.Thread(target=consumer, args=(1, 5)),
    threading.Thread(target=consumer, args=(2, 5)),
]
for t in threads:
    t.start()
for t in threads:
    t.join()
print("Producer-Consumer completed without deadlock!")


# ============================================================
# 3. Readers-Writers với Condition Variable
# ============================================================

print("\n" + "=" * 60)
print("3. Readers-Writers Problem")
print("=" * 60)

class ReadWriteLock:
    """Readers-Writers Lock: nhiều readers, exclusive writer"""
    def __init__(self):
        self._read_count = 0
        self._condition = threading.Condition(threading.Lock())
        self._writing = False
    
    def acquire_read(self):
        with self._condition:
            while self._writing:
                self._condition.wait()  # Chờ writer xong
            self._read_count += 1
    
    def release_read(self):
        with self._condition:
            self._read_count -= 1
            if self._read_count == 0:
                self._condition.notify_all()  # Wake up waiting writers
    
    def acquire_write(self):
        with self._condition:
            while self._writing or self._read_count > 0:
                self._condition.wait()  # Chờ tất cả readers VÀ writers xong
            self._writing = True
    
    def release_write(self):
        with self._condition:
            self._writing = False
            self._condition.notify_all()  # Wake up readers và writers

rw_lock = ReadWriteLock()
shared_data = 0

def reader_task(rid):
    rw_lock.acquire_read()
    print(f"  [Reader {rid}] Reading: shared_data = {shared_data}")
    time.sleep(0.02)
    rw_lock.release_read()

def writer_task(wid, value):
    global shared_data
    rw_lock.acquire_write()
    print(f"  [Writer {wid}] Writing: {shared_data} → {value}")
    shared_data = value
    time.sleep(0.03)
    rw_lock.release_write()

threads = []
for i in range(3):
    threads.append(threading.Thread(target=reader_task, args=(i,)))
threads.append(threading.Thread(target=writer_task, args=(1, 42)))
for i in range(3, 6):
    threads.append(threading.Thread(target=reader_task, args=(i,)))
threads.append(threading.Thread(target=writer_task, args=(2, 99)))

for t in threads:
    t.start()
for t in threads:
    t.join()
print("Readers-Writers completed!")


# ============================================================
# 4. threading.Lock, threading.Semaphore API Reference
# ============================================================

print("\n" + "=" * 60)
print("4. Python threading API Summary")
print("=" * 60)

# threading.Lock — Mutex
lock = threading.Lock()
# lock.acquire()          # Block until available
# lock.release()          # Release (can be called by any thread in Python!)
# with lock:              # Context manager (preferred)

# threading.RLock — Reentrant Mutex
rlock = threading.RLock()
# Same thread can acquire multiple times without deadlock

# threading.Semaphore — Counting Semaphore
sem = threading.Semaphore(3)   # Initial value = 3
# sem.acquire()           # P() — decrement, block if 0
# sem.release()           # V() — increment, wake one

# threading.Event — Simple signaling
event = threading.Event()
# event.set()             # Set flag, wake all waiters
# event.wait()            # Block until set
# event.clear()           # Reset flag
# event.is_set()          # Check

# threading.Condition — Condition Variable
cond = threading.Condition(lock=None)
# with cond:
#     cond.wait()         # Release lock, wait for notify
#     cond.notify()       # Wake one waiter
#     cond.notify_all()   # Wake all waiters

print("Python threading primitives: Lock, RLock, Semaphore, Event, Condition")
print("Use 'with' context manager for all lock types — ensures release on exception!")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Mutex — Dùng khi:**
- Bảo vệ shared data (counter, list, dict) khỏi concurrent modification
- Cần owner semantics (chỉ người acquire mới release)
- Ví dụ: bảo vệ global cache, connection pool

**Semaphore — Dùng khi:**
- Giới hạn concurrent access (database connection pool, rate limiter)
- Signaling giữa threads (producer-consumer)
- Không cần owner semantics

**Counting Semaphore (value > 1) — Dùng khi:**
- Resource pool: `Semaphore(N)` cho phép N threads concurrent access
- Thread pool, connection pool

**Condition Variable — Dùng khi:**
- Thread cần chờ một condition phức tạp (không chỉ lock availability)
- Producer-consumer, bounded buffer
- Luôn dùng với while loop, không dùng if (spurious wakeup)

**Spinlock — Dùng khi:**
- Lock hold time cực ngắn (< vài microseconds)
- Kernel code, interrupt handlers
- Không thể sleep (interrupt context)

**Spinlock — Không dùng khi:**
- Lock hold time dài → CPU waste
- Single-core system → deadlock (spinning thread block owner từ chạy)
- User space (OS đã handle tốt hơn)

**Lock-free / CAS — Dùng khi:**
- Performance critical, high contention counters
- Java `AtomicLong`, C++ `std::atomic`
- Reference counting (shared_ptr)

---

## Lỗi thường gặp (Common Pitfalls)

- **Sử dụng if thay vì while với condition variable**: Spurious wakeup có thể xảy ra (OS wake thread mà không có signal thực sự). Luôn dùng `while (condition_not_met) { wait(); }`.
- **Lock granularity quá coarse**: Một lock to cho toàn bộ data structure → bottleneck. Dùng fine-grained locking (per-bucket lock trong hash map).
- **Lock granularity quá fine**: Nhiều locks → overhead acquire/release cao, và dễ deadlock khi cần acquire nhiều locks.
- **Forgetting to release lock**: Dùng RAII (C++) hoặc `with` statement (Python) để đảm bảo release dù có exception.
- **Python GIL nhầm lẫn**: GIL (Global Interpreter Lock) trong CPython bảo vệ interpreter state nhưng KHÔNG đảm bảo thread safety cho code Python bạn viết. Vẫn cần Lock cho shared data.
- **Double-checked locking unsafe**: Pattern `if (!init) { lock(); if (!init) { ... } }` không an toàn nếu không có memory barrier đúng cách.
- **Deadlock từ nested locks**: Luôn acquire theo thứ tự nhất quán. Document lock ordering.
- **Priority inversion**: Khi mix priorities, cân nhắc priority inheritance trong mutex.
- **Signal vs Broadcast**: `notify()` chỉ wake một thread — nếu condition có thể thỏa mãn nhiều threads, cần `notify_all()`. Dùng nhầm có thể gây starvation.

---

## Câu hỏi phỏng vấn hay gặp

**Cơ bản:**
1. Race condition là gì? Cho ví dụ counter++ với 2 threads.
2. 3 yêu cầu của critical section solution là gì?
3. Phân biệt Mutex và Semaphore.
4. Tại sao cần dùng `while` thay vì `if` với condition variable?

**Trung bình:**
5. Giải thích Producer-Consumer problem và giải pháp dùng semaphore.
6. Spinlock vs Mutex — khi nào dùng cái nào?
7. Readers-Writers problem: readers exclusive với writers hay readers concurrent?
8. Tại sao CAS (compare-and-swap) quan trọng cho lock-free programming?

**Nâng cao:**
9. Priority inversion là gì? Mars Pathfinder bug xảy ra thế nào? Priority inheritance giải quyết thế nào?
10. Giải thích tại sao Peterson's solution cần memory barrier trên modern CPUs.
11. ABA problem trong lock-free programming là gì?
12. False sharing là gì? Tại sao nó ảnh hưởng performance của concurrent code trên multi-core?
13. Giải thích `java.util.concurrent.locks.ReentrantReadWriteLock` implementation strategy.
14. Describe một race condition bug bạn đã debug — làm thế nào phát hiện và fix?
