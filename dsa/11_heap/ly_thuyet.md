# Heap / Priority Queue — Lý Thuyết Toàn Diện

---

## 1. Giải Thích Cho Người Mới Hoàn Toàn

Hãy tưởng tượng bạn đang quản lý một phòng cấp cứu bệnh viện. Bệnh nhân vào liên tục nhưng bác sĩ không xử lý theo thứ tự đến trước — họ luôn ưu tiên người nguy kịch nhất. Đây chính xác là cách **Priority Queue (Hàng đợi ưu tiên)** hoạt động.

- Bạn thêm bệnh nhân vào (insert) — mỗi người có mức độ nguy kịch (priority)
- Khi cần xử lý, bạn luôn lấy ra người **nguy kịch nhất** (extract-min hoặc extract-max)
- Người còn lại tự động sắp xếp lại để người nguy kịch tiếp theo luôn ở "đầu hàng"

**Heap** là cấu trúc dữ liệu cụ thể để implement Priority Queue một cách hiệu quả — như một cái cây nhị phân được lưu trong mảng, luôn đảm bảo phần tử nhỏ nhất (hoặc lớn nhất) nằm ở gốc.

**Ví dụ cuộc sống thực:**
- Hệ điều hành lên lịch các tiến trình theo priority
- Thuật toán Dijkstra tìm đường đi ngắn nhất
- Bộ nén Huffman encoding
- Lọc top-K kết quả trong search engine

---

## 2. Giải Thích Nâng Cao Cho Người Chuyên Ngành

### Heap Property và Cấu Trúc Bên Trong

**Heap** là một **complete binary tree** (cây nhị phân đầy đủ) thỏa mãn **heap property**:
- **Min-heap:** Mỗi node ≤ cả hai con → gốc luôn là phần tử nhỏ nhất
- **Max-heap:** Mỗi node ≥ cả hai con → gốc luôn là phần tử lớn nhất

"Complete binary tree" nghĩa là: tất cả các level đều đầy đủ **ngoại trừ level cuối**, và level cuối được lấp từ trái sang phải. Tính chất này cho phép lưu heap trong mảng mà **không cần con trỏ**.

### Array Representation

Với node tại index `i` (0-indexed):
```
parent(i)     = (i - 1) // 2
left_child(i) = 2 * i + 1
right_child(i)= 2 * i + 2
```

Ví dụ min-heap `[1, 3, 2, 7, 5, 9, 4]`:
```
        1          (index 0)
       / \
      3   2        (index 1, 2)
     / \ / \
    7  5 9  4      (index 3, 4, 5, 6)
```

**Tại sao array tốt hơn con trỏ?**
- Cache locality: các phần tử liên tiếp trong bộ nhớ → ít cache miss
- Không tốn overhead cho pointer (8 bytes/con trỏ × 2 = 16 bytes/node)
- Index arithmetic nhanh hơn pointer dereference

### Memory Model

Heap được lưu trong **heap memory** (vùng nhớ động) dưới dạng một mảng liên tiếp. Khi mảng đầy, cần **resize** (thường nhân đôi) — đây là O(n) amortized operation nhưng O(n) worst case cho một lần insert cụ thể.

### Trade-offs

| Đặc điểm | Heap | Sorted Array | BST |
|---|---|---|---|
| Insert | O(log n) | O(n) | O(log n) avg |
| Get-min | O(1) | O(1) | O(log n) |
| Extract-min | O(log n) | O(n) shift | O(log n) |
| Search arbitrary | O(n) | O(log n) | O(log n) avg |
| Memory | Array contiguous | Array contiguous | Pointer overhead |

Heap **không** hỗ trợ search O(log n) — nếu cần cả insert nhanh **và** search nhanh, dùng BST hoặc Hash + Heap kết hợp.

---

## 3. Định Nghĩa Chính Xác

**Heap** là cấu trúc dữ liệu dạng cây nhị phân thỏa mãn hai tính chất:
1. **Shape property:** Là một complete binary tree
2. **Heap property:** Mỗi node thỏa mãn quan hệ thứ tự với các con của nó (min hoặc max)

