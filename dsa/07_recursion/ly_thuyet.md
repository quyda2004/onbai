# Recursion & Recurrence Relation (Đệ Quy & Quan Hệ Truy Hồi)

---

## 1. Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đứng trước một cái gương và cầm thêm một cái gương khác phía sau — bạn thấy vô số ảnh thu nhỏ dần. Đó là ý tưởng của đệ quy: **một hàm gọi lại chính nó** với bài toán nhỏ hơn.

**Ví dụ đơn giản — Tính giai thừa:**
- `5! = 5 × 4!`
- `4! = 4 × 3!`
- `3! = 3 × 2!`
- `2! = 2 × 1!`
- `1! = 1` ← dừng ở đây

Bạn không cần giải quyết toàn bộ ngay lập tức. Bạn chỉ cần tin rằng: "Nếu tôi biết `(n-1)!`, tôi có thể tính `n!` dễ dàng." Sau đó, giao việc tính `(n-1)!` cho chính mình — nhưng với bài toán nhỏ hơn.

**Ví dụ cuộc sống thực:**
- **Tìm tài liệu trong thư mục:** Vào thư mục con đầu tiên → nếu có thư mục con nữa, vào tiếp → khi không còn thư mục con, xem file → quay lại thư mục cha. Đây chính là đệ quy!
- **Bóc vỏ hành:** Bóc một lớp, nếu còn lớp khác thì lại bóc tiếp, cho đến khi hết vỏ.
- **Tháp Hà Nội trong đời thường:** Di chuyển 64 đĩa = di chuyển 63 đĩa + di chuyển đĩa lớn nhất + di chuyển 63 đĩa. Không ai cần nghĩ tất cả 2^64 - 1 bước!

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Call Stack — Cơ chế bên trong

Mỗi lần gọi hàm đệ quy, máy tính tạo một **stack frame** trên call stack. Stack frame chứa:
- **Return address:** địa chỉ để quay về sau khi hàm kết thúc
- **Local variables:** biến cục bộ của lần gọi này
- **Parameters:** tham số truyền vào
- **Saved registers:** trạng thái CPU cần khôi phục

```
Call stack khi tính factorial(4):
┌─────────────────────┐  ← Top of stack
│ factorial(1)        │
│   n=1, return 1     │
├─────────────────────┤
│ factorial(2)        │
│   n=2, waiting...   │
├─────────────────────┤
│ factorial(3)        │
│   n=3, waiting...   │
├─────────────────────┤
│ factorial(4)        │
│   n=4, waiting...   │
├─────────────────────┤
│ main()              │
└─────────────────────┘  ← Bottom of stack
```

Khi `factorial(1)` trả về 1, stack unwinds: 1 → 2×1=2 → 3×2=6 → 4×6=24.

### Stack Overflow

Call stack có giới hạn kích thước. Python mặc định cho phép ~1000 stack frames (`sys.getrecursionlimit()`). Nếu đệ quy sâu hơn → `RecursionError`.

**Khi nào xảy ra:**
- Base case sai → đệ quy vô tận
- Input quá lớn (n > 1000 cho đệ quy tuyến tính trong Python)
- Đệ quy trên cây không cân bằng có chiều sâu O(n)

**Giải pháp:**
1. Chuyển sang iteration (explicit stack)
2. Tăng `sys.setrecursionlimit()` (tạm thời, không khuyến khích)
3. Dùng Tail Call Optimization (không available trong Python)
4. Trampolining

### Tail Recursion vs Non-Tail Recursion

**Non-tail recursion:** Có computation sau khi gọi đệ quy.
```python
def factorial(n):
    if n <= 1: return 1
    return n * factorial(n-1)  # phải nhân n sau khi factorial(n-1) trả về
    # → stack frame phải giữ n cho đến khi sub-call xong
```

**Tail recursion:** Gọi đệ quy là thao tác CUỐI CÙNG.
```python
def factorial_tail(n, acc=1):
    if n <= 1: return acc
    return factorial_tail(n-1, n * acc)  # không cần làm gì sau khi sub-call trả về
    # → Compiler/interpreter có thể tái sử dụng stack frame (TCO)
```

**Tại sao quan trọng:** Với TCO, tail recursion sử dụng O(1) stack space thay vì O(n). Python không có TCO nên tail recursion vẫn dùng O(n) stack, nhưng trong ngôn ngữ khác (Scheme, Haskell, Scala) thì tối ưu.

