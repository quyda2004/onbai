# Process và Thread

---

## Giải thích cho người mới hoàn toàn

> Hãy tưởng tượng bạn đang làm việc ở một nhà hàng.
>
> **Process** giống như một **đầu bếp** được thuê để nấu một món ăn. Đầu bếp đó có:
> - Không gian bếp riêng (bộ nhớ riêng)
> - Bộ dao thớt riêng (tài nguyên riêng)
> - Công thức nấu ăn của riêng mình (code)
>
> **Thread** giống như **các trợ lý** của đầu bếp đó. Họ:
> - Cùng làm việc trong một gian bếp (chia sẻ bộ nhớ với process cha)
> - Dùng chung nguyên liệu (chia sẻ tài nguyên)
> - Nhưng mỗi người có tay và thao tác riêng (có stack và register riêng)
>
> Nếu một nhà hàng có nhiều đầu bếp (multi-process), họ không can thiệp vào bếp của nhau.  
> Nếu một đầu bếp có nhiều trợ lý (multi-thread), họ làm việc nhanh hơn nhưng phải phối hợp để không đụng chạm nhau.
>
> **Context switch** giống như quản lý nhà hàng "tạm dừng" một đầu bếp và cho đầu bếp khác lên làm — cần thời gian ghi nhớ lại đang nấu đến bước nào.

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Process** là một instance đang chạy của chương trình, được OS cấp phát không gian địa chỉ ảo (virtual address space) riêng biệt. Mỗi process có:
- **Code segment** (text): chứa machine code
- **Data segment**: global/static variables
- **Heap**: dynamic allocation (malloc/new)
- **Stack**: local variables, function call frames
- **File descriptors**, **signal handlers**, **environment variables**

**Thread** là đơn vị thực thi nhẹ hơn (lightweight) bên trong process. Các thread trong cùng process chia sẻ:
- Virtual address space (heap, code, data, file descriptors)
- Nhưng mỗi thread có **stack riêng**, **program counter riêng**, **register set riêng**

**Context Switch Overhead:**
- Process-level: cần flush TLB, thay đổi page table base register (CR3 trên x86), tốn ~1000–10000 cycles
- Thread-level: chỉ cần lưu/khôi phục register set và stack pointer, tốn ~100–1000 cycles

**IPC (Inter-Process Communication):**
| Phương thức | Tốc độ | Phạm vi | Ghi chú |
|---|---|---|---|
| Pipe (anonymous) | Nhanh | Cùng host, parent-child | Unidirectional, kernel buffer |
| Named Pipe (FIFO) | Nhanh | Cùng host, bất kỳ process | Bidirectional nếu dùng 2 pipe |
| Shared Memory | Rất nhanh | Cùng host | Cần synchronization (mutex/semaphore) |
| Message Queue | Trung bình | Cùng host | Structured messages, kernel-managed |
| Socket | Chậm hơn | Network hoặc localhost | Linh hoạt nhất, cross-host |
| Signal | Rất nhanh | Cùng host | Chỉ truyền số hiệu, không có data |

**Race Condition** xảy ra khi 2+ thread/process cùng truy cập và modify shared data mà không có synchronization. Kết quả phụ thuộc vào thứ tự thực thi (scheduling) — không deterministic.

```
Thread 1: x = x + 1  →  read x=5, compute 6, (bị interrupt)
Thread 2: x = x + 1  →  read x=5, compute 6, write x=6
Thread 1: (resume)   →  write x=6  ← Sai! Phải là x=7
```

---

## Định nghĩa chính xác

- **Process**: Một chương trình đang trong quá trình thực thi, được OS cung cấp virtual address space độc lập, tài nguyên hệ thống và ít nhất một thread thực thi.
- **Thread** (Thread of Execution): Đơn vị lập lịch (scheduling unit) nhỏ nhất trong OS, chia sẻ address space với các thread trong cùng process nhưng có stack và register riêng.
- **PCB (Process Control Block)**: Cấu trúc dữ liệu kernel lưu trạng thái của một process (PID, trạng thái, priority, page table pointer, file descriptor table, ...).
- **TCB (Thread Control Block)**: Cấu trúc dữ liệu lưu trạng thái của một thread (Thread ID, program counter, register values, stack pointer, state).
- **Context Switch**: Hành động OS lưu trạng thái (context) của process/thread hiện tại và khôi phục trạng thái của process/thread tiếp theo.

---

