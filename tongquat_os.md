# Tổng quan OS — Hệ điều hành (Operating System)

---

## Roadmap học OS

### Bước 1 — Nền tảng
- **Kernel vs User space** — mode switching, system call
- **Process vs Thread** — PCB, TCB, context switch
- **Process states** — new, ready, running, waiting, terminated
- **CPU scheduling** — FCFS, SJF, Round Robin, Priority

### Bước 2 — Quản lý bộ nhớ
- **Virtual Memory** — page table, TLB, demand paging
- **Paging vs Segmentation** — internal vs external fragmentation
- **Page Replacement** — FIFO, LRU, Optimal
- **Thrashing** — nguyên nhân, cách ngăn

### Bước 3 — Đồng bộ & Deadlock
- **Race condition** — critical section, mutual exclusion
- **Synchronization** — Mutex, Semaphore, Monitor
- **Deadlock** — 4 điều kiện, prevention, avoidance (Banker's), detection
- **Deadlock vs Livelock vs Starvation**

### Bước 4 — File System & I/O
- **File system** — inode, directory, FAT, ext4, NTFS
- **I/O scheduling** — FCFS, SSTF, SCAN (Elevator), C-SCAN
- **Disk structure** — sector, track, cylinder, seek time
- **Buffer & Cache** — page cache, write-back vs write-through

---

## Bảng so sánh Process vs Thread

| | Process | Thread |
|-|---------|--------|
| Không gian bộ nhớ | Riêng biệt | Chia sẻ trong process |
| Context switch | Nặng (slow) | Nhẹ (fast) |
| Giao tiếp | IPC (pipe, socket, shm) | Shared memory trực tiếp |
| Crash ảnh hưởng | Isolated | Crash 1 thread → crash cả process |
| Tạo mới | `fork()` — tốn kém | `pthread_create()` — rẻ hơn |
| Ví dụ | Chrome: mỗi tab là process | Web server: mỗi request là thread |

---

## CPU Scheduling Algorithms

| Algorithm | Preemptive? | Starvation? | Overhead | Tốt khi |
|-----------|------------|-------------|----------|---------|
| FCFS | Không | Không | Thấp | Batch jobs |
| SJF | Không | Có (long jobs) | Trung bình | Biết burst time |
| SRTF | Có | Có | Cao | Minimize avg wait |
| Round Robin | Có | Không | Trung bình | Time-sharing |
| Priority | Cả hai | Có | Trung bình | Real-time |
| Multilevel Queue | Có | Có | Cao | Mixed workload |

---

## Deadlock — 4 điều kiện Coffman

Deadlock xảy ra khi **đồng thời** có đủ 4 điều kiện:

1. **Mutual Exclusion** — resource không chia sẻ được
2. **Hold and Wait** — giữ resource này, chờ resource khác
3. **No Preemption** — resource không thể bị lấy lại bắt buộc
4. **Circular Wait** — P1 chờ P2, P2 chờ P3, P3 chờ P1

**Phá vỡ deadlock:** Phá vỡ ít nhất 1 trong 4 điều kiện trên.

---

## Page Replacement — So sánh

| Algorithm | Hit Rate | Overhead | Belady's Anomaly? |
|-----------|----------|----------|-------------------|
| FIFO | Thấp | Thấp | Có (thêm frame → nhiều fault hơn) |
| LRU | Tốt | Trung bình | Không |
| Optimal (OPT) | Tốt nhất | Cao (cần biết tương lai) | Không |
| Clock (Second Chance) | Gần LRU | Thấp | Không |

---

## Mã giả — Hỏi output là gì?

### Bài 1 — Round Robin Scheduling

```
Processes: P1(burst=6), P2(burst=4), P3(burst=2)
Thứ tự arrive: P1, P2, P3 (cùng lúc t=0)
Time quantum = 2

Timeline:
[0-2]: P1 chạy (còn 4)
[2-4]: P2 chạy (còn 2)
[4-6]: P3 chạy (còn 0) → P3 DONE
[6-8]: P1 chạy (còn 2)
[8-10]: P2 chạy (còn 0) → P2 DONE
[10-12]: P1 chạy (còn 0) → P1 DONE

Hỏi:
a) Completion time của P1, P2, P3?
b) Turnaround time trung bình?
c) Waiting time trung bình?
```

> **Đáp án:**
> a) P1=12, P2=10, P3=6
> b) Turnaround: (12+10+6)/3 = **9.33**
> c) Waiting: P1=(12-6)=6, P2=(10-4)=6, P3=(6-2)=4 → avg = **(6+6+4)/3 = 5.33**

---

### Bài 2 — Deadlock Detection

