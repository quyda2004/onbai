# Memory Management

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn có một căn phòng làm việc (RAM) với diện tích giới hạn. Nhưng bạn có rất nhiều đồ đạc (chương trình) cần dùng. Làm sao để quản lý?

**Virtual Memory** giống như việc bạn có một kho chứa (ổ đĩa cứng) bên ngoài. Khi bàn làm việc (RAM) đầy, bạn cất bớt những thứ tạm thời không dùng ra kho, và lấy ra khi cần. Ai ngồi ở bàn (chạy chương trình) cứ tưởng mình có cả căn phòng rộng lớn, không biết rằng một số đồ đang ở trong kho.

**Paging** giống như chia bàn làm việc thành các ngăn nhỏ đều nhau (page frames). Đồ đạc của bạn cũng được gấp lại thành các gói đều nhau (pages). Mỗi khi cần một gói, bạn đặt vào bất kỳ ngăn trống nào — không cần liên tiếp nhau.

**Page Fault** xảy ra khi bạn cần dùng một món đồ nhưng nó đang ở trong kho, chưa được đưa vào phòng. Bạn phải dừng lại, chạy ra kho lấy về — chậm hơn rất nhiều so với lấy trực tiếp từ bàn.

**Thrashing** là khi bạn cứ mải miết chạy ra kho lấy đồ rồi lại cất vào, không làm được việc gì thực sự — hệ thống bận toàn bộ thời gian với việc swap.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Virtual Memory và Address Space

Mỗi process có một **virtual address space** riêng biệt (thường 4GB trên 32-bit, 128TB trên 64-bit Linux). Kernel duy trì **page table** để map virtual address → physical address.

Lợi ích chính:
- **Isolation**: process A không thể đọc memory của process B
- **Abstraction**: programmer không cần quan tâm physical memory layout
- **Overcommit**: tổng virtual memory có thể lớn hơn RAM vật lý

### Paging Chi Tiết

- **Page size**: thường 4KB (x86), có thể 2MB/1GB (huge pages)
- **Page Table Entry (PTE)**: chứa physical frame number + flags (Present, Read/Write, User/Kernel, Dirty, Accessed)
- **Multi-level page table**: tránh lãng phí bộ nhớ (x86-64 dùng 4-level: PML4 → PDPT → PD → PT)

```
Virtual Address (64-bit):
[PML4 idx 9bit][PDPT idx 9bit][PD idx 9bit][PT idx 9bit][Offset 12bit]
```

### TLB (Translation Lookaside Buffer)

TLB là cache phần cứng lưu các mapping virtual→physical gần đây nhất.
- **TLB hit**: địa chỉ vật lý tra ngay trong TLB (~1 cycle)
- **TLB miss**: phải walk page table (~100 cycles)
- **TLB flush**: xảy ra khi context switch (trừ khi dùng ASID — Address Space ID)
- Locality of reference làm TLB hit rate thường >99%

### Page Fault

| Loại | Nguyên nhân | Chi phí |
|------|-------------|---------|
| **Minor fault** | Page chưa được map vào page table nhưng đã có trong memory (shared library, CoW) | Rất thấp — chỉ cập nhật PTE |
| **Major fault** | Page phải load từ disk (swap area hoặc file) | Cao — disk I/O (~ms) |

**Demand paging**: OS không load tất cả pages khi khởi động process — chỉ load khi cần (lazy loading). Giảm startup time nhưng có thể gây major fault lúc đầu.

### Segmentation vs Paging

| Tiêu chí | Segmentation | Paging |
|----------|-------------|--------|
| Đơn vị | Segment kích thước thay đổi | Page kích thước cố định |
| Fragmentation | External fragmentation | Internal fragmentation |
| Protection | Dễ set per-segment | Per-page nhưng cần nhiều entries |
| Sharing | Dễ share segment (code) | Phức tạp hơn |
| Hiện đại | x86 dùng flat model (vestigial) | Được dùng phổ biến |

### Internal vs External Fragmentation

- **Internal fragmentation**: cấp phát 4KB cho request 100B → lãng phí 3996B bên trong block
- **External fragmentation**: có đủ tổng free memory nhưng không có block liên tiếp đủ lớn

### Memory Allocation Algorithms

Áp dụng cho dynamic memory allocation (heap, malloc):

