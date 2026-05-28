# Big O Notation — Độ Phức Tạp Thuật Toán

---

## 1. Vấn đề đặt ra

Bạn viết hai chương trình tìm kiếm cùng cho ra đáp án đúng. Một chương trình chạy mất 0.01 giây với 1000 dữ liệu. Chương trình kia cũng vậy. Nhưng khi dữ liệu tăng lên 1 triệu, chương trình đầu tiên mất 10 giây, còn chương trình thứ hai mất... 3 giờ.

Bạn cần một cách để **dự đoán trước** điều này — mà không cần thực sự chạy thử với dữ liệu khổng lồ. Big O Notation ra đời để giải quyết đúng vấn đề đó: đo lường tốc độ tăng trưởng của thời gian chạy khi dữ liệu tăng lên.

---

## 2. Giải thích bằng hình ảnh đời thường

Hãy tưởng tượng bạn cần tìm tên một người trong danh sách điện thoại.

- **Cách 1 — Lật từng trang:** Bạn mở từ trang đầu, lật từng trang một cho đến khi gặp tên cần tìm. Nếu danh sách có 100 trang thì tối đa 100 lần lật. Có 1.000 trang thì tối đa 1.000 lần. Số lần lật tỉ lệ thuận với số trang.

- **Cách 2 — Chia đôi liên tục (Binary Search):** Bạn mở ra giữa cuốn sách, thấy tên "Nguyễn M", biết tên cần tìm "Trần B" nằm ở nửa sau, bỏ nửa trước. Tiếp tục chia đôi phần còn lại. Với 1.000 trang, chỉ cần khoảng 10 lần — vì 2^10 = 1024.

- **Cách 3 — Nhớ đúng trang:** Bạn nhớ chính xác tên đó ở trang 247, mở thẳng đến đó. Dù danh sách có 10 trang hay 10 triệu trang, vẫn chỉ mở 1 lần.

Big O đo lường **xu hướng tăng của số bước thực hiện** khi kích thước dữ liệu (n) tăng lên. Nó không đo giây hay mili-giây — nó đo *tốc độ tăng*.

---

## 3. Khái niệm cơ bản

**Big O Notation** là ký hiệu mô tả giới hạn trên của thời gian chạy hoặc bộ nhớ của một thuật toán, theo kích thước đầu vào n.

Các mức phổ biến (từ nhanh đến chậm):

| Ký hiệu | Tên | Ví dụ trực quan |
|---------|-----|-----------------|
| O(1) | Constant | Tra bảng, truy cập index mảng |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Duyệt 1 lần toàn bộ mảng |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | 2 vòng lặp lồng nhau |
| O(2ⁿ) | Exponential | Liệt kê tất cả tập con |
| O(n!) | Factorial | Liệt kê tất cả hoán vị |

---

## 4. Ví dụ từng bước (step-by-step)

Bài toán: Tìm phần tử lớn nhất trong mảng `[3, 7, 1, 9, 4]`.

**Bước 1:** Khởi tạo `max = arr[0] = 3`. Tại sao? Vì ta cần một giá trị ban đầu để so sánh.

**Bước 2:** Duyệt từ phần tử thứ 2 (index 1):
- So sánh `7 > 3`? Có → `max = 7`
- So sánh `1 > 7`? Không → giữ nguyên
- So sánh `9 > 7`? Có → `max = 9`
- So sánh `4 > 9`? Không → giữ nguyên

**Bước 3:** Kết thúc. `max = 9`.

Ta thực hiện `n - 1` phép so sánh cho mảng `n` phần tử → **O(n)**.

Tại sao bỏ đi `- 1`? Vì Big O quan tâm đến xu hướng khi n rất lớn. Khi n = 1.000.000, sự khác biệt giữa n và n-1 là không đáng kể.

---

## 5. Code Python đơn giản nhất

```python
def find_max(arr):
    max_val = arr[0]          # Bước 1: Khởi tạo
    for num in arr[1:]:       # Bước 2: Duyệt từ phần tử thứ 2
        if num > max_val:
            max_val = num     # Cập nhật nếu tìm được phần tử lớn hơn
    return max_val

# Test
print(find_max([3, 7, 1, 9, 4]))  # Output: 9
```