```
Processes: P1, P2, P3
Resources: R1 (1 instance), R2 (1 instance)

Hiện tại:
- P1 giữ R1, đang chờ R2
- P2 giữ R2, đang chờ R1
- P3 không giữ gì, đang chờ R1

Hỏi:
a) Có deadlock không?
b) Process nào bị deadlock?
c) P3 có bị deadlock không?
```

> **Đáp án:**
> a) **Có deadlock** — P1 chờ P2, P2 chờ P1 → circular wait
> b) **P1 và P2** bị deadlock với nhau
> c) **P3 không bị deadlock** — P3 chờ R1, khi P1 hoặc P2 giải phóng (nếu deadlock bị phá) thì P3 chạy được. Nhưng vì P1/P2 deadlock → P3 bị **starvation** vô thời hạn.

---

### Bài 3 — Page Replacement (LRU)

```
Reference string: 7 0 1 2 0 3 0 4 2 3 0 3 2
Số frame: 3

Mô phỏng LRU:
Page | Frames      | Fault?
  7  | [7, -, -]   | YES
  0  | [7, 0, -]   | YES
  1  | [7, 0, 1]   | YES
  2  | [2, 0, 1]   | YES (replace 7 — LRU nhất)
  0  | [2, 0, 1]   | NO (0 đã có)
  3  | [2, 0, 3]   | YES (replace 1 — LRU nhất)
  0  | [2, 0, 3]   | NO
  4  | [4, 0, 3]   | YES (replace 2 — LRU nhất)
  2  | [4, 0, 2]   | YES (replace 3 — LRU nhất)
  3  | [4, 3, 2]   | YES (replace 0 — LRU nhất)
  0  | [0, 3, 2]   | YES (replace 4 — LRU nhất)
  3  | [0, 3, 2]   | NO
  2  | [0, 3, 2]   | NO

Hỏi: Tổng page fault là bao nhiêu?
```

> **Đáp án: 9 page faults**  
> **Giải thích:** LRU replace page không được dùng lâu nhất. Đây là thuật toán tốt trong thực tế (xấp xỉ Optimal).

---

### Bài 4 — Mutex vs Semaphore

```python
# Mutex: chỉ 1 thread vào critical section
mutex = Mutex()
shared_counter = 0

def increment():
    global shared_counter
    mutex.lock()
    shared_counter += 1    # critical section
    mutex.unlock()

# 3 threads cùng gọi increment()
# Hỏi: shared_counter cuối cùng là bao nhiêu?
```

> **Đáp án: 3**  
> **Giải thích:** Mutex đảm bảo mutual exclusion — chỉ 1 thread vào critical section tại một thời điểm → không có race condition → kết quả luôn là 3. Nếu không có mutex, có thể là 1, 2, hoặc 3 (tùy timing).

---

### Bài 5 — Fork Bomb (đọc hiểu)

```c
int main() {
    printf("A\n");
    fork();  // tạo child process
    printf("B\n");
    fork();  // mỗi process tạo thêm 1 child
    printf("C\n");
    return 0;
}

Hỏi: Tổng cộng có bao nhiêu lần in "A", "B", "C"?
```

> **Đáp án:** A=1, B=2, C=4
> ```
> Process gốc: A, B, C
> Sau fork() 1: có 2 process, mỗi cái in B
> Sau fork() 2: có 4 process, mỗi cái in C
> ```
> **Giải thích:** Mỗi `fork()` nhân đôi số processes. printf("A") chạy trước fork đầu → 1 lần. printf("B") chạy sau fork đầu (2 process) → 2 lần. printf("C") chạy sau fork thứ hai (4 process) → 4 lần.

---

## Bảng so sánh Mutex vs Semaphore vs Monitor

| | Mutex | Semaphore | Monitor |
|-|-------|-----------|---------|
| Giá trị | 0/1 (binary) | 0..N (counting) | — |
| Owner | Có (chỉ owner unlock) | Không | Có |
| Dùng cho | Mutual exclusion | Rate limiting, producer-consumer | High-level sync |
| Ví dụ | File write lock | DB connection pool | Java `synchronized` |

---

## Câu hỏi tự test nhanh

1. Sự khác nhau giữa Process và Thread? → Memory riêng vs chia sẻ
2. 4 điều kiện Deadlock là gì? → ME, H&W, NP, CW
3. Thrashing là gì? → CPU dành phần lớn thời gian swap thay vì chạy process
4. LRU vs FIFO: cái nào tốt hơn? → LRU (không có Belady's anomaly)
5. fork() trả về gì cho parent, child? → Parent: PID của child; Child: 0
