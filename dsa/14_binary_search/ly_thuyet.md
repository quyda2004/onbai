# Binary Search — Tìm Kiếm Nhị Phân

## 1. Giải thích cho người mới hoàn toàn

Bạn chơi trò "đoán số" với một người bạn. Bạn ấy nghĩ ra một số từ 1 đến 100. Bạn đoán, bạn ấy chỉ trả lời "cao hơn" hoặc "thấp hơn".

**Cách ngây thơ:** Đoán 1, 2, 3, ... mãi đến khi trúng. Worst case 100 lần đoán.

**Cách thông minh (binary search):**
- Đoán 50. "Thấp hơn" → số nằm trong [1, 49].
- Đoán 25. "Cao hơn" → số nằm trong [26, 49].
- Đoán 37. "Cao hơn" → [38, 49].
- Đoán 43. "Thấp hơn" → [38, 42].
- Đoán 40. "Cao hơn" → [41, 42].
- Đoán 41. "Trúng!"

Chỉ 6 lần đoán cho 100 số. Vì sao? Mỗi lần đoán **loại bỏ một nửa** không gian còn lại. 100 → 50 → 25 → 12 → 6 → 3 → 1. Số lần đoán ≈ log₂(100) ≈ 7.

Với 1 triệu số: log₂(1.000.000) ≈ 20 lần. Với 1 tỷ: 30 lần. Đó là sức mạnh của log.

### Điều kiện then chốt

Bạn chỉ chơi được trò này nếu **các con số được sắp xếp** theo thứ tự. Nếu bạn ấy ghi 100 số lung tung, "cao hơn" và "thấp hơn" không giúp loại bỏ gì cả.

→ **Binary search bắt buộc có cấu trúc đã sort** (hoặc có tính monotonic).

---

## 2. Giải thích nâng cao cho người chuyên ngành

**Binary Search** là kỹ thuật chia khoảng tìm kiếm thành 2 phần đều nhau, loại bỏ một nửa dựa trên kiểm tra điều kiện ở giữa. Áp dụng cho mọi không gian tìm kiếm **đơn điệu** (monotonic) — không nhất thiết phải là mảng sorted.

### Các biến thể quan trọng

**1. Vanilla — tìm chính xác giá trị**
- Tìm chỉ số của `target` trong mảng sorted. Trả về -1 nếu không có.

**2. Lower bound / Upper bound (bounded search)**
- **Lower bound**: chỉ số đầu tiên `i` sao cho `arr[i] >= target`.
- **Upper bound**: chỉ số đầu tiên `i` sao cho `arr[i] > target`.
- Hữu ích cho: đếm số lượng phần tử ≤ x, tìm điểm chèn, range query.
- C++: `std::lower_bound`, `std::upper_bound`. Python: `bisect.bisect_left`, `bisect.bisect_right`.

**3. Binary search trên answer space**
- Đây là dạng "khó" và mạnh mẽ nhất. Không có mảng sorted sẵn, mà bài toán có:
  - Một khoảng giá trị answer khả thi [lo, hi].
  - Một hàm kiểm tra `check(x)` đơn điệu (nếu check(x) = True thì check(x+1) cũng = True, hoặc ngược lại).
- Bisect trên answer: tìm x nhỏ nhất sao cho check(x) = True.
- Ví dụ:
  - Capacity to Ship Packages within D Days.
  - Koko Eating Bananas.
  - Split Array Largest Sum.
  - Median of Two Sorted Arrays (kinh điển).

**4. Binary search trên rotated sorted array**
- Mảng sorted bị xoay quanh một điểm pivot. Tại mỗi bước, xác định nửa nào "sorted thuần", kiểm tra target có nằm trong nửa đó không → quyết định đi tiếp nửa nào.

**5. Binary search trên 2D matrix sorted**
- Coi matrix m×n như mảng 1D độ dài m*n (mapping `i → (i//n, i%n)`).
- Hoặc đi từ góc trên-phải/dưới-trái với 2 con trỏ (search in 2D matrix II).

### Các pattern code chuẩn

**Pattern 1: Closed interval [lo, hi]**
```
lo, hi = 0, n - 1
while lo <= hi:
    mid = (lo + hi) // 2
    if arr[mid] == target: return mid
    elif arr[mid] < target: lo = mid + 1
    else: hi = mid - 1
return -1
```