## Độ phức tạp / Bảng so sánh kỹ thuật

### Process States (5-state model)

```
        fork()              schedule()
New ──────────► Ready ◄────────────── Running
                  │                      │
                  │   schedule()         │  I/O request / wait event
                  └──────────────────────┘
                                         │
                                         ▼
                                      Waiting ──► Ready (khi event xảy ra)
                                         
Running ──► Terminated (exit() / error)
```

| Trạng thái | Ý nghĩa |
|---|---|
| New | Process vừa được tạo, chưa vào ready queue |
| Ready | Sẵn sàng chạy, đang đợi CPU |
| Running | Đang được CPU thực thi |
| Waiting (Blocked) | Đang đợi I/O, event, hoặc signal |
| Terminated | Đã kết thúc, OS đang dọn tài nguyên |

### So sánh Process vs Thread

| Tiêu chí | Process | Thread |
|---|---|---|
| Không gian bộ nhớ | Riêng biệt (isolated) | Chia sẻ với process cha |
| Tạo mới (fork/create) | Chậm (~ms) | Nhanh (~μs) |
| Context switch | Chậm (flush TLB, CR3) | Nhanh (chỉ registers/stack) |
| Communication | IPC (pipe, socket, shm) | Shared memory trực tiếp |
| Crash isolation | Crash không ảnh hưởng process khác | Crash thread có thể kill cả process |
| Overhead bộ nhớ | Cao (copy address space với fork) | Thấp |
| Use case | Web server (Apache prefork), Chrome tabs | Web server (Nginx worker), game engine |

### Context Switch Cost

| Loại | Thời gian điển hình | Nguyên nhân chi phí |
|---|---|---|
| Thread switch (same process) | ~1–5 μs | Save/restore registers, stack pointer |
| Process switch | ~5–50 μs | + TLB flush, page table switch |
| Process switch (cross NUMA) | ~50–500 μs | + cache misses do NUMA topology |

---

## Pseudocode / Code mẫu

```python
import multiprocessing
import threading
import os
import time

# === DEMO PROCESS ===
def process_worker(name):
    """Mỗi process có PID riêng và không chia sẻ biến"""
    print(f"Process {name}: PID={os.getpid()}, PPID={os.getppid()}")
    time.sleep(1)

# === DEMO THREAD ===
shared_counter = 0  # Biến dùng chung giữa các thread

def thread_worker_unsafe(n):
    """KHÔNG an toàn - race condition!"""
    global shared_counter
    for _ in range(n):
        # Read-Modify-Write không atomic → race condition
        shared_counter += 1

def thread_worker_safe(n, lock):
    """An toàn - dùng lock để protect critical section"""
    global shared_counter
    for _ in range(n):
        with lock:  # Chỉ một thread tại một thời điểm
            shared_counter += 1

if __name__ == "__main__":
    # --- Test Process ---
    print("=== MULTI-PROCESS ===")
    processes = []
    for i in range(3):
        p = multiprocessing.Process(target=process_worker, args=(f"P{i}",))
        processes.append(p)
        p.start()
    for p in processes:
        p.join()

    # --- Test Thread (unsafe) ---
    print("\n=== MULTI-THREAD (UNSAFE - Race Condition) ===")
    shared_counter = 0
    threads = [threading.Thread(target=thread_worker_unsafe, args=(10000,)) for _ in range(5)]
    for t in threads: t.start()
    for t in threads: t.join()
    print(f"Expected: 50000, Got: {shared_counter}")  # Thường < 50000

    # --- Test Thread (safe) ---
    print("\n=== MULTI-THREAD (SAFE - With Lock) ===")
    shared_counter = 0
    lock = threading.Lock()
    threads = [threading.Thread(target=thread_worker_safe, args=(10000, lock)) for _ in range(5)]
    for t in threads: t.start()
    for t in threads: t.join()
    print(f"Expected: 50000, Got: {shared_counter}")  # Luôn = 50000
```