### Memoization — Đệ quy + Cache

Fibonacci naive gọi `fib(n-1)` và `fib(n-2)`, dẫn đến tính lại subproblem nhiều lần:
```
fib(5)
├── fib(4)
│   ├── fib(3)
│   │   ├── fib(2)  ← tính lần 1
│   │   └── fib(1)
│   └── fib(2)      ← tính lại lần 2!
└── fib(3)          ← tính lại lần 3!
```

Memoization: thêm cache dictionary. Mỗi subproblem chỉ tính 1 lần → O(n) thay vì O(2^n).

---

## 3. Định nghĩa chính xác

**Recursion (Đệ quy):** Kỹ thuật lập trình trong đó một hàm gọi lại chính nó với input nhỏ hơn, cho đến khi đạt base case.

**3 thành phần của đệ quy:**
1. **Base case:** Điều kiện dừng — trả về kết quả trực tiếp mà không gọi đệ quy
2. **Recursive case:** Gọi lại hàm với input nhỏ hơn (tiến dần về base case)
3. **Trust the recursion:** Giả định sub-call hoạt động đúng, chỉ cần viết logic 1 bước

**Recurrence Relation (Quan hệ truy hồi):** Phương trình biểu diễn thời gian chạy T(n) theo thời gian chạy trên input nhỏ hơn. Ví dụ: `T(n) = 2T(n/2) + O(n)`.

**Master Theorem:** Công cụ giải recurrence relation dạng `T(n) = aT(n/b) + f(n)`.

---

## 4. Bảng Độ Phức Tạp Đầy Đủ

### Master Theorem — 3 Cases

Dạng: `T(n) = aT(n/b) + f(n)` (a ≥ 1, b > 1)

So sánh `f(n)` với `n^(log_b a)`:

| Case | Điều kiện | Kết quả T(n) | Ví dụ |
|------|-----------|-------------|-------|
| Case 1 | `f(n) = O(n^(log_b a - ε))` | `Θ(n^(log_b a))` | Merge Sort: 2T(n/2)+O(n) → a=2,b=2,log_2(2)=1, f(n)=O(n^1) → Case 2 |
| Case 2 | `f(n) = Θ(n^(log_b a))` | `Θ(n^(log_b a) × log n)` | Merge Sort: Θ(n log n) |
| Case 3 | `f(n) = Ω(n^(log_b a + ε))` và regularity | `Θ(f(n))` | T(n)=T(n/2)+O(n) → O(n) |

### Recurrence Relations Phổ Biến

| Recurrence | Giải ra | Thuật toán ví dụ |
|-----------|---------|------------------|
| T(n) = T(n-1) + O(1) | O(n) | Factorial, traversal |
| T(n) = T(n-1) + O(n) | O(n²) | Insertion sort |
| T(n) = 2T(n-1) + O(1) | O(2^n) | Tower of Hanoi |
| T(n) = T(n/2) + O(1) | O(log n) | Binary search |
| T(n) = T(n/2) + O(n) | O(n) | Quickselect (average) |
| T(n) = 2T(n/2) + O(1) | O(n) | Tree size |
| T(n) = 2T(n/2) + O(n) | O(n log n) | Merge sort |
| T(n) = 2T(n/2) + O(n²) | O(n²) | Matrix add recursive |

### Complexity Các Thuật Toán Đệ Quy

| Thuật toán | Time (Best) | Time (Average) | Time (Worst) | Space (Stack) |
|------------|-------------|----------------|-------------|---------------|
| Fibonacci naive | O(2^n) | O(2^n) | O(2^n) | O(n) — depth n |
| Fibonacci memoized | O(n) | O(n) | O(n) | O(n) — stack + memo |
| Factorial | O(n) | O(n) | O(n) | O(n) — n stack frames |
| Binary search (recursive) | O(1) | O(log n) | O(log n) | O(log n) — stack depth |
| Merge sort | O(n log n) | O(n log n) | O(n log n) | O(n) merge buffer + O(log n) stack |
| Quick sort | O(n log n) | O(n log n) | O(n²) — bad pivot | O(log n) avg, O(n) worst stack |
| Tower of Hanoi | O(2^n) | O(2^n) | O(2^n) | O(n) — stack depth n |
| Tree DFS (balanced) | O(n) | O(n) | O(n) | O(log n) stack |
| Tree DFS (unbalanced) | O(n) | O(n) | O(n) | O(n) stack — worst case |