**Priority Queue** là Abstract Data Type (ADT) với các operations:
- `insert(item, priority)` — thêm phần tử
- `extract_min()` / `extract_max()` — lấy và xóa phần tử có priority cao nhất
- `peek()` — xem phần tử có priority cao nhất mà không xóa
- `decrease_key(item, new_priority)` — giảm priority (dùng trong Dijkstra)

---

## 4. Bảng Độ Phức Tạp Đầy Đủ

### 4.1 Các Thao Tác Cơ Bản

| Thao tác | Best Case | Average Case | Worst Case | Điều kiện |
|---|---|---|---|---|
| **Insert** | O(1) | O(log n) | O(log n) | Best: phần tử lớn hơn parent → không cần sift up |
| **Extract-min/max** | O(log n) | O(log n) | O(log n) | Luôn phải sift down từ gốc |
| **Peek (get-min)** | O(1) | O(1) | O(1) | Luôn là heap[0] |
| **Decrease Key** | O(1) | O(log n) | O(log n) | Best: key mới vẫn ≥ parent |
| **Delete arbitrary** | O(log n) | O(log n) | O(log n) | Cần decrease_key + extract |
| **Heapify-up** | O(1) | O(log n) | O(log n) | Best: đã đúng vị trí |
| **Heapify-down** | O(1) | O(log n) | O(log n) | Best: đã đúng vị trí |
| **Search** | O(1) | O(n) | O(n) | Không có ordering giữa các nhánh |

### 4.2 Build Heap

| Thao tác | Best Case | Average Case | Worst Case | Space |
|---|---|---|---|---|
| **Build heap (heapify)** | O(n) | O(n) | O(n) | O(1) auxiliary |
| **Build heap (n inserts)** | O(n) | O(n log n) | O(n log n) | O(1) auxiliary |

### 4.3 Heap Sort

| Thao tác | Best Case | Average Case | Worst Case | Space | Stable? |
|---|---|---|---|---|---|
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) auxiliary | No |

### 4.4 Space Complexity

| Cấu trúc | Space |
|---|---|
| Heap (array) | O(n) total |
| Auxiliary space (iterative heapify) | O(1) |
| Auxiliary space (recursive heapify) | O(log n) call stack |
| Heap Sort (in-place) | O(1) auxiliary, O(n) total |

---

## 5. Tại Sao Build Heap là O(n) Chứ Không Phải O(n log n)?

Đây là một trong những kết quả counterintuitive nhất trong DSA.

**Phân tích:**
Khi gọi `heapify_down` trên từng node từ `n//2 - 1` xuống `0`, công việc của mỗi node phụ thuộc vào **chiều cao** của nó trong cây.

Trong cây nhị phân đầy đủ với `n` nodes:
- Có `n/2` node ở level cuối → chiều cao 0 → mỗi node O(1)
- Có `n/4` node ở level kế cuối → chiều cao 1 → mỗi node O(1×log trong heapify)
- Có `n/8` node ở level kế kế cuối → chiều cao 2
- ...
- Có `1` node ở gốc → chiều cao h = log n

**Tổng công việc:**
```
T(n) = Σ (số node ở chiều cao h) × O(h)
     = Σ_{h=0}^{log n} (n / 2^{h+1}) × h
     = n × Σ_{h=0}^{log n} h / 2^{h+1}
     = n × Σ_{h=0}^{∞} h / 2^h      (bound bằng series vô hạn)
     = n × 2                          (geometric series: Σ h×x^h = x/(1-x)² với x=1/2)
     = O(n)
```

Hầu hết công việc xảy ra ở các node gần **lá** (chiều cao thấp, nhiều node), không phải ở gốc.

---

## 6. Heapify Up vs Heapify Down

### Heapify Up (Sift Up) — dùng khi INSERT

Khi insert một phần tử mới:
1. Thêm vào cuối mảng (vị trí `n`)
2. So sánh với parent `(n-1)//2`
3. Nếu vi phạm heap property → swap
4. Lặp lại từ vị trí mới cho đến khi đúng hoặc đến gốc

```python
def _sift_up(self, i):
    while i > 0:
        parent = (i - 1) // 2
        if self.heap[i] < self.heap[parent]:  # min-heap
            self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
            i = parent
        else:
            break
```