**Pattern 2: Half-open interval [lo, hi) — lower bound**
```
lo, hi = 0, n  # hi = n cho phép trả về n nếu mọi phần tử < target
while lo < hi:
    mid = (lo + hi) // 2
    if arr[mid] < target: lo = mid + 1
    else: hi = mid
return lo  # lower bound
```

**Pattern 3: Bisect trên answer**
```
lo, hi = min_answer, max_answer
while lo < hi:
    mid = (lo + hi) // 2
    if check(mid): hi = mid
    else: lo = mid + 1
return lo  # nhỏ nhất sao cho check = True
```

### Trade-off và lưu ý

- **Overflow `(lo + hi) // 2`**: trong Java/C++ với int 32-bit, lo+hi có thể overflow. Dùng `lo + (hi - lo) // 2`.
- **Cập nhật `lo = mid` thay vì `lo = mid + 1`**: dễ gây vòng lặp vô hạn khi `lo` và `hi` cách nhau 1. Phải cẩn thận condition.
- **Đệ quy vs Iterative**: iterative thường ưa thích hơn — không có recursion overhead, O(1) space.
- **Floating-point binary search**: dùng cho bài continuous (find sqrt, find median). Điều kiện dừng `hi - lo < epsilon` thay vì `lo < hi`.

### Phân tích complexity

Mỗi bước giảm không gian tìm kiếm đi 1/2 → số bước = log₂(n). Mỗi bước O(1) (truy cập array) hoặc O(k) cho check phức tạp trên answer space → tổng O(k log n).

---

## 3. Định nghĩa chính xác

**Binary Search**: Thuật toán tìm kiếm trên không gian đơn điệu/sorted hoạt động bằng cách chia đôi không gian tìm kiếm tại mỗi bước, dựa vào kết quả so sánh với điểm giữa để loại bỏ một nửa.

**Monotonic function (predicate)**: Hàm `f: X → {True, False}` sao cho tồn tại điểm chia: với mọi x ≤ k, f(x) cùng giá trị; với mọi x > k, f(x) cùng giá trị (khác). Binary search trên answer dùng tính chất này.

**Lower bound**: `LB(arr, x)` = chỉ số nhỏ nhất `i` sao cho `arr[i] >= x` (hoặc `len(arr)` nếu không có).

**Upper bound**: `UB(arr, x)` = chỉ số nhỏ nhất `i` sao cho `arr[i] > x`.

**Đếm số lượng = x**: `UB(arr, x) - LB(arr, x)`.

---

## 4. Bảng Độ phức tạp đầy đủ

| Thao tác | Best | Average | Worst | Auxiliary Space | Ghi chú |
|----------|------|---------|-------|-----------------|---------|
| Binary search vanilla | O(1) | O(log n) | O(log n) | O(1) iter / O(log n) recur | Best khi target = arr[mid] đầu tiên |
| Lower bound / Upper bound | O(1) | O(log n) | O(log n) | O(1) | Best hiếm khi đạt được nếu cần bound chuẩn |
| Search trong rotated sorted array | O(1) | O(log n) | O(log n) | O(1) | Best khi pivot = mid lần đầu |
| Find peak element | O(1) | O(log n) | O(log n) | O(1) | Yêu cầu arr[i] ≠ arr[i+1] |
| Search 2D matrix (sorted rows + cols) | O(1) | O(m + n) | O(m + n) | O(1) | Two pointers từ corner |
| Search 2D matrix (true sorted) | O(1) | O(log(mn)) | O(log(mn)) | O(1) | Map sang 1D |
| Median of 2 sorted arrays | O(1) | O(log(min(m,n))) | O(log(min(m,n))) | O(1) | Binary search partition |
| Bisect trên answer | O(log(range) × cost(check)) | tương tự | tương tự | O(1) | range = max - min |
| Sort + binary search 1 query | O(n log n) | O(n log n) | O(n log n) | O(log n) | Build + 1 query |
| Sort + binary search Q queries | O((n + Q) log n) | tương tự | tương tự | O(log n) | Hiệu quả với Q lớn |

**So sánh với linear search**:

| n | Linear O(n) | Binary O(log n) |
|---|-------------|-----------------|
| 100 | 100 ops | 7 ops |
| 10,000 | 10,000 ops | 14 ops |
| 1,000,000 | 1,000,000 ops | 20 ops |
| 1,000,000,000 | 10⁹ ops | 30 ops |

---

## 5. Code mẫu

### Vanilla binary search