**Ghi chú Stack Depth:**
- Fibonacci: depth = n → O(n) stack
- Binary search: depth = log n → O(log n) stack
- Merge sort: depth = log n → O(log n) stack (không tính O(n) merge buffer)
- Quick sort: worst case (sorted array, naive pivot) depth = n → O(n) stack

---

## 5. Code mẫu Python

```python
import sys
from functools import lru_cache

# ============================================================
# 1. FACTORIAL — Minh họa call stack
# ============================================================

def factorial_recursive(n):
    """
    Base case: n <= 1
    Recursive case: n * factorial(n-1)
    T(n) = T(n-1) + O(1) → O(n)
    Stack depth: O(n)
    """
    if n <= 1:        # base case
        return 1
    return n * factorial_recursive(n - 1)  # non-tail recursion


def factorial_tail(n, accumulator=1):
    """
    Tail recursion version — accumulator gộp kết quả vào param.
    Trong Python vẫn O(n) stack, nhưng minh họa pattern.
    """
    if n <= 1:
        return accumulator
    return factorial_tail(n - 1, n * accumulator)  # tail call


# ============================================================
# 2. FIBONACCI — Naive vs Memoized
# ============================================================

def fib_naive(n):
    """
    Naive đệ quy — tính lại subproblem nhiều lần.
    T(n) = T(n-1) + T(n-2) + O(1) ≈ O(φ^n) ≈ O(2^n)
    Stack depth: O(n)
    """
    if n <= 1:
        return n
    return fib_naive(n - 1) + fib_naive(n - 2)


def fib_memo(n, memo=None):
    """
    Memoization: lưu kết quả đã tính vào dict.
    Mỗi fib(i) chỉ tính 1 lần → T(n) = O(n)
    Space: O(n) cho memo dict + O(n) stack
    """
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]


@lru_cache(maxsize=None)
def fib_lru(n):
    """
    Python built-in decorator cho memoization — elegant nhất.
    """
    if n <= 1:
        return n
    return fib_lru(n - 1) + fib_lru(n - 2)


# ============================================================
# 3. BINARY SEARCH — Đệ quy
# ============================================================

def binary_search(arr, target, lo, hi):
    """
    T(n) = T(n/2) + O(1) → O(log n)
    Stack depth: O(log n)
    """
    if lo > hi:          # base case: không tìm thấy
        return -1
    mid = (lo + hi) // 2
    if arr[mid] == target:   # base case: tìm thấy
        return mid
    elif arr[mid] < target:
        return binary_search(arr, target, mid + 1, hi)  # tìm nửa phải
    else:
        return binary_search(arr, target, lo, mid - 1)  # tìm nửa trái


# ============================================================
# 4. MERGE SORT — T(n) = 2T(n/2) + O(n) → O(n log n)
# ============================================================

def merge_sort(arr):
    """
    Divide: chia đôi mảng.
    Conquer: đệ quy sort từng nửa.
    Combine: merge 2 nửa đã sorted.
    T(n) = 2T(n/2) + O(n) → O(n log n) theo Master Theorem Case 2.
    Stack depth: O(log n)
    """
    if len(arr) <= 1:    # base case
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])   # sort nửa trái
    right = merge_sort(arr[mid:])  # sort nửa phải
    return merge(left, right)      # merge kết quả


def merge(left, right):
    """Merge 2 sorted arrays — O(n) time."""
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result


# ============================================================
# 5. TOWER OF HANOI — T(n) = 2T(n-1) + O(1) → O(2^n)
# ============================================================

def hanoi(n, source, auxiliary, target):
    """
    Di chuyển n đĩa từ source sang target dùng auxiliary.
    Bước 1: Di chuyển n-1 đĩa từ source sang auxiliary.
    Bước 2: Di chuyển đĩa lớn nhất từ source sang target.
    Bước 3: Di chuyển n-1 đĩa từ auxiliary sang target.
    
    T(n) = 2T(n-1) + 1 → T(n) = 2^n - 1 = O(2^n)
    Không thể làm tốt hơn — cần đúng 2^n - 1 bước.
    """
    if n == 1:    # base case: 1 đĩa, di chuyển thẳng
        print(f"Move disk 1 from {source} to {target}")
        return
    
    hanoi(n - 1, source, target, auxiliary)  # bước 1
    print(f"Move disk {n} from {source} to {target}")  # bước 2
    hanoi(n - 1, auxiliary, source, target)  # bước 3


# ============================================================
# 6. POWER FUNCTION — Fast exponentiation
# ============================================================

def power(base, exp):
    """
    Tính base^exp nhanh.
    T(n) = T(n/2) + O(1) → O(log n) thay vì O(n) naive.
    """
    if exp == 0:
        return 1
    if exp % 2 == 0:
        half = power(base, exp // 2)
        return half * half           # tái sử dụng, không gọi 2 lần
    else:
        return base * power(base, exp - 1)


# ============================================================
# DEMO
# ============================================================

print("Factorial(5):", factorial_recursive(5))      # 120
print("Fibonacci naive(10):", fib_naive(10))         # 55
print("Fibonacci memo(100):", fib_memo(100))         # huge number
print("Binary search:", binary_search([1,3,5,7,9], 5, 0, 4))  # 2
print("Merge sort:", merge_sort([5,2,4,6,1,3]))      # [1,2,3,4,5,6]
hanoi(3, 'A', 'B', 'C')
print("2^10:", power(2, 10))                          # 1024


# ============================================================
# 7. STACK OVERFLOW DEMO — Đo giới hạn
# ============================================================

def check_recursion_limit():
    """Kiểm tra giới hạn đệ quy của Python."""
    print(f"Python recursion limit: {sys.getrecursionlimit()}")
    # Default: 1000
    # Tăng: sys.setrecursionlimit(10000)  — không khuyến khích


check_recursion_limit()
```