### Heapify Down (Sift Down) — dùng khi EXTRACT-MIN hoặc BUILD HEAP

Khi extract min:
1. Lưu gốc (min)
2. Đưa phần tử cuối lên gốc
3. Xóa phần tử cuối
4. Sift down từ gốc: so sánh với 2 con, swap với con nhỏ hơn nếu cần
5. Lặp đến khi đúng vị trí hoặc là lá

```python
def _sift_down(self, i, n):
    while True:
        left = 2 * i + 1
        right = 2 * i + 2
        smallest = i
        if left < n and self.heap[left] < self.heap[smallest]:
            smallest = left
        if right < n and self.heap[right] < self.heap[smallest]:
            smallest = right
        if smallest == i:
            break
        self.heap[i], self.heap[smallest] = self.heap[smallest], self.heap[i]
        i = smallest
```

**Quy tắc nhớ:**
- **Insert** → phần tử mới vào cuối → sift **UP** (đi lên tìm vị trí đúng)
- **Extract** → đưa cuối lên gốc → sift **DOWN** (đi xuống tìm vị trí đúng)
- **Build heap** → sift **DOWN** cho tất cả non-leaf nodes (vì không có gì "phía trên")

---

## 7. Code Mẫu Python

```python
import heapq
from typing import List, Optional

# ============================================================
# PHẦN 1: Min-Heap tự implement từ đầu
# ============================================================

class MinHeap:
    def __init__(self):
        self.heap: List[int] = []
    
    def push(self, val: int) -> None:
        """Thêm phần tử vào heap — O(log n)"""
        self.heap.append(val)
        self._sift_up(len(self.heap) - 1)
    
    def pop(self) -> int:
        """Lấy và xóa phần tử nhỏ nhất — O(log n)"""
        if not self.heap:
            raise IndexError("Heap is empty")
        
        # Swap gốc với phần tử cuối
        self.heap[0], self.heap[-1] = self.heap[-1], self.heap[0]
        min_val = self.heap.pop()  # Xóa phần tử cuối (min cũ)
        
        if self.heap:
            self._sift_down(0, len(self.heap))
        return min_val
    
    def peek(self) -> int:
        """Xem phần tử nhỏ nhất mà không xóa — O(1)"""
        if not self.heap:
            raise IndexError("Heap is empty")
        return self.heap[0]
    
    def _sift_up(self, i: int) -> None:
        """Đẩy phần tử tại index i lên đúng vị trí"""
        while i > 0:
            parent = (i - 1) // 2
            if self.heap[i] < self.heap[parent]:
                self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
                i = parent
            else:
                break  # Đã đúng vị trí, dừng sớm
    
    def _sift_down(self, i: int, n: int) -> None:
        """Đẩy phần tử tại index i xuống đúng vị trí trong heap kích thước n"""
        while True:
            left = 2 * i + 1
            right = 2 * i + 2
            smallest = i
            
            if left < n and self.heap[left] < self.heap[smallest]:
                smallest = left
            if right < n and self.heap[right] < self.heap[smallest]:
                smallest = right
            
            if smallest == i:
                break  # Không cần swap nữa
            
            self.heap[i], self.heap[smallest] = self.heap[smallest], self.heap[i]
            i = smallest
    
    def build_heap(self, arr: List[int]) -> None:
        """Build heap từ mảng có sẵn — O(n)"""
        self.heap = arr[:]  # Copy để không modify input
        n = len(self.heap)
        
        # Bắt đầu từ node cuối không phải lá: index (n//2 - 1)
        # Các node từ n//2 đến n-1 đều là lá, không cần sift down
        for i in range(n // 2 - 1, -1, -1):
            self._sift_down(i, n)
    
    def __len__(self):
        return len(self.heap)
    
    def __repr__(self):
        return f"MinHeap({self.heap})"


# ============================================================
# PHẦN 2: Heap Sort
# ============================================================

def heap_sort(arr: List[int]) -> List[int]:
    """
    Heap Sort — O(n log n) worst case, O(1) auxiliary space, NOT stable
    
    Ý tưởng: Build max-heap, sau đó liên tục extract max và đặt vào cuối
    """
    result = arr[:]
    n = len(result)
    
    def sift_down(i: int, heap_size: int) -> None:
        """Max-heap sift down"""
        while True:
            left = 2 * i + 1
            right = 2 * i + 2
            largest = i
            
            if left < heap_size and result[left] > result[largest]:
                largest = left
            if right < heap_size and result[right] > result[largest]:
                largest = right
            
            if largest == i:
                break
            result[i], result[largest] = result[largest], result[i]
            i = largest
    
    # Bước 1: Build max-heap — O(n)
    for i in range(n // 2 - 1, -1, -1):
        sift_down(i, n)
    
    # Bước 2: Extract max lần lượt — O(n log n)
    for i in range(n - 1, 0, -1):
        result[0], result[i] = result[i], result[0]  # Max về cuối
        sift_down(0, i)  # Heap size giảm đi 1
    
    return result


# ============================================================
# PHẦN 3: Python heapq — min-heap only
# ============================================================

def demo_heapq():
    """Python heapq là min-heap. Để dùng max-heap, negate các giá trị."""
    
    # --- Min-heap ---
    min_heap = []
    for val in [5, 3, 8, 1, 9, 2]:
        heapq.heappush(min_heap, val)
    
    print("Min-heap:", min_heap)          # [1, 3, 2, 5, 9, 8]
    print("Pop min:", heapq.heappop(min_heap))  # 1
    
    # --- Max-heap: negate giá trị ---
    max_heap = []
    for val in [5, 3, 8, 1, 9, 2]:
        heapq.heappush(max_heap, -val)    # Lưu âm
    
    print("Max:", -heapq.heappop(max_heap))  # 9 (âm của -9)
    
    # --- Build heap từ list có sẵn — O(n) ---
    data = [5, 3, 8, 1, 9, 2]
    heapq.heapify(data)  # In-place, O(n)
    print("After heapify:", data)
    
    # --- heappushpop: push rồi pop — hiệu quả hơn push + pop riêng ---
    result = heapq.heappushpop(data, 0)  # Push 0, pop min
    print("heappushpop result:", result)
    
    # --- heapreplace: pop rồi push — heap phải không rỗng ---
    result = heapq.heapreplace(data, 100)
    print("heapreplace result:", result)


# ============================================================
# PHẦN 4: Ứng dụng — K-th Largest Element
# ============================================================

def find_kth_largest(nums: List[int], k: int) -> int:
    """
    Tìm phần tử lớn thứ k — O(n log k) time, O(k) space
    
    Ý tưởng: Dùng min-heap kích thước k
    - Duyệt qua từng số
    - Nếu heap chưa đầy k phần tử: push vào
    - Nếu số hiện tại > min của heap (heap[0]): pop min, push số mới
    - Sau khi duyệt xong: heap[0] là k-th largest
    """
    min_heap = []
    
    for num in nums:
        if len(min_heap) < k:
            heapq.heappush(min_heap, num)
        elif num > min_heap[0]:
            heapq.heapreplace(min_heap, num)  # Efficient: pop+push
    
    return min_heap[0]

# Test
print(find_kth_largest([3, 2, 1, 5, 6, 4], 2))  # 5


# ============================================================
# PHẦN 5: Ứng dụng — Merge K Sorted Lists
# ============================================================

def merge_k_sorted_lists(lists: List[List[int]]) -> List[int]:
    """
    Merge K sorted lists — O(n log k) time, O(k) space
    n = tổng số phần tử, k = số lists
    
    Ý tưởng: Min-heap lưu (giá trị, list_index, element_index)
    - Luôn pop phần tử nhỏ nhất từ tất cả các danh sách
    """
    result = []
    # (value, list_index, element_index)
    heap = []
    
    # Khởi tạo heap với phần tử đầu tiên của mỗi list
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))
    
    while heap:
        val, list_idx, elem_idx = heapq.heappop(heap)
        result.append(val)
        
        # Thêm phần tử tiếp theo từ cùng list vào heap
        next_idx = elem_idx + 1
        if next_idx < len(lists[list_idx]):
            heapq.heappush(heap, (lists[list_idx][next_idx], list_idx, next_idx))
    
    return result

# Test
print(merge_k_sorted_lists([[1, 4, 7], [2, 5, 8], [3, 6, 9]]))
# [1, 2, 3, 4, 5, 6, 7, 8, 9]


# ============================================================
# PHẦN 6: Lazy Deletion Pattern
# ============================================================

class LazyHeap:
    """
    Lazy Deletion: Không xóa ngay, đánh dấu "deleted" và bỏ qua khi pop.
    
    Dùng khi: Cần xóa phần tử tùy ý hiệu quả mà không cần decrease_key.
    Trade-off: Heap có thể chứa "ghost" elements, nhưng các thao tác vẫn đúng.
    """
    
    def __init__(self):
        self.heap = []           # (priority, value)
        self.deleted = set()     # Set các value cần xóa
        self.counter = 0         # Tiebreaker để tránh so sánh value phức tạp
    
    def push(self, value, priority: int) -> None:
        self.counter += 1
        heapq.heappush(self.heap, (priority, self.counter, value))
    
    def delete(self, value) -> None:
        """Đánh dấu lazy delete — O(1)"""
        self.deleted.add(value)
    
    def pop(self):
        """Pop và bỏ qua các phần tử đã deleted — O(log n) amortized"""
        while self.heap:
            priority, _, value = heapq.heappop(self.heap)
            if value not in self.deleted:
                return value
            self.deleted.discard(value)
        raise IndexError("Heap is empty")
    
    def peek(self):
        """Xem top mà không pop"""
        while self.heap:
            priority, _, value = self.heap[0]
            if value not in self.deleted:
                return value
            self.deleted.discard(heapq.heappop(self.heap)[2])
        raise IndexError("Heap is empty")
```