```python
def binary_search(arr, target):
    """Trả về chỉ số của target trong arr (sorted), hoặc -1."""
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2  # tránh overflow
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1

print(binary_search([1, 3, 5, 7, 9, 11], 7))  # 3
print(binary_search([1, 3, 5, 7, 9, 11], 6))  # -1
```

### Lower bound (Python bisect.bisect_left)

```python
def lower_bound(arr, target):
    """Chỉ số đầu tiên i sao cho arr[i] >= target. Trả về len(arr) nếu không có."""
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid
    return lo

# Demo
arr = [1, 2, 4, 4, 4, 5, 7]
print(lower_bound(arr, 4))  # 2 — chỉ số đầu tiên có giá trị >= 4
print(lower_bound(arr, 6))  # 6 — chỉ số đầu tiên có giá trị >= 6
print(lower_bound(arr, 100))  # 7 — không có, trả về len
```

### Upper bound (Python bisect.bisect_right)

```python
def upper_bound(arr, target):
    """Chỉ số đầu tiên i sao cho arr[i] > target."""
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] <= target:
            lo = mid + 1
        else:
            hi = mid
    return lo

print(upper_bound([1, 2, 4, 4, 4, 5, 7], 4))  # 5

# Đếm số lần xuất hiện
def count_occurrences(arr, target):
    return upper_bound(arr, target) - lower_bound(arr, target)

print(count_occurrences([1, 2, 4, 4, 4, 5, 7], 4))  # 3
```

### Search in rotated sorted array

```python
def search_rotated(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        if arr[lo] <= arr[mid]:
            # Nửa trái sorted
            if arr[lo] <= target < arr[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:
            # Nửa phải sorted
            if arr[mid] < target <= arr[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return -1

print(search_rotated([4, 5, 6, 7, 0, 1, 2], 0))  # 4
print(search_rotated([4, 5, 6, 7, 0, 1, 2], 3))  # -1
```

### Binary search on answer — Koko Eating Bananas

```python
import math

def min_eating_speed(piles, h):
    """
    Koko ăn k chuối/giờ. Mỗi pile mất ceil(p/k) giờ. Tìm k nhỏ nhất ăn xong trong h giờ.
    """
    def hours_needed(k):
        return sum(math.ceil(p / k) for p in piles)

    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if hours_needed(mid) <= h:
            hi = mid   # mid khả thi, thử nhỏ hơn
        else:
            lo = mid + 1
    return lo

print(min_eating_speed([3, 6, 7, 11], 8))  # 4
```

### Find peak element

```python
def find_peak(nums):
    """Tìm 1 peak bất kỳ: nums[i] > nums[i-1] và nums[i] > nums[i+1]. O(log n)."""
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] < nums[mid + 1]:
            lo = mid + 1  # đi về phía tăng
        else:
            hi = mid      # mid có thể là peak, không bỏ
    return lo

print(find_peak([1, 2, 3, 1]))  # 2
```

### Floating-point binary search — sqrt