| Thuật toán | Cơ chế | Ưu điểm | Nhược điểm |
|-----------|--------|---------|-----------|
| **First Fit** | Lấy block trống đầu tiên đủ lớn | Nhanh | Fragmentation ở đầu list |
| **Best Fit** | Lấy block nhỏ nhất đủ chứa | Ít lãng phí | Chậm, nhiều tiny fragments |
| **Worst Fit** | Lấy block lớn nhất | Để lại fragment lớn nhất | Chậm, phá vỡ block lớn |
| **Next Fit** | Tiếp tục từ vị trí lần trước | Phân phối đều | Giống First Fit nhưng phân tán hơn |

Trong thực tế: `glibc malloc` dùng **segregated free lists** + **slab allocator** cho kernel objects.

### Page Replacement Algorithms

Khi physical memory đầy, cần chọn page nào để evict:

| Thuật toán | Cơ chế | Hiệu quả | Ghi chú |
|-----------|--------|---------|---------|
| **FIFO** | Evict page cũ nhất (vào trước) | Kém | Belady's anomaly: thêm frames có thể tăng fault |
| **Optimal (OPT)** | Evict page sẽ không dùng lâu nhất | Tốt nhất lý thuyết | Không thể implement (cần biết tương lai) |
| **LRU** | Evict page ít dùng gần đây nhất | Tốt | Tốn chi phí tracking (counter/stack) |
| **Clock (Second Chance)** | Circular list, dùng reference bit | Gần LRU, thực tế | Linux dùng biến thể này |

**LRU xấp xỉ trong thực tế** (vì true LRU tốn kém):
- **Aging**: shift reference bit định kỳ
- **NFU (Not Frequently Used)**: đếm số lần access
- Linux dùng **two-list LRU**: active list + inactive list

### Thrashing

**Nguyên nhân**: Degree of multiprogramming quá cao → mỗi process không có đủ frames → page fault rate tăng vọt → CPU utilization giảm (CPU chờ I/O) → OS nghĩ cần thêm process → vòng lặp xấu.

**Working Set Model**: Mỗi process cần một "working set" W(t, Δ) — tập pages được access trong khoảng thời gian Δ gần nhất. Chỉ chạy process khi có đủ frames cho working set của nó.

### Stack vs Heap trong Process Memory Layout

```
High address
+------------------+
|   Kernel space   |  (không accessible từ user space)
+------------------+
|      Stack       |  grows downward ↓ (local vars, return addr, stack frame)
|        ↓         |
|    (gap)         |
|        ↑         |
|      Heap        |  grows upward ↑ (malloc, new)
+------------------+
|  BSS segment     |  (uninitialized globals, zero-filled)
+------------------+
|  Data segment    |  (initialized globals, static vars)
+------------------+
|  Text segment    |  (code, read-only)
+------------------+
Low address
```

### Copy-on-Write (CoW) sau fork()

Khi `fork()` được gọi:
1. Child process chia sẻ **cùng physical pages** với parent (PTE của cả hai đều trỏ đến cùng frame, marked read-only)
2. Khi một trong hai **write** vào page đó → hardware trap → kernel copy page đó → cập nhật PTE của process ghi
3. Chỉ copy page thực sự bị modify → tiết kiệm memory, tăng tốc fork

`exec()` sau `fork()` sẽ discard tất cả pages ngay → CoW rất hiệu quả cho fork+exec pattern.

### Buffer Overflow — Stack Smashing

```
Stack frame của function f():
+------------------+
| Local buffer[64] | ← attacker ghi quá 64 bytes
+------------------+
| Saved EBP        |
+------------------+
| Return Address   | ← bị ghi đè → redirect execution
+------------------+
```

Defenses: Stack canary, ASLR (Address Space Layout Randomization), NX bit (non-executable stack), PIE (Position Independent Executable).

---

## Định nghĩa chính xác

**Virtual Memory**: Kỹ thuật quản lý bộ nhớ tách biệt logical address space khỏi physical address space, cho phép process sử dụng nhiều bộ nhớ hơn RAM vật lý bằng cách sử dụng disk làm backing store.

**Paging**: Phương pháp quản lý bộ nhớ chia physical memory thành các frame có kích thước cố định và virtual memory thành các page cùng kích thước, ánh xạ qua page table.

**Page Fault**: Exception xảy ra khi process truy cập virtual page không có trong physical memory, yêu cầu OS load page từ disk.

**Thrashing**: Tình trạng system dành nhiều thời gian swap hơn là thực thi, xảy ra khi tổng working set của các processes vượt quá available physical memory.

---

## Bảng so sánh / Sơ đồ kỹ thuật