---

## 8. Khi Nào Dùng / Không Dùng

### Nên Dùng Heap Khi:

- **Cần lấy min/max liên tục** kết hợp với insert thường xuyên (Priority Queue)
- **Top-K elements** từ luồng dữ liệu lớn hoặc vô hạn
- **Merge K sorted sequences** (log K factor)
- **Graph algorithms:** Dijkstra, Prim's MST
- **Scheduling:** Job scheduling, event simulation
- **Median of data stream** (dùng 2 heap: max-heap + min-heap)

### Không Nên Dùng Heap Khi:

- **Cần search theo key:** Dùng Hash Map hoặc BST
- **Cần duyệt theo thứ tự toàn bộ:** Dùng sorted array hoặc BST
- **Cần range queries (tìm phần tử trong khoảng):** Dùng Segment Tree hoặc BST
- **Cần decrease-key hiệu quả:** Fibonacci Heap (lý thuyết) hoặc indexed priority queue
- **Dữ liệu tĩnh, chỉ query một lần:** Sort thẳng, không cần heap overhead

---

## 9. So Sánh Với Các Cấu Trúc Liên Quan

| Cấu trúc | Insert | Extract-min | Peek | Search | Delete-arb | Space | Notes |
|---|---|---|---|---|---|---|---|
| **Binary Heap** | O(log n) | O(log n) | O(1) | O(n) | O(log n) | O(n) | Cache-friendly |
| **Sorted Array** | O(n) | O(1) | O(1) | O(log n) | O(n) | O(n) | Good nếu ít insert |
| **Sorted Linked List** | O(n) | O(1) | O(1) | O(n) | O(n) | O(n) | No random access |
| **BST (balanced)** | O(log n) | O(log n) | O(log n) | O(log n) | O(log n) | O(n) | Hỗ trợ range query |
| **Fibonacci Heap** | O(1) amort | O(log n) | O(1) | O(n) | O(log n) | O(n) | Decrease-key O(1) |
| **d-ary Heap** | O(log_d n) | O(d log_d n) | O(1) | O(n) | O(log_d n) | O(n) | Tốt hơn khi nhiều insert |
| **Skip List** | O(log n) | O(log n) | O(log n) | O(log n) | O(log n) | O(n log n) | Probabilistic |