Hàm này: O(n) time, O(1) space.

---

## 6. Mở rộng dần

**Nhận biết độ phức tạp từ code:**

```python
# O(1) — Không có vòng lặp phụ thuộc n
def get_first(arr):
    return arr[0]

# O(n) — 1 vòng lặp
def linear_search(arr, target):
    for item in arr:
        if item == target:
            return True
    return False

# O(n²) — 2 vòng lặp lồng nhau
def has_duplicate(arr):
    for i in range(len(arr)):
        for j in range(i + 1, len(arr)):
            if arr[i] == arr[j]:
                return True
    return False

# O(log n) — Mỗi bước chia đôi bài toán
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

**Quy tắc cộng và nhân:**
- Hai vòng lặp **tuần tự**: O(n) + O(m) = O(n + m)
- Hai vòng lặp **lồng nhau**: O(n) × O(m) = O(n × m)

---

## 7. Độ phức tạp — Phân tích đầy đủ

### Ba loại ký hiệu Asymptotic

| Ký hiệu | Tên | Nghĩa | Dùng để |
|---------|-----|-------|---------|
| O(g(n)) | Big-O | Upper bound | Mô tả **worst case** (phổ biến nhất) |
| Ω(g(n)) | Omega | Lower bound | Mô tả **best case** |
| Θ(g(n)) | Theta | Tight bound | Khi best = worst (chính xác nhất) |

**Ví dụ — Linear Search:**
- **O(n)** — worst case: phần tử ở cuối hoặc không có
- **Ω(1)** — best case: phần tử ở đầu mảng
- Không có Θ(n) vì best ≠ worst

**Ví dụ — tính tổng mảng:**
- **Θ(n)** — luôn phải duyệt hết mảng, không có trường hợp thoát sớm

### Tại sao bỏ hằng số và lower-order terms?

```
T(n) = 5n² + 3n + 100
```

Khi n = 1.000.000:
- `5n²` = 5 × 10¹²
- `3n` = 3 × 10⁶  → chỉ chiếm 0.00006% → không đáng kể
- `100` → không đáng kể

Hằng số `5` phụ thuộc vào phần cứng (CPU nhanh hay chậm), không phản ánh thuật toán. Nên ta viết **O(n²)**.

**Định nghĩa hình thức:**
```
f(n) = O(g(n)) iff ∃ c > 0, n₀ > 0 sao cho f(n) ≤ c·g(n) với mọi n ≥ n₀
```

### Bảng độ phức tạp đầy đủ

| Thao tác | Best Case | Average Case | Worst Case | Space | Ghi chú |
|----------|-----------|--------------|------------|-------|---------|
| Truy cập mảng | O(1) | O(1) | O(1) | O(1) | Index trực tiếp |
| Linear Search | O(1) | O(n) | O(n) | O(1) | Best: phần tử đầu |
| Binary Search | O(1) | O(log n) | O(log n) | O(1) | Mảng phải sorted |
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Best: đã sorted |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Luôn ổn định |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | Worst: pivot tệ |

---

## 8. Patterns & Variations

### Nhận biết nhanh từ code pattern

```python
# Pattern 1: O(1) — Constant operations
arr[i]          # Index access
hash_map[key]   # Hash lookup
stack.append()  # Push to stack

# Pattern 2: O(n) — Single pass
for x in arr: ...

# Pattern 3: O(n²) — Nested loops trên cùng tập dữ liệu
for i in range(n):
    for j in range(n): ...

# Pattern 4: O(log n) — Chia đôi mỗi bước
while n > 1:
    n = n // 2

# Pattern 5: O(n log n) — Sort + scan, hoặc loop × binary search
arr.sort()      # O(n log n)
for x in arr:   # O(n)
    binary_search(arr, target - x)  # O(log n) → tổng O(n log n)