```python
# === IPC: Pipe demo ===
import multiprocessing

def sender(conn):
    conn.send(["hello", 42, True])
    conn.close()

def receiver(conn):
    data = conn.recv()
    print(f"Received: {data}")

if __name__ == "__main__":
    parent_conn, child_conn = multiprocessing.Pipe()
    p1 = multiprocessing.Process(target=sender, args=(child_conn,))
    p2 = multiprocessing.Process(target=receiver, args=(parent_conn,))
    p1.start(); p2.start()
    p1.join(); p2.join()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng Process khi:**
- Cần isolation mạnh (crash một component không ảnh hưởng toàn bộ hệ thống)
- Chạy code không tin tưởng (sandboxing)
- Cần tận dụng nhiều CPU core mà không bị GIL (Python)
- Ví dụ: Chrome tạo process riêng cho mỗi tab để tránh một tab crash kill browser

**Dùng Thread khi:**
- Cần latency thấp và communication nhiều giữa các task
- Chia sẻ nhiều dữ liệu lớn (tránh copy)
- I/O-bound tasks (web scraping, file reading) trong ngôn ngữ có native threads
- Ví dụ: Web server xử lý nhiều request đồng thời, GUI app tách UI thread và worker thread

**KHÔNG dùng Thread khi:**
- Code không thread-safe và khó thêm lock
- Python CPU-bound tasks (dùng Process thay vì Thread do GIL)
- Khi cần true isolation (security boundary)

**KHÔNG dùng Process khi:**
- Cần chia sẻ nhiều state phức tạp (overhead IPC quá lớn)
- Resource-constrained systems (mỗi process tốn RAM cho address space riêng)

---

## So sánh với các khái niệm liên quan

| | Process | Thread | Coroutine (async) | Fiber (Green Thread) |
|---|---|---|---|---|
| Managed by | OS kernel | OS kernel | Language runtime | Language runtime |
| Preemptible | Có | Có | Không (cooperative) | Không (cooperative) |
| Stack | Riêng, lớn | Riêng, nhỏ hơn | Có thể rất nhỏ | Rất nhỏ |
| Context switch | Kernel mode | Kernel mode | User mode (rất nhanh) | User mode |
| Parallelism | True (multi-CPU) | True (multi-CPU) | Concurrency only | Concurrency only |
| Ví dụ | subprocess.Popen | threading.Thread | asyncio coroutine | greenlet, gevent |

---

## Lỗi thường gặp (Common Pitfalls)

1. **Nhầm concurrency vs parallelism**: Concurrency là nhiều task tiến hành đan xen (có thể trên 1 CPU). Parallelism là chạy thật sự đồng thời trên nhiều CPU.

2. **Python GIL gotcha**: Dùng `threading` cho CPU-bound task trong Python không mang lại speedup vì GIL chỉ cho 1 thread Python chạy tại 1 thời điểm. Dùng `multiprocessing` hoặc C extension.

3. **Zombie process**: Process con kết thúc nhưng process cha chưa gọi `wait()` để đọc exit status → process con trở thành "zombie" (còn entry trong process table dù đã chết).

4. **Orphan process**: Process cha kết thúc trước process con → process con trở thành orphan, được `init` (PID 1) adopt.

5. **Thread-safety của data structures**: Nhiều data structure (dict, list trong CPython) có GIL protection nhưng compound operations (read-then-write) vẫn không atomic.

6. **Stack overflow trong threads**: Thread stack mặc định thường 1–8 MB. Deep recursion hoặc many threads có thể gây out of memory.

7. **Fork-after-thread deadlock**: Nếu fork() sau khi tạo threads, child process chỉ có 1 thread nhưng có thể giữ locks từ parent → deadlock trong child.

---

## Câu hỏi phỏng vấn hay gặp

1. **Sự khác biệt giữa process và thread?** → Tập trung vào address space, communication, isolation, overhead.

2. **Context switch tốn kém vì điều gì?** → TLB flush, page table switch, CPU cache invalidation, kernel/user mode transition.

3. **Race condition là gì? Cho ví dụ.** → Tả ví dụ counter++, giải thích tại sao không atomic ở assembly level (LOAD, ADD, STORE).

4. **Zombie process vs Orphan process?** → Zombie: con chết nhưng cha chưa wait(). Orphan: cha chết trước con.

5. **IPC nào nhanh nhất? Khi nào dùng?** → Shared memory nhanh nhất nhưng cần sync. Socket linh hoạt nhất cho distributed systems.

6. **Tại sao Chrome dùng multi-process thay vì multi-thread?** → Isolation: một tab crash không kill browser, sandbox security.

7. **fork() trong Unix hoạt động như thế nào?** → Copy-on-write (COW): không copy toàn bộ memory ngay, chỉ copy page khi có write.

8. **Thread-safe có nghĩa là gì?** → Function/data structure có thể được gọi từ nhiều thread đồng thời mà kết quả vẫn đúng.