**Khi nào Fibonacci Heap hữu ích?** Dijkstra với nhiều decrease-key operations: O((V+E) log V) với binary heap → O(V log V + E) với Fibonacci Heap. Tuy nhiên constant factor lớn nên ít dùng trong practice.

---

## 10. Common Pitfalls

### Pitfall 1: Dùng heapq cho max-heap mà quên negate

```python
# SAI: heapq là min-heap, không phải max-heap
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
print(heapq.heappop(heap))  # 3 (min), không phải 5 (max)

# ĐÚNG: Negate để giả lập max-heap
heap = []
heapq.heappush(heap, -5)
heapq.heappush(heap, -3)
print(-heapq.heappop(heap))  # 5 (max)
```

### Pitfall 2: Modify phần tử trong heap trực tiếp

```python
# SAI: Thay đổi trực tiếp phá vỡ heap property
heap = [1, 3, 5]
heapq.heapify(heap)
heap[1] = -10  # NGUY HIỂM: heap property bị phá vỡ

# ĐÚNG: Dùng lazy deletion hoặc implement decrease_key đúng cách
```

### Pitfall 3: Off-by-one trong build heap

```python
# SAI: Bắt đầu từ index sai
n = len(arr)
for i in range(n - 1, -1, -1):  # Duyệt cả lá — lãng phí nhưng vẫn đúng
    sift_down(i, n)

# ĐÚNG: Bắt đầu từ node cuối không phải lá
for i in range(n // 2 - 1, -1, -1):  # Chỉ non-leaf nodes
    sift_down(i, n)
```