# Pattern 6: O(2ⁿ) — Đệ quy phân nhánh nhị phân
def fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)  # 2 nhánh mỗi lần → O(2ⁿ)
```

### Amortized Analysis — Phân tích khấu hao

Đôi khi một thao tác tốn O(n) nhưng xảy ra rất ít. Ví dụ `list.append()` trong Python:

- Thông thường: O(1) — chỉ thêm vào cuối
- Khi mảng đầy: O(n) — cần resize (copy toàn bộ sang mảng gấp đôi)
- **Amortized:** O(1) — vì resize xảy ra rất ít, chia đều chi phí ra tất cả các lần append

---

## 9. Code nâng cao / Phân tích thực tế

```python
def analyze_complexity_examples():
    """
    Ví dụ minh họa các mức complexity khác nhau
    trên cùng một bài toán: "Có tồn tại hai số trong mảng cộng lại bằng target không?"
    """
    
    # Cách 1: O(n²) — Brute force, 2 vòng lặp lồng
    def two_sum_brute(arr, target):
        n = len(arr)
        for i in range(n):
            for j in range(i + 1, n):   # j bắt đầu từ i+1 để tránh dùng lại phần tử
                if arr[i] + arr[j] == target:
                    return True
        return False
    
    # Cách 2: O(n log n) — Sort + Two Pointers
    def two_sum_sort(arr, target):
        arr = sorted(arr)       # O(n log n)
        left, right = 0, len(arr) - 1
        while left < right:     # O(n)
            s = arr[left] + arr[right]
            if s == target: return True
            elif s < target: left += 1
            else: right -= 1
        return False
    
    # Cách 3: O(n) — HashSet
    def two_sum_hash(arr, target):
        seen = set()            # O(n) space
        for num in arr:         # O(n) time
            if target - num in seen:    # O(1) lookup
                return True
            seen.add(num)
        return False
    
    return two_sum_brute, two_sum_sort, two_sum_hash

# Benchmark để thấy sự khác biệt thực tế
import time
import random

def benchmark():
    arr = random.sample(range(10**6), 10**4)  # 10.000 phần tử
    target = arr[0] + arr[-1]  # Đảm bảo có solution
    
    brute, sort_tp, hash_tp = analyze_complexity_examples()
    
    for name, func in [("O(n²) Brute", brute), ("O(n log n) Sort", sort_tp), ("O(n) Hash", hash_tp)]:
        start = time.time()
        result = func(arr, target)
        elapsed = time.time() - start
        print(f"{name}: {elapsed:.4f}s → {result}")