### Page Replacement Algorithms — So sánh

| Thuật toán | Belady's Anomaly | Overhead | Độ chính xác | Dùng trong thực tế |
|-----------|-----------------|---------|------------|-------------------|
| FIFO | Có | Thấp | Kém | Ít |
| Optimal | Không | N/A | Tốt nhất | Không (benchmark) |
| LRU | Không | Cao | Tốt | Xấp xỉ (Clock) |
| Clock | Không | Thấp | Khá tốt | Linux, nhiều OS |

### Sơ đồ địa chỉ Virtual → Physical (2-level page table)

```
Virtual Address: [Page Dir idx | Page Table idx | Offset]
                      10 bits       10 bits       12 bits

CR3 register → Page Directory
                    ↓ [idx]
               Page Directory Entry → Page Table
                                          ↓ [idx]
                                     Page Table Entry → Physical Frame
                                                             ↓ + offset
                                                        Physical Address
```

### Memory Allocation — Ví dụ

```
Free list: [100B] [500B] [200B] [300B] [600B]
Request: 212B

First Fit  → [500B] block (đầu tiên đủ lớn)
Best Fit   → [300B] block (nhỏ nhất đủ chứa)
Worst Fit  → [600B] block (lớn nhất)
```

---

## Code mẫu

```python
import os
import sys

# ============================================================
# Mô phỏng Page Replacement Algorithms
# ============================================================

from collections import OrderedDict, deque

def simulate_fifo(pages, num_frames):
    """FIFO Page Replacement"""
    frames = deque()
    frame_set = set()
    page_faults = 0
    
    print(f"FIFO với {num_frames} frames:")
    for page in pages:
        if page not in frame_set:
            page_faults += 1
            if len(frames) == num_frames:
                # Evict page cũ nhất (đầu queue)
                evicted = frames.popleft()
                frame_set.remove(evicted)
            frames.append(page)
            frame_set.add(page)
            print(f"  Page {page}: FAULT  → frames = {list(frames)}")
        else:
            print(f"  Page {page}: HIT    → frames = {list(frames)}")
    
    print(f"  Tổng page faults: {page_faults}\n")
    return page_faults


def simulate_lru(pages, num_frames):
    """LRU Page Replacement dùng OrderedDict"""
    # OrderedDict: key = page, value = None; most-recently-used ở cuối
    cache = OrderedDict()
    page_faults = 0
    
    print(f"LRU với {num_frames} frames:")
    for page in pages:
        if page in cache:
            # Move to end (most recently used)
            cache.move_to_end(page)
            print(f"  Page {page}: HIT    → frames = {list(cache.keys())}")
        else:
            page_faults += 1
            if len(cache) == num_frames:
                # Evict least recently used (đầu OrderedDict)
                evicted, _ = cache.popitem(last=False)
                print(f"  Page {page}: FAULT  (evict {evicted}) → ", end="")
            else:
                print(f"  Page {page}: FAULT  → ", end="")
            cache[page] = None
            print(f"frames = {list(cache.keys())}")
    
    print(f"  Tổng page faults: {page_faults}\n")
    return page_faults


def simulate_optimal(pages, num_frames):
    """Optimal Page Replacement (Belady's Algorithm)"""
    frames = []
    page_faults = 0
    
    print(f"Optimal với {num_frames} frames:")
    for i, page in enumerate(pages):
        if page not in frames:
            page_faults += 1
            if len(frames) == num_frames:
                # Tìm page sẽ không được dùng lâu nhất trong tương lai
                future_use = {}
                for f in frames:
                    try:
                        future_use[f] = pages[i+1:].index(f)
                    except ValueError:
                        future_use[f] = float('inf')  # Không dùng nữa → evict ngay
                evict = max(future_use, key=future_use.get)
                frames.remove(evict)
                print(f"  Page {page}: FAULT  (evict {evict}) → ", end="")
            else:
                print(f"  Page {page}: FAULT  → ", end="")
            frames.append(page)
            print(f"frames = {frames}")
        else:
            print(f"  Page {page}: HIT    → frames = {frames}")
    
    print(f"  Tổng page faults: {page_faults}\n")
    return page_faults


# Test với reference string cổ điển
reference_string = [7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2]
num_frames = 3

print("=" * 60)
print(f"Reference string: {reference_string}")
print(f"Number of frames: {num_frames}")
print("=" * 60 + "\n")

fifo_faults = simulate_fifo(reference_string, num_frames)
lru_faults = simulate_lru(reference_string, num_frames)
opt_faults = simulate_optimal(reference_string, num_frames)

print(f"Tổng kết: FIFO={fifo_faults}, LRU={lru_faults}, Optimal={opt_faults}")


# ============================================================
# Demo: Virtual Address Translation (giả lập đơn giản)
# ============================================================

def translate_virtual_address(virtual_addr, page_size, page_table):
    """
    Chuyển virtual address → physical address
    page_table: dict {page_number: frame_number}
    """
    page_number = virtual_addr // page_size
    offset = virtual_addr % page_size
    
    if page_number not in page_table:
        raise Exception(f"Page Fault! Page {page_number} không có trong memory")
    
    frame_number = page_table[page_number]
    physical_addr = frame_number * page_size + offset
    return physical_addr

# Ví dụ: page size = 1KB = 1024 bytes
page_size = 1024
page_table = {0: 5, 1: 2, 2: 8, 3: 1}  # virtual page → physical frame

print("\n" + "=" * 60)
print("Demo Address Translation:")
for vaddr in [0, 1500, 3000, 4500]:
    try:
        paddr = translate_virtual_address(vaddr, page_size, page_table)
        vpage = vaddr // page_size
        offset = vaddr % page_size
        print(f"  VA={vaddr} (page {vpage}, offset {offset}) → PA={paddr}")
    except Exception as e:
        print(f"  VA={vaddr} → {e}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Virtual Memory — Dùng khi:**
- Chạy nhiều processes đồng thời (mọi OS hiện đại đều dùng)
- Process cần address space lớn hơn RAM vật lý
- Cần isolation giữa các processes
- Muốn lazy-load shared libraries

**Virtual Memory — Không dùng khi:**
- Real-time systems yêu cầu latency deterministic (page fault không dự đoán được)
- Embedded systems với memory tĩnh, không có MMU (microcontrollers)

**Huge Pages — Dùng khi:**
- Database servers (PostgreSQL, MySQL) cần large buffer pools
- JVM heap lớn (giảm TLB miss)
- High-performance computing

**LRU — Dùng khi:**
- Cache cần evict theo temporal locality (web cache, CPU cache)
- Khi chi phí miss cao (disk I/O)

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm Internal và External Fragmentation**: Internal = lãng phí bên trong block đã cấp phát; External = không còn block liên tiếp đủ lớn dù tổng free space đủ.
- **Nhầm Minor và Major Page Fault**: Minor fault không cần disk I/O (page đã ở memory nhưng chưa mapped); Major fault cần load từ disk.
- **Quên Belady's Anomaly cho FIFO**: Tăng số frames có thể tăng page faults với FIFO (counter-intuitive). LRU và Optimal không có anomaly này.
- **Thrashing vs High CPU Usage**: Thrashing là CPU utilization thấp vì CPU chờ I/O, không phải CPU utilization cao.
- **Stack overflow vs Heap overflow**: Stack overflow = vượt quá stack size limit (deep recursion); Heap overflow = ghi vượt qua buffer được malloc trên heap.
- **Nhầm CoW timing**: CoW xảy ra khi write, không phải khi fork. Sau fork() cả hai processes đọc cùng physical page.
- **Page size trade-off**: Page lớn → ít TLB entries cần, ít page table overhead, nhưng internal fragmentation nhiều hơn.

---

## Câu hỏi phỏng vấn hay gặp

**Cơ bản:**
1. Virtual memory là gì? Tại sao cần?
2. Phân biệt paging và segmentation.
3. TLB là gì? Điều gì xảy ra khi TLB miss?
4. Minor page fault và major page fault khác nhau thế nào?

**Trung bình:**
5. Giải thích Belady's Anomaly với FIFO. LRU có bị không?
6. Tại sao LRU tốt hơn FIFO? Cách implement LRU hiệu quả O(1)?
7. Thrashing là gì? Làm sao OS phát hiện và xử lý?
8. Copy-on-write hoạt động như thế nào sau fork()?
9. First Fit vs Best Fit: cái nào dùng trong practice?

**Nâng cao:**
10. Giải thích multi-level page table. Tại sao cần? 4-level trên x86-64 là gì?
11. Inverted page table là gì? Ưu/nhược điểm?
12. Huge pages giúp gì cho database performance?
13. ASLR là gì? Tại sao quan trọng cho security?
14. Describe một scenario dẫn đến thrashing và cách fix bằng working set model.
15. Kernel memory allocation (kmalloc/slab allocator) khác gì user-space malloc?