### Pitfall 4: Heap không đảm bảo toàn bộ mảng được sắp xếp

```python
heap = [1, 5, 3, 7, 9, 8]
heapq.heapify(heap)
print(heap)  # [1, 5, 3, 7, 9, 8] — chỉ heap[0] là min, còn lại không sorted!

# Để lấy sorted: phải gọi heappop() liên tục hoặc dùng heapq.nsmallest()
sorted_list = [heapq.heappop(heap) for _ in range(len(heap))]
```

### Pitfall 5: Tuple comparison với objects không comparable

```python
# SAI: Nếu priorities bằng nhau, Python sẽ so sánh phần thứ 2
# Nếu phần thứ 2 là object không có __lt__, sẽ raise TypeError
import heapq
heap = []
heapq.heappush(heap, (1, some_object))
heapq.heappush(heap, (1, another_object))  # ERROR nếu object không comparable

# ĐÚNG: Dùng counter làm tiebreaker
counter = 0
heapq.heappush(heap, (1, counter, some_object))
counter += 1
heapq.heappush(heap, (1, counter, another_object))
```

### Pitfall 6: Nhầm lẫn giữa heappushpop và heapreplace

```python
# heappushpop(heap, item): push item, rồi pop min — heap có thể rỗng
# heapreplace(heap, item): pop min, rồi push item — heap PHẢI không rỗng
# heappushpop hiệu quả hơn nếu item nhỏ hơn min (không cần thao tác heap)
```

---

## 11. Câu Hỏi Phỏng Vấn Hay Gặp

**Q1: Tại sao heapify (build heap) là O(n) chứ không phải O(n log n)?**
> Vì hầu hết nodes nằm gần lá (chiều cao thấp). Tổng công việc là Σ (n/2^h) × h = O(n) theo geometric series. Chỉ node gốc mới cần O(log n) sift down.

**Q2: Heap Sort có worst case O(n log n) nhưng tại sao ít được dùng hơn Quick Sort trong practice?**
> 1. Cache performance kém: heap sort truy cập bộ nhớ không theo thứ tự (nhảy qua index 2i+1, 2i+2 có thể xa nhau), gây nhiều cache miss. 2. Constant factor lớn hơn quick sort. 3. Quick sort average case rất tốt với pivot tốt. Trong thực tế, Python và Java dùng Timsort; C++ dùng introsort (hybrid quicksort + heapsort + insertion sort).

**Q3: Làm thế nào tìm median của data stream?**
> Dùng 2 heap: max-heap cho nửa nhỏ hơn, min-heap cho nửa lớn hơn. Luôn balance 2 heap sao cho kích thước chênh nhau tối đa 1. Median = gốc của heap lớn hơn, hoặc trung bình 2 gốc nếu cùng kích thước.

**Q4: Phân biệt heappushpop và heapreplace trong Python?**
> `heappushpop(h, x)`: push x rồi pop min. Nếu x <= min, return x luôn (không cần thao tác heap). `heapreplace(h, x)`: pop min rồi push x, heap không được rỗng. `heapreplace` hiệu quả hơn khi biết chắc x > min vì chỉ cần 1 sift down.