benchmark()
```

---

## 10. Khi nào dùng / Không dùng

**Quan tâm đến Big O khi:**
- Dataset lớn (n > 1.000 trở lên)
- Code chạy trong production (server, API)
- Phỏng vấn kỹ thuật
- Lựa chọn thuật toán/CTDL phù hợp

**Không cần quá lo lắng khi:**
- n nhỏ (< 100): sự khác biệt O(n) vs O(n²) không đáng kể
- Prototype/script chạy 1 lần
- Constant factor thực sự quan trọng: đôi khi O(n²) với hằng số nhỏ nhanh hơn O(n log n) với overhead lớn khi n nhỏ

---

## 11. So sánh với các cách đo lường khác

| Phương pháp | Ưu điểm | Nhược điểm |
|-------------|---------|------------|
| Big O (lý thuyết) | Độc lập hardware, tổng quát | Bỏ qua constant factor |
| Benchmark thực tế | Đo chính xác trên hardware cụ thể | Phụ thuộc môi trường, khó tái lặp |
| Profiling | Tìm bottleneck cụ thể | Tốn thời gian, cần code hoàn chỉnh |
| Amortized analysis | Chính xác hơn Big O cho dynamic structures | Phức tạp hơn |

---

## 12. Pitfalls & Câu hỏi phỏng vấn

### Lỗi thường gặp

1. **Nhầm O(n + m) thành O(n):** Khi hàm nhận 2 input khác nhau, complexity phải tính cả hai.
   ```python
   def print_both(arr_a, arr_b):   # O(n + m), không phải O(n)
       for x in arr_a: print(x)
       for x in arr_b: print(x)
   ```

2. **Bỏ qua Space Complexity:** Đệ quy tốn O(n) stack space dù code trông gọn.

3. **Nhầm worst case của QuickSort:** Thường là O(n log n) nhưng worst case là O(n²) khi pivot luôn chọn min/max.

4. **`in` operator trong Python list là O(n):** `if x in list` → O(n). Dùng `set` nếu cần O(1).

### Câu hỏi phỏng vấn hay gặp

1. **"Complexity của đoạn code này là gì?"** — Đếm số vòng lặp lồng nhau và quan hệ của chúng với n.

2. **"Tối ưu từ O(n²) xuống O(n)?"** — Thường dùng HashMap để đổi time lấy space.

3. **"Space complexity của DFS đệ quy trên cây?"** — O(h) với h là chiều cao cây (call stack).

4. **"Tại sao O(2n) = O(n)?"** — Vì hằng số 2 không ảnh hưởng đến xu hướng tăng khi n → ∞.

5. **"Difference giữa O(log n) và O(n log n)?"** — O(log n): binary search. O(n log n): sort, hoặc binary search trong vòng lặp.

6. **"Amortized complexity của Python list append?"** — O(1) amortized, dù O(n) trong lần resize.

---

## 13. Dạng Bài Thường Gặp Trong Thi Cử / Phỏng Vấn

### Dạng 1: Tính Big O của vòng lặp lồng nhau

**Nhận dạng đề:** Đề cho đoạn code có nhiều vòng `for`/`while` lồng nhau, hỏi time complexity hoặc "đoạn code này chạy bao lâu?".

**Approach chuẩn:**
1. Xác định vòng ngoài chạy bao nhiêu lần theo n
2. Xác định vòng trong phụ thuộc vào vòng ngoài hay chạy độc lập
3. Lồng nhau → nhân; tuần tự → cộng; lấy dominant term
4. Chú ý vòng lặp nhân đôi (`j *= 2`) → O(log n), không phải O(n)

**Ví dụ LeetCode:** Valid Sudoku (Medium), Four Sum (Medium)

---

### Dạng 2: Phân tích Recursion bằng Recurrence Relation

**Nhận dạng đề:** Hàm đệ quy tự gọi với tham số nhỏ hơn; hỏi overall complexity hay "sẽ TLE không?".

**Approach chuẩn:**
1. Viết recurrence: `T(n) = aT(n/b) + O(f(n))`
2. Áp dụng Master Theorem: so sánh f(n) với n^(log_b a)
3. Với T(n) = T(n-1) + O(1) → O(n); T(n) = 2T(n/2) + O(n) → O(n log n)
4. Luôn tính thêm space của call stack

**Ví dụ LeetCode:** Pow(x, n) (Medium), Merge Sort implementation

---

### Dạng 3: Amortized Analysis — Dynamic Structures

**Nhận dạng đề:** Bài hỏi về chuỗi operations trên dynamic array, stack, hoặc CTDL tự resize — "average cost per operation là bao nhiêu?".

**Approach chuẩn:**
1. Phân biệt "đôi khi tốn O(n)" vs "thường xuyên O(1)"
2. Tổng cost cho n operations / n = amortized cost
3. Dynamic array: resize tốn O(n) sau mỗi n appends → amortized O(1) per append
4. Nếu đề hỏi worst-case single operation thì vẫn phải nói O(n)

**Ví dụ LeetCode:** Min Stack (Easy), Design Dynamic Array

---

### Dạng 4: Chọn CTDL tối ưu cho bài toán

**Nhận dạng đề:** "Hệ thống cần thực hiện X, Y, Z operations — nên dùng CTDL nào?" hoặc "tối ưu solution này".

**Approach chuẩn:**
1. Liệt kê tất cả operations cần và tần suất dùng
2. So sánh: Array O(1) access / HashMap O(1) lookup / Heap O(log n) extract-min / BST O(log n) tất cả
3. Chọn CTDL mà operation quan trọng nhất (bottleneck) có complexity tốt nhất
4. Xét tradeoff space vs time

**Ví dụ LeetCode:** LRU Cache (Medium), LFU Cache (Hard)

---

### Dạng 5: Space Complexity với Đệ Quy — Bẫy Call Stack

**Nhận dạng đề:** Hỏi space complexity của thuật toán đệ quy — thường có bẫy người làm quên tính call stack.

**Approach chuẩn:**
1. Auxiliary space = bộ nhớ ngoài input, không tính stack
2. Call stack depth = độ sâu đệ quy tối đa
3. Balanced tree: stack O(log n); skewed tree: O(n)
4. Fibonacci naive: time O(2ⁿ) nhưng space chỉ O(n) call stack (không phải O(2ⁿ))

**Ví dụ LeetCode:** Maximum Depth of Binary Tree (Easy), Path Sum II (Medium)

---

### Dạng 6: Complexity Ẩn — String Concatenation và List Slicing

**Nhận dạng đề:** Code trông như O(n) nhưng thực ra O(n²) do `result += s` hoặc `arr[l:r]` bên trong loop.

**Approach chuẩn:**
1. `result += s` trong loop → mỗi lần tạo string mới, copy O(len) → tổng O(n²)
2. Dùng `"".join(parts)` để O(n)
3. `arr[l:r]` trong mỗi đệ quy → O(n) mỗi lần → thêm O(n) vào complexity
4. `x in list` → O(n); `x in set` → O(1)

**Ví dụ LeetCode:** Reverse String (Easy), Group Anagrams (Medium)

---

### Dạng 7: Multi-Variable Complexity — O(n·m) hay O(n²)?

**Nhận dạng đề:** Bài có hai input độc lập (n và m), nested loop với mỗi dimension — hỏi complexity.

**Approach chuẩn:**
1. Khi n ≠ m phải viết O(n·m), không được viết O(n²)
2. String matching: pattern length m, text length n → O(n·m) naive
3. Graph: V vertices, E edges → O(V+E), không phải O(V²)
4. 2D matrix m×n: O(m·n) để duyệt hết

**Ví dụ LeetCode:** Edit Distance (Medium), Number of Islands (Medium)

---

### Dạng 8: Worst vs Average — HashMap Gotcha

**Nhận dạng đề:** Hỏi về worst case của HashMap, hoặc "solution dùng dict của bạn có guarantee O(1) không?".

**Approach chuẩn:**
1. HashMap average O(1) nhưng worst case O(n) khi tất cả keys hash collision
2. Python 3.3+ thêm hash randomization để phòng hash flooding attack
3. Nếu đề yêu cầu worst-case O(1) guaranteed phải dùng CTDL khác
4. Phân biệt expected O(1) (probabilistic) vs guaranteed O(1)

**Ví dụ LeetCode:** Two Sum (Easy — tại sao dùng dict), Design HashMap (Easy)

---

### Dạng 9: So sánh Thuật Toán — Khi Nào Slow Thắng Fast

**Nhận dạng đề:** "Tại sao trong thực tế O(n²) insertion sort có thể nhanh hơn O(n log n) merge sort với n nhỏ?" hoặc "constant factor là gì?".

**Approach chuẩn:**
1. Constant factor: O(n²) với constant 1 vs O(n log n) với constant 10
2. Cache behavior: linear scan cache-friendly hơn random access
3. Breakeven: thực nghiệm thường n < 20-50 thì O(n²) nhanh hơn
4. Timsort = insertion sort cho small runs + merge sort cho large → hybrid

**Ví dụ LeetCode:** Sort Colors (Medium — counting sort O(n) thắng O(n log n))

---

### Dạng 10: Lower Bound của Bài Toán — Comparison-Based Sorting

**Nhận dạng đề:** "Tại sao không thể sort nhanh hơn O(n log n)?" hoặc "khi nào có thể sort O(n)?".

**Approach chuẩn:**
1. Decision tree: n! permutations cần ít nhất log₂(n!) ≈ n log n comparisons → lower bound
2. Counting sort: O(n+k) khi biết key là integer trong range [0,k]
3. Radix sort: O(n·d) với d = số digits
4. Điều kiện sort O(n): phải biết thêm thông tin về input (không arbitrary comparison)

**Ví dụ LeetCode:** Sort Colors (Medium — counting sort), Maximum Gap (Hard — radix sort)