```python
def my_sqrt(x, eps=1e-9):
    if x < 1:
        lo, hi = x, 1.0
    else:
        lo, hi = 0.0, float(x)
    while hi - lo > eps:
        mid = (lo + hi) / 2
        if mid * mid > x:
            hi = mid
        else:
            lo = mid
    return lo

print(my_sqrt(2))   # ~1.4142135...
print(my_sqrt(10))  # ~3.1622776...
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Mảng/dữ liệu đã sort hoặc có tính monotonic.
- Bài toán có "answer space" với hàm check đơn điệu (Koko, Capacity, Split Array...).
- Cần tìm điểm chuyển (boundary) — first true / last false.
- Search trong matrix sorted, rotated array, peak element.
- Cần tìm trong stream/array khổng lồ với latency thấp (giảm từ O(n) xuống O(log n)).
- Build sorted structure 1 lần và query nhiều lần.

**Không dùng khi:**
- Dữ liệu chưa sort và sort không khả thi (vd dữ liệu thay đổi liên tục).
- Cần 1 query duy nhất trên dữ liệu chưa sort — O(n) linear thắng O(n log n) sort + O(log n) search.
- Hàm check không đơn điệu (không có ranh giới rõ ràng).
- Dữ liệu có duplicate phức tạp và yêu cầu hành vi đặc biệt — phải dùng lower/upper bound cẩn thận.
- Cần truy cập tuần tự (như linked list) — random access O(1) là điều kiện tiên quyết. Trên linked list, binary search vô nghĩa vì truy cập giữa O(n).

---

## 7. So sánh với các thuật toán liên quan

| Tiêu chí | Linear Search | Binary Search | Hash Table | BST |
|----------|---------------|---------------|------------|-----|
| Yêu cầu sort | Không | Có | Không | Tự duy trì |
| Search time | O(n) | O(log n) | O(1) avg | O(log n) balanced |
| Insert time | O(1) | O(n) (phải sort lại) | O(1) avg | O(log n) |
| Space | O(1) | O(1) | O(n) | O(n) |
| Ordered iteration | Có | Có | Không | Có |
| Range query | O(n) | O(log n + k) | O(n) | O(log n + k) |
| Khi nào tốt nhất | Mảng nhỏ, không sort | Sort sẵn, query nhiều | Lookup nhanh, không cần thứ tự | Update + query động |

**Binary Search vs Two Pointers**: Cả hai khai thác tính sorted. Binary search O(log n) cho 1 query đơn lẻ. Two pointers O(n) cho cả quá trình duyệt — phù hợp khi cần tất cả cặp/duyệt toàn mảng.

**Binary Search vs BFS/DFS**: BFS/DFS dùng cho không gian rời rạc tổng quát (graph). Binary search dùng cho không gian tuyến tính có thứ tự.

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Overflow khi `mid = (lo + hi) // 2`** trong Java/C++ với int 32-bit (Python không bị do int unbounded). Sửa: `mid = lo + (hi - lo) // 2`.

2. **Vòng lặp vô hạn** khi cập nhật `lo = mid` hoặc `hi = mid` không đúng pattern. Quy tắc: nếu `lo = mid`, phải dùng `mid = (lo + hi + 1) // 2` (làm tròn lên) tránh lo không đổi khi hi = lo + 1.

3. **Điều kiện `<` vs `<=`** trong vòng while: dùng nhầm gây miss case khi `lo = hi`.

4. **Khoảng đóng vs khoảng mở**: Pattern `[lo, hi]` vs `[lo, hi)` khác nhau về update logic. Phải nhất quán: nếu hi = len(arr), không truy cập arr[hi].

5. **Không xác định invariant rõ**: Trước mỗi vòng lặp, vùng tìm kiếm là gì? Câu trả lời nằm ở đâu? Viết comment trước khi code.

6. **Áp dụng cho mảng có duplicate mà cần "first occurrence"** mà dùng vanilla — kết quả ngẫu nhiên trong các giá trị bằng nhau. Phải dùng lower bound.

7. **Binary search trên answer mà chọn sai khoảng [lo, hi]** — bỏ sót answer hợp lệ. Khoảng phải bao gồm tất cả answer khả thi.

8. **Check function không monotonic** — binary search trả về kết quả ngẫu nhiên. Phải chứng minh monotonicity trước khi dùng.

9. **Floating-point: dùng `==` thay vì `epsilon`** — không bao giờ chạy đúng do sai số.

10. **Rotated array: nhầm điều kiện** `arr[lo] <= arr[mid]` (đúng cho nửa trái sorted) với `arr[lo] < arr[mid]` (sai khi có duplicate hoặc lo = mid).

---

## 9. Câu hỏi phỏng vấn hay gặp

- Implement binary search iterative và recursive — so sánh complexity.
- Sự khác nhau giữa lower bound và upper bound, viết cả hai.
- Đếm số lần xuất hiện của 1 giá trị trong sorted array — O(log n).
- Search trong rotated sorted array (có và không có duplicate).
- Find peak element — tại sao binary search hoạt động được (không monotonic toàn cục)?
- Median of two sorted arrays — O(log(min(m,n))).
- Koko Eating Bananas / Capacity to Ship — binary search trên answer.
- Aggressive Cows / Split Array Largest Sum — bisect on answer kinh điển.
- Tìm sqrt(x) integer — vanilla; tìm sqrt(x) floating với precision — float binary search.
- Search 2D matrix — phương án map sang 1D, phương án two pointers từ corner.
- First Bad Version — pattern lower bound trên API call.
- Tại sao mỗi bước phải loại "ít nhất" 1 phần tử (không phải nửa)? Đảm bảo termination.
- Sự khác biệt khi mảng sorted có duplicate (search vs bound).
- Khi nào nên sort trước khi binary search? Trade-off với hash map.
- Galloping search (exponential + binary) cho mảng vô hạn.