**Q5: Implement "K-th Largest in Stream" (LeetCode 703)?**
> Dùng min-heap kích thước k. Khi thêm phần tử mới: nếu heap chưa đủ k, push vào. Nếu phần tử mới > heap[0] (min của k phần tử lớn nhất), thì replace. K-th largest luôn là heap[0].

**Q6: Tại sao Python heapq không có max-heap built-in?**
> Thiết kế đơn giản hóa API. Max-heap dễ dàng implement bằng cách negate. Cho phép dùng (priority, item) tuples linh hoạt. Fibonacci Heap hoặc các cấu trúc phức tạp hơn không được include vì ít cần thiết trong practice.

**Q7: Difference giữa Priority Queue và Heap?**
> Heap là một concrete data structure (implementation). Priority Queue là một abstract data type (interface/concept). Heap là cách implement Priority Queue phổ biến nhất, nhưng có thể dùng sorted array, skip list, hoặc Fibonacci Heap để implement Priority Queue.

---

## 12. Dạng Bài Thường Gặp Trong Thi Cử / Phỏng Vấn

### Dạng 1: Top-K Elements — tìm K phần tử lớn/nhỏ nhất

**Nhận dạng đề:** "Tìm k phần tử lớn nhất", "k phần tử nhỏ nhất", "kth largest/smallest". Thường yêu cầu O(n log k) — gợi ý dùng heap kích thước k thay vì sort toàn bộ.

**Approach chuẩn:**
- K phần tử lớn nhất: min-heap kích thước k. Nếu phần tử mới > heap[0], `heapreplace`. Kết quả là toàn bộ heap.
- K phần tử nhỏ nhất: max-heap kích thước k (negate). Tương tự.
- Kth largest: min-heap kích thước k, trả về heap[0].

**LeetCode tiêu biểu:** "Kth Largest Element in a Stream" (LC 703), "Top K Frequent Elements" (LC 347), "K Closest Points to Origin" (LC 973)

---

### Dạng 2: Merge K Sorted Lists/Arrays

**Nhận dạng đề:** "Merge k sorted lists", "merge k sorted arrays", k danh sách/mảng đã sort thành một. Dấu hiệu: k > 2 sources cần merge.

**Approach chuẩn:** Min-heap lưu (value, list_index, element_index). Pop min → thêm vào result → push phần tử tiếp theo từ cùng danh sách. O(n log k) với n = tổng số phần tử.

**LeetCode tiêu biểu:** "Merge k Sorted Lists" (LC 23), "Smallest Range Covering Elements from K Lists" (LC 632)

---

### Dạng 3: Running Median — Median of Data Stream

**Nhận dạng đề:** "Tìm median sau mỗi lần thêm phần tử", "median của luồng dữ liệu", "find median dynamically". Không thể sort lại sau mỗi insert.

**Approach chuẩn:** Dùng 2 heap:
- Max-heap (lower half): chứa nửa nhỏ hơn
- Min-heap (upper half): chứa nửa lớn hơn
- Duy trì `|max_heap| - |min_heap| <= 1`
- Median = top của heap lớn hơn, hoặc trung bình 2 tops nếu cùng kích thước

**LeetCode tiêu biểu:** "Find Median from Data Stream" (LC 295)

---

### Dạng 4: Task Scheduling với Priority

**Nhận dạng đề:** "Lên lịch các task theo priority/deadline", "CPU scheduling", "job scheduling", "minimize total cost/time". Luôn xử lý task quan trọng/ngắn nhất trước.

**Approach chuẩn:**
- Sắp xếp tasks theo start time
- Heap lưu (cost/duration, deadline)
- Greedy: luôn pop task có cost nhỏ nhất/priority cao nhất trong các task available

**LeetCode tiêu biểu:** "Task Scheduler" (LC 621), "Minimum Number of Refueling Stops" (LC 871), "IPO" (LC 502)

---

### Dạng 5: Sliding Window Maximum/Minimum — heap với lazy deletion

**Nhận dạng đề:** "Maximum/minimum trong mỗi cửa sổ kích thước k", "sliding window max". Cần track min/max khi cửa sổ di chuyển.