---

## 6. Khi nào dùng / Không dùng

### Dùng Đệ Quy khi:
- Bài toán có **cấu trúc tự tương tự (self-similar)**: tree traversal, graph DFS, divide & conquer
- Bài toán có **subproblem rõ ràng**: Fibonacci, merge sort, binary search
- Code đệ quy **đơn giản hơn iteration đáng kể**: tree operations, permutation generation
- **Depth nhỏ** (log n hoặc constant): binary search, merge sort
- Cần **backtracking**: N-Queens, Sudoku solver, path finding

### Không dùng khi:
- Depth đệ quy lớn (n > 1000 trong Python) → nguy cơ stack overflow
- **Performance critical** với nhiều repeated calls (dùng DP/memo)
- **Simple linear iteration** đủ giải quyết — iteration luôn nhanh hơn do không có function call overhead
- Ngôn ngữ không hỗ trợ TCO và cần deep recursion

### So sánh Đệ quy vs Iteration

| Tiêu chí | Đệ quy | Iteration |
|----------|--------|-----------|
| Readability | Thường rõ ràng hơn với tree/graph | Rõ hơn với simple loops |
| Stack usage | O(depth) — nguy cơ stack overflow | O(1) — explicit stack nếu cần |
| Performance | Overhead: function call, frame allocation | Không có overhead đó |
| Debugging | Khó trace call stack sâu | Dễ trace hơn |
| Suitability | Tree, graph, divide & conquer | Array processing, simple loops |
| TCO | Cần compiler support | Không cần |

**Quy tắc thực tế:** Nếu bài toán tự nhiên là đệ quy (tree, graph, D&C) → dùng đệ quy. Nếu là simple array scan → dùng iteration.

---

## 7. So sánh với các kỹ thuật liên quan

| Kỹ thuật | Cơ chế | Time | Space | Khi nào dùng |
|----------|--------|------|-------|-------------|
| Naive Recursion | Gọi đệ quy thuần túy | Phụ thuộc | O(depth) stack | Bài toán đơn giản, depth nhỏ |
| Memoization (Top-down DP) | Recursion + cache | O(n) states | O(n) memo + stack | Overlapping subproblems |
| Bottom-up DP | Iteration + table | O(n) states | O(n) hoặc O(1) | Overlapping subproblems, không cần stack |
| Tail Recursion | Last call optimization | Giống recursion | O(1) stack (nếu TCO) | Ngôn ngữ hỗ trợ TCO |
| Explicit Stack (iterative DFS) | Stack data structure | Giống recursion | O(depth) heap | Tránh stack overflow |
| Trampolining | Thunk + loop | Giống recursion | O(1) stack | Python không có TCO |

---

## 8. Common Pitfalls

### Pitfall 1: Thiếu base case hoặc base case sai
```python
# SAI: base case không bao giờ đạt được
def countdown(n):
    print(n)
    return countdown(n - 1)  # không có base case → infinite recursion

# ĐÚNG:
def countdown(n):
    if n <= 0:
        return
    print(n)
    return countdown(n - 1)
```