**Approach chuẩn (Heap với lazy deletion):**
- Push `(-value, index)` vào max-heap
- Khi pop: kiểm tra xem index có còn trong cửa sổ không (index >= i - k + 1)
- Nếu không → bỏ qua (lazy delete), pop tiếp
- Cách tối ưu hơn: dùng Monotonic Deque O(n)

**LeetCode tiêu biểu:** "Sliding Window Maximum" (LC 239), "Sliding Window Minimum" (LC 1696)

---

### Dạng 6: Dijkstra — Shortest Path với Weighted Graph

**Nhận dạng đề:** "Đường đi ngắn nhất trong đồ thị có trọng số dương", "minimum cost path", "shortest time to reach". Có từ "weighted", "cost", "distance" với số dương.

**Approach chuẩn:**
1. Min-heap lưu `(distance, node)`
2. Pop node có distance nhỏ nhất
3. Cập nhật neighbors: nếu `dist[node] + weight < dist[neighbor]` → push vào heap
4. Đánh dấu visited để không xử lý node 2 lần
5. O((V+E) log V)

**LeetCode tiêu biểu:** "Network Delay Time" (LC 743), "Path With Minimum Effort" (LC 1631), "Cheapest Flights Within K Stops" (LC 787)

---

### Dạng 7: Greedy với Heap — luôn chọn phần tử tối ưu hiện tại

**Nhận dạng đề:** "Tối thiểu hóa/tối đa hóa kết quả", "greedy choice at each step", bài toán cần chọn phần tử min/max liên tục trong khi tập phần tử thay đổi.

**Approach chuẩn:**
- Sort hoặc heap để luôn có phần tử tốt nhất
- Pop phần tử tối ưu → process → push kết quả mới vào heap
- Pattern: `result += heappop(heap)` rồi `heappush(heap, new_val)`

**LeetCode tiêu biểu:** "Minimum Cost to Connect Sticks" (LC 1167), "Reorganize String" (LC 767), "Maximum Frequency Stack" (LC 895)

---

### Dạng 8: K-th Smallest/Largest trong Matrix hay BST

**Nhận dạng đề:** "Kth smallest in sorted matrix", "kth smallest in BST", matrix hoặc BST đã có tính chất thứ tự.

**Approach chuẩn (matrix):**
- Min-heap + BFS tương tự merge k sorted lists
- Push (matrix[0][0], 0, 0) vào heap
- Pop min → push neighbors chưa thăm

**Approach chuẩn (BST):** Inorder traversal (cho sorted sequence), đếm đến k.

**LeetCode tiêu biểu:** "Kth Smallest Element in a Sorted Matrix" (LC 378), "Kth Smallest Element in a BST" (LC 230)

---

### Dạng 9: Huffman Encoding / Optimal Merging

**Nhận dạng đề:** "Minimum cost to merge files/arrays", "minimum total cost", "luôn merge 2 phần tử nhỏ nhất". Bài toán Huffman tree.

**Approach chuẩn:**
1. Đưa tất cả vào min-heap
2. Pop 2 phần tử nhỏ nhất, cộng lại = cost
3. Push tổng trở lại heap
4. Lặp đến khi còn 1 phần tử
5. Tổng cost là kết quả

**LeetCode tiêu biểu:** "Minimum Cost to Connect Sticks" (LC 1167), "Minimum Cost of Ropes" (tương tự trong interview)

---

### Dạng 10: Heap với Custom Comparator / Tuple Priority

**Nhận dạng đề:** Cần ưu tiên theo nhiều tiêu chí: "nếu cost bằng nhau, chọn theo thứ tự alphabet", bài toán multi-criteria priority.

**Approach chuẩn:**
- Python heapq so sánh tuple lexicographically: `(priority1, priority2, value)`
- Max-heap: negate priority `(-priority, value)`
- Tránh TypeError khi priority bằng nhau và value không comparable: thêm counter làm tiebreaker `(priority, counter, value)`
- Counter đảm bảo FIFO khi priority bằng nhau

**LeetCode tiêu biểu:** "The Skyline Problem" (LC 218), "Find the Most Competitive Subsequence" (LC 1673), "Process Tasks Using Servers" (LC 1882)