### Pitfall 2: Base case sai → silent wrong answer
```python
# SAI: base case n==0 thay vì n<=1
def factorial(n):
    if n == 0: return 1
    return n * factorial(n - 1)
# factorial(-1) → gọi factorial(-2) → ... → stack overflow!
# ĐÚNG: kiểm tra n <= 0 hoặc n <= 1
```

### Pitfall 3: Không trust the recursion — code quá phức tạp
```python
# SAI: cố tự giải quyết thay vì tin vào sub-call
def sum_list(lst):
    if len(lst) == 0: return 0
    total = lst[0]
    # Cố iterate thủ công thay vì trust recursion:
    for i in range(1, len(lst)):
        total += lst[i]  # ??? không phải recursion nữa
    return total

# ĐÚNG: trust the recursion
def sum_list(lst):
    if len(lst) == 0: return 0
    return lst[0] + sum_list(lst[1:])  # tin rằng sum_list(lst[1:]) đúng
```

### Pitfall 4: Mutable default argument trong memoization
```python
# SAI: memo dict được share giữa tất cả calls!
def fib(n, memo={}):  # {} chỉ tạo 1 lần khi define function
    ...

# ĐÚNG: dùng None sentinel
def fib(n, memo=None):
    if memo is None:
        memo = {}
    ...
```

### Pitfall 5: Không giảm input về phía base case
```python
# SAI: n tăng thay vì giảm
def bad_recursion(n):
    if n == 100: return "done"
    return bad_recursion(n + 1)  # nếu n > 100, tăng vô tận!
```

### Pitfall 6: Stack overflow trên cây không cân bằng
```python
# Với BST degenerate (sorted input), DFS đệ quy có depth O(n)
# → stack overflow với n = 1000 trong Python
def bst_insert(root, val):
    if not root: return Node(val)
    if val < root.val:
        root.left = bst_insert(root.left, val)
    else:
        root.right = bst_insert(root.right, val)
    return root
# Solution: dùng iterative version hoặc balanced BST
```

---

## 9. Câu hỏi phỏng vấn hay gặp

**Q1. Giải thích đệ quy mà không dùng từ "đệ quy".**
→ Chia bài toán thành bài toán con cùng loại nhưng nhỏ hơn, giải quyết đến khi đơn giản đến mức tầm thường, rồi kết hợp kết quả.

**Q2. Base case của binary search đệ quy là gì?**
→ `lo > hi` (không tìm thấy) hoặc `arr[mid] == target` (tìm thấy).

**Q3. Tại sao Fibonacci đệ quy naive là O(2^n)?**
→ Recurrence T(n) = T(n-1) + T(n-2). Mỗi call tạo 2 sub-calls → cây nhị phân có chiều sâu n → O(2^n) nodes.

**Q4. Memoization giúp gì cho Fibonacci?**
→ Mỗi `fib(i)` chỉ tính 1 lần, kết quả cache lại → O(n) unique subproblems → O(n) total. Stack depth vẫn O(n).

**Q5. Tail recursion là gì? Python có tối ưu không?**
→ Recursive call là thao tác cuối cùng trong hàm. Python KHÔNG có TCO, nên tail recursion vẫn dùng O(n) stack.

**Q6. Khi nào đổi từ đệ quy sang iteration?**
→ (1) Stack overflow risk, (2) depth lớn hơn recursion limit, (3) performance critical, (4) simple loop đủ.

**Q7. Giải recurrence T(n) = 2T(n/2) + O(n).**
→ Master Theorem Case 2: a=2, b=2, log_b(a)=1, f(n)=O(n^1). f(n) = Θ(n^(log_b a)) → T(n) = Θ(n log n).

**Q8. Tower of Hanoi cần ít nhất bao nhiêu bước?**
→ T(n) = 2^n - 1. Chứng minh: T(1)=1, T(n)=2T(n-1)+1 → T(n)=2^n-1. Optimal — không thể ít hơn.

**Q9. LeetCode 104 — Maximum Depth of Binary Tree: đệ quy như thế nào?**
→ `depth(node) = 1 + max(depth(node.left), depth(node.right))`. Base case: `depth(None) = 0`.

**Q10. Khi nào dùng explicit stack thay vì đệ quy?**
→ Khi cần tránh stack overflow (deep tree/graph), hoặc khi cần kiểm soát thứ tự xử lý chi tiết hơn.
