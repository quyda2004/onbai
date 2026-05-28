# Prefix Sum (Tổng Tiền Tố)

---

## 1. Vấn đề đặt ra

Có một mảng số nguyên. Bạn cần trả lời hàng nghìn câu hỏi dạng: "Tổng các phần tử từ vị trí 3 đến vị trí 7 là bao nhiêu?" Mỗi lần hỏi, nếu cứ duyệt lại từ đầu, tốn O(n) mỗi câu hỏi. Với 10,000 câu hỏi và mảng 10,000 phần tử — 10⁸ phép tính, quá chậm.

Prefix Sum giải quyết bằng cách: bỏ O(n) vào bước tiền xử lý một lần duy nhất, để mỗi câu hỏi sau đó chỉ tốn O(1).

---

## 2. Giải thích bằng hình ảnh đời thường

Hãy tưởng tượng bạn có cuốn sổ ghi điểm học sinh qua 10 buổi. Cần trả lời hàng trăm câu hỏi: "Tổng điểm từ buổi 3 đến buổi 7 là bao nhiêu?"

**Cách ngây thơ:** Mỗi lần hỏi, lại cộng từng số từ buổi 3 đến 7 — tốn 5 phép cộng. 1000 câu hỏi → 5000 phép cộng.

**Cách Prefix Sum:** Trước khi trả lời bất kỳ câu hỏi nào, tính sẵn **tổng tích lũy**:
- Tổng tích lũy sau buổi 1 = điểm buổi 1
- Tổng tích lũy sau buổi 2 = buổi 1 + buổi 2
- Tổng tích lũy sau buổi 3 = buổi 1 + buổi 2 + buổi 3
- ...

Để trả lời "tổng từ buổi 3 đến buổi 7":
→ `tổng_đến_buổi_7 - tổng_đến_buổi_2 = 1 phép trừ duy nhất!`

Ứng dụng thực tế: ngân hàng tính tổng giao dịch theo kỳ, game tính điểm theo màn chơi, phân tích dữ liệu tính trung bình một đoạn.

---

## 3. Khái niệm cơ bản

**Prefix Sum Array** là mảng `P` mà `P[i]` lưu tổng tất cả phần tử từ đầu mảng đến trước vị trí `i`.

Công thức:
```
P[0] = 0                      (sentinel — tránh xử lý đặc biệt)
P[i] = P[i-1] + A[i-1]
```

Query tổng từ `l` đến `r` (0-indexed, inclusive):
```
sum(l, r) = P[r+1] - P[l]
```

**Ví dụ nhỏ nhất:**
```
A = [2, 4, 1, 3]
P = [0, 2, 6, 7, 10]

Tổng A[1..2] = P[3] - P[1] = 7 - 2 = 5   (kiểm tra: 4 + 1 = 5 ✓)
```

---

## 4. Ví dụ từng bước (step-by-step)

**Bài toán:** Mảng `A = [3, 1, 4, 1, 5]`. Tính tổng từ index 1 đến 3.

**Bước 1 — Xây dựng Prefix Sum:**
```
P[0] = 0
P[1] = 0 + 3 = 3
P[2] = 3 + 1 = 4
P[3] = 4 + 4 = 8
P[4] = 8 + 1 = 9
P[5] = 9 + 5 = 14
```

Tại sao thêm P[0] = 0? Để công thức `P[r+1] - P[l]` hoạt động khi `l = 0` mà không cần if/else đặc biệt.

**Bước 2 — Query:**
```
sum(1, 3) = P[4] - P[1] = 9 - 3 = 6
Kiểm tra: A[1]+A[2]+A[3] = 1+4+1 = 6 ✓
```

**Tại sao đúng?**
```
P[4] = A[0]+A[1]+A[2]+A[3]
P[1] = A[0]
P[4] - P[1] = A[1]+A[2]+A[3]  ← đúng là những gì ta cần!
```

---

## 5. Code Python đơn giản nhất

```python
def build_prefix(arr):
    """Xây dựng prefix sum — O(n) time, O(n) space"""
    n = len(arr)
    prefix = [0] * (n + 1)   # +1 cho sentinel ở đầu
    for i in range(n):
        prefix[i + 1] = prefix[i] + arr[i]
    return prefix

def query(prefix, l, r):
    """Trả về tổng arr[l..r] — O(1)"""
    return prefix[r + 1] - prefix[l]

# Test
arr = [3, 1, 4, 1, 5]
p = build_prefix(arr)
print(p)                  # [0, 3, 4, 8, 9, 14]
print(query(p, 1, 3))     # 6 (= 1+4+1)
print(query(p, 0, 4))     # 14 (tổng toàn mảng)
```

---

## 6. Mở rộng dần

### Subarray Sum Equals K — kết hợp HashMap

```python
def count_subarrays_sum_k(arr, k):
    """
    Đếm số subarray có tổng = k.
    Insight: prefix[j] - prefix[i] = k  =>  prefix[i] = prefix[j] - k
    Dùng HashMap để tìm prefix[i] cần thiết trong O(1).
    Time: O(n), Space: O(n)
    """
    count = 0
    current = 0
    seen = {0: 1}   # sentinel: prefix sum 0 xuất hiện 1 lần trước khi bắt đầu

    for num in arr:
        current += num
        needed = current - k
        count += seen.get(needed, 0)
        seen[current] = seen.get(current, 0) + 1

    return count

print(count_subarrays_sum_k([1, 1, 1], 2))   # 2
print(count_subarrays_sum_k([1, 2, 3], 3))   # 2 ([1,2] và [3])
```

### Difference Array — range update O(1)

```python
def range_update(diff, l, r, val):
    """Cộng val vào tất cả phần tử từ l đến r — O(1)"""
    diff[l] += val
    if r + 1 < len(diff):
        diff[r + 1] -= val

def build_result(diff):
    """Rebuild mảng từ difference array — O(n)"""
    result = diff[:]
    for i in range(1, len(result)):
        result[i] += result[i - 1]
    return result

# Ví dụ
n = 6
diff = [0] * n
range_update(diff, 1, 4, 3)   # cộng 3 vào index 1..4
range_update(diff, 2, 5, 1)   # cộng 1 vào index 2..5
print(build_result(diff))     # [0, 3, 4, 4, 4, 1]
```

---

## 7. Độ phức tạp

### 1D Prefix Sum

| Thao tác | Best | Average | Worst | Space (Aux) | Ghi chú |
|----------|------|---------|-------|-------------|---------|
| Build prefix array | O(n) | O(n) | O(n) | O(n) | Luôn phải duyệt hết |
| Query range sum | O(1) | O(1) | O(1) | O(1) | 1 phép trừ |
| Update 1 phần tử (rebuild) | O(n) | O(n) | O(n) | O(1) extra | Rebuild từ vị trí thay đổi |

### So sánh Naive vs Prefix Sum

| Tiêu chí | Naive | Prefix Sum |
|----------|-------|------------|
| Preprocessing | O(1) | O(n) |
| Mỗi query | O(n) | O(1) |
| Q queries | O(Q × n) | O(n + Q) |
| Nên dùng khi | Q = 1 | Q >= 2 |

### Difference Array

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| Range update [l,r] += v | O(1) | Chỉ 2 điểm |
| Build result | O(n) | Prefix sum trên D |

### 2D Prefix Sum

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| Build | O(m × n) | m hàng, n cột |
| Query hình chữ nhật | O(1) | 4 phép tính |
| Space | O(m × n) | Ma trận prefix |

---

## 8. Patterns & Variations

### Pattern 1: 2D Prefix Sum

```python
def build_prefix_2d(matrix):
    m, n = len(matrix), len(matrix[0])
    P = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            P[i][j] = (matrix[i-1][j-1]
                      + P[i-1][j]      # tổng hàng trên
                      + P[i][j-1]      # tổng cột trái
                      - P[i-1][j-1])   # trừ phần bị cộng 2 lần
    return P

def query_2d(P, r1, c1, r2, c2):
    return (P[r2+1][c2+1] - P[r1][c2+1]
            - P[r2+1][c1] + P[r1][c1])
```

### Pattern 2: XOR Prefix

```python
# XOR tự triệt tiêu: a XOR a = 0
# XOR prefix hoạt động giống sum prefix
def xor_range(arr, l, r):
    prefix = [0] * (len(arr) + 1)
    for i, x in enumerate(arr):
        prefix[i+1] = prefix[i] ^ x
    return prefix[r+1] ^ prefix[l]
```

### Pattern 3: Product Array Except Self

```python
def product_except_self(arr):
    """LeetCode 238 — prefix + suffix product — O(n) time, O(1) extra"""
    n = len(arr)
    result = [1] * n
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= arr[i]
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= arr[i]
    return result
```

---

## 9. Code nâng cao / Optimized

```python
from itertools import accumulate

class PrefixSum:
    """Production-level 1D Prefix Sum"""
    def __init__(self, arr):
        # itertools.accumulate nhanh hơn vòng lặp thủ công
        self._prefix = [0] + list(accumulate(arr))

    def query(self, l: int, r: int) -> int:
        """Tổng arr[l..r] inclusive — O(1)"""
        return self._prefix[r + 1] - self._prefix[l]


class PrefixSum2D:
    """Production-level 2D Prefix Sum"""
    def __init__(self, matrix):
        m, n = len(matrix), len(matrix[0])
        p = [[0] * (n + 1) for _ in range(m + 1)]
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                p[i][j] = (matrix[i-1][j-1]
                          + p[i-1][j] + p[i][j-1] - p[i-1][j-1])
        self._p = p

    def query(self, r1, c1, r2, c2) -> int:
        """Tổng hình chữ nhật (r1,c1)→(r2,c2) — O(1)"""
        p = self._p
        return p[r2+1][c2+1] - p[r1][c2+1] - p[r2+1][c1] + p[r1][c1]


if __name__ == "__main__":
    ps = PrefixSum([3, 1, 4, 1, 5, 9, 2, 6])
    print(ps.query(2, 5))   # 19 (= 4+1+5+9)

    matrix = [[1,2,3],[4,5,6],[7,8,9]]
    ps2 = PrefixSum2D(matrix)
    print(ps2.query(1, 1, 2, 2))  # 28 (= 5+6+8+9)
```

---

## 10. Khi nào dùng / Không dùng

**Dùng Prefix Sum khi:**
- Nhiều range sum queries trên mảng tĩnh (không thay đổi)
- Bài toán "subarray sum = k" — kết hợp với HashMap
- Image processing: tính tổng pixel trong vùng chữ nhật (2D prefix sum)
- Kiểm tra nhanh điều kiện tổng của subarray

**Không dùng Prefix Sum khi:**
- Mảng thay đổi thường xuyên → dùng Fenwick Tree / Segment Tree
- Chỉ 1 query duy nhất → loop thẳng, không cần build
- Cần range max/min → Sparse Table (static) hoặc Segment Tree (dynamic)
- Memory bị giới hạn — prefix array tốn O(n) extra space

---

## 11. So sánh với các cấu trúc liên quan

| Cấu trúc | Build | Query | Update | Khi dùng |
|----------|-------|-------|--------|----------|
| Naive loop | O(1) | O(n) | O(1) | Q = 1 |
| **Prefix Sum** | **O(n)** | **O(1)** | **O(n) rebuild** | **Static, nhiều queries** |
| Difference Array | O(n) | O(n) rebuild | O(1) | Nhiều range updates |
| Fenwick Tree | O(n log n) | O(log n) | O(log n) | Dynamic range sum |
| Segment Tree | O(n) | O(log n) | O(log n) | Dynamic, queries phức tạp |
| Sparse Table | O(n log n) | O(1) | Không hỗ trợ | Range max/min, static |

**Quy tắc chọn nhanh:**
- Static + range sum → Prefix Sum
- Dynamic + range sum → Fenwick Tree
- Range max/min static → Sparse Table
- Nhiều range updates + ít queries → Difference Array

---

## 12. Pitfalls & Câu hỏi phỏng vấn

### Pitfalls thường gặp

**Pitfall 1: Off-by-one trong indexing**
```python
# SAI
for i in range(1, n + 1):
    prefix[i] = prefix[i-1] + arr[i]   # arr[i] out of range khi i=n!
# ĐÚNG
for i in range(1, n + 1):
    prefix[i] = prefix[i-1] + arr[i-1]
```

**Pitfall 2: Quên sentinel {0: 1} trong "subarray sum = k"**
```python
seen = {}        # SAI — bỏ qua subarray bắt đầu từ index 0
seen = {0: 1}    # ĐÚNG
```

**Pitfall 3: Difference array — quên kiểm tra boundary**
```python
diff[r + 1] -= val   # IndexError nếu r = n-1
# FIX: tạo diff size n+1, hoặc if r+1 < n
```

**Pitfall 4: Prefix sum không dùng cho range max/min**
```python
# max(A[l..r]) ≠ P[r+1] - P[l] — sai hoàn toàn!
# Dùng Sparse Table hoặc Segment Tree
```

### Câu hỏi phỏng vấn hay gặp

**Q1: Viết hàm range sum O(1) sau O(n) preprocessing.**
→ Build prefix array, dùng `P[r+1] - P[l]`.

**Q2: LeetCode 560 — Subarray Sum Equals K.**
→ Prefix sum + HashMap. Tại sao cần `{0: 1}` ban đầu?
→ Để handle subarray bắt đầu từ index 0.

**Q3: LeetCode 304 — Range Sum Query 2D.**
→ 2D prefix sum. Inclusion-exclusion: `+P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]`.

**Q4: Khi nào dùng Difference Array thay vì Prefix Sum?**
→ Nhiều range updates, ít queries. Difference Array O(1) update; Prefix Sum O(1) query.

**Q5: Prefix Sum áp dụng cho XOR được không?**
→ Có. `XOR(l,r) = prefix[r+1] XOR prefix[l]` vì XOR tự triệt tiêu.

**Q6: Tại sao không dùng cho dynamic array?**
→ Update A[i] → toàn bộ P[i+1..n] đổi → O(n) rebuild. Dùng Fenwick Tree: O(log n).

**Q7: LeetCode 238 — Product of Array Except Self.**
→ Prefix product trái + suffix product phải. Không dùng phép chia. O(n) time, O(1) extra space.

### Cơ chế bên trong

Prefix sum là kỹ thuật **precomputation** — bỏ chi phí tính toán vào giai đoạn build để giai đoạn query trở nên O(1).

**Memory model:**
```
Array A:      [3, 1, 4, 1, 5, 9, 2, 6]
Prefix P:  [0, 3, 4, 8, 9, 14, 23, 25, 31]
            ^
            P[0] = 0 là sentinel value (off-by-one prevention)
```

`P[i]` lưu `A[0] + A[1] + ... + A[i-1]`.
Range sum `[l, r]` (inclusive) = `P[r+1] - P[l]`.

**Tại sao thêm sentinel P[0] = 0?**  
Tránh xử lý đặc biệt khi l = 0. Không có sentinel, ta cần if/else. Với sentinel, công thức nhất quán.

### Trade-off

| Yếu tố | Naive | Prefix Sum |
|--------|-------|------------|
| Build time | O(1) | O(n) |
| Query time | O(n) | O(1) |
| Space | O(1) extra | O(n) extra |
| Update array | O(1) | O(n) rebuild |

**Prefix sum tốt khi:** nhiều queries, array ít thay đổi (static array).  
**Tệ khi:** array thay đổi thường xuyên → dùng Fenwick Tree / Segment Tree.

### Difference Array — đảo ngược prefix sum

Nếu cần **range update** (cộng giá trị v vào tất cả phần tử từ l đến r), thì Difference Array giải quyết trong O(1) update, O(n) build lại.

```
D[l] += v
D[r+1] -= v
```

Sau tất cả updates, chạy prefix sum trên D để thu được mảng kết quả.

### 2D Prefix Sum

Mở rộng cho ma trận — precompute `P[i][j]` = tổng của hình chữ nhật từ (0,0) đến (i-1,j-1).

Công thức build:
```
P[i][j] = A[i-1][j-1] + P[i-1][j] + P[i][j-1] - P[i-1][j-1]
```

Query hình chữ nhật (r1,c1) đến (r2,c2):
```
P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]
```

Nguyên tắc inclusion-exclusion: cộng lại phần bị trừ 2 lần.

### Subarray Sum Equals K

Bài toán: đếm số subarray có tổng bằng k.

**Insight:** prefix[j] - prefix[i] = k → prefix[i] = prefix[j] - k.

Dùng HashMap lưu số lần xuất hiện của từng prefix sum. Scan một lần O(n).

```python
count += hashmap.get(current_prefix - k, 0)
```

---

## 3. Định nghĩa chính xác

**Prefix Sum Array** (Mảng tổng tiền tố): Cho mảng `A[0..n-1]`, prefix sum array `P[0..n]` được định nghĩa:
- `P[0] = 0`
- `P[i] = P[i-1] + A[i-1]` với `1 ≤ i ≤ n`

**Range Sum Query:** `sum(l, r) = P[r+1] - P[l]` với `0 ≤ l ≤ r < n`.

**Difference Array** (Mảng hiệu): Cho mảng `A[0..n-1]`, difference array `D[0..n]`:
- `D[0] = A[0]`
- `D[i] = A[i] - A[i-1]` với `1 ≤ i < n`

Tính lại A từ D bằng cách chạy prefix sum trên D.

---

## 4. Bảng Độ Phức Tạp Đầy Đủ

### 1D Prefix Sum

| Thao tác | Best | Average | Worst | Điều kiện |
|----------|------|---------|-------|-----------|
| Build prefix array | O(n) | O(n) | O(n) | Luôn phải duyệt toàn bộ |
| Query range sum | O(1) | O(1) | O(1) | Phép trừ đơn giản |
| Update một phần tử + rebuild | O(n) | O(n) | O(n) | Phải rebuild từ vị trí thay đổi |
| Point update (không rebuild) | O(1) | O(1) | O(1) | Chỉ update A, không update P |

**Space Complexity:**
- Auxiliary space: O(n) cho mảng prefix
- Total space: O(n) (array gốc) + O(n) (prefix) = O(n)

### 2D Prefix Sum

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| Build | O(m×n) | m rows, n cols |
| Query rectangle | O(1) | 4 phép tính |
| Space | O(m×n) | Ma trận prefix |

### Difference Array

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| Range update [l,r] += v | O(1) | Chỉ update D[l] và D[r+1] |
| Build result array | O(n) | Chạy prefix sum trên D |
| Point query sau build | O(1) | |

### So sánh với Naive Approach

| | Naive | Prefix Sum |
|-|-------|-----------|
| Preprocessing | O(1) | O(n) |
| Single query | O(n) | O(1) |
| Q queries total | O(Q×n) | O(n + Q) |
| Break-even point | - | Q = 1 |

Với Q queries và n phần tử, prefix sum thắng khi Q > 1.

---

## 5. Code mẫu Python

```python
# ============================================================
# PREFIX SUM - 1D
# ============================================================

def build_prefix_sum(arr):
    """
    Xây dựng mảng prefix sum từ mảng input.
    P[0] = 0 (sentinel)
    P[i] = A[0] + A[1] + ... + A[i-1]
    """
    n = len(arr)
    prefix = [0] * (n + 1)  # +1 cho sentinel ở đầu
    for i in range(n):
        prefix[i + 1] = prefix[i] + arr[i]
    return prefix


def range_sum(prefix, l, r):
    """
    Trả về tổng arr[l..r] (inclusive) trong O(1).
    Công thức: P[r+1] - P[l]
    """
    return prefix[r + 1] - prefix[l]


# Demo 1D
arr = [3, 1, 4, 1, 5, 9, 2, 6]
prefix = build_prefix_sum(arr)
print("Array:", arr)
print("Prefix:", prefix)
print("Sum [2..5]:", range_sum(prefix, 2, 5))  # 4+1+5+9 = 19
print("Sum [0..3]:", range_sum(prefix, 0, 3))  # 3+1+4+1 = 9


# ============================================================
# PREFIX SUM - 2D
# ============================================================

def build_prefix_2d(matrix):
    """
    Xây dựng 2D prefix sum.
    P[i][j] = tổng hình chữ nhật từ (0,0) đến (i-1,j-1).
    Dùng inclusion-exclusion để tránh cộng trùng.
    """
    m = len(matrix)
    n = len(matrix[0])
    # +1 ở cả 2 chiều cho sentinel row/col
    prefix = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            prefix[i][j] = (matrix[i-1][j-1]
                           + prefix[i-1][j]      # tổng hàng trên
                           + prefix[i][j-1]      # tổng cột trái
                           - prefix[i-1][j-1])   # trừ phần bị cộng 2 lần
    return prefix


def range_sum_2d(prefix, r1, c1, r2, c2):
    """
    Tổng hình chữ nhật (r1,c1) đến (r2,c2) inclusive, trong O(1).
    Dùng inclusion-exclusion: cộng lại góc trên-trái bị trừ 2 lần.
    """
    return (prefix[r2+1][c2+1]
            - prefix[r1][c2+1]    # trừ phần trên
            - prefix[r2+1][c1]    # trừ phần trái
            + prefix[r1][c1])     # cộng lại góc bị trừ 2 lần


# Demo 2D
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
p2d = build_prefix_2d(matrix)
# Tổng hình chữ nhật (1,1) đến (2,2) = 5+6+8+9 = 28
print("\n2D Sum (1,1)-(2,2):", range_sum_2d(p2d, 1, 1, 2, 2))


# ============================================================
# DIFFERENCE ARRAY - RANGE UPDATE O(1)
# ============================================================

def range_update(diff, l, r, val):
    """
    Cộng val vào tất cả phần tử từ index l đến r.
    Chỉ cần 2 thao tác O(1) thay vì O(n).
    """
    diff[l] += val
    if r + 1 < len(diff):
        diff[r + 1] -= val


def get_result(diff):
    """
    Rebuild mảng kết quả từ difference array.
    Chạy prefix sum trên diff array.
    """
    result = diff[:]
    for i in range(1, len(result)):
        result[i] += result[i - 1]
    return result


# Demo Difference Array
arr = [0] * 8  # mảng ban đầu toàn 0
diff = arr[:]

range_update(diff, 1, 4, 3)   # cộng 3 vào index 1..4
range_update(diff, 2, 6, 2)   # cộng 2 vào index 2..6
range_update(diff, 0, 3, -1)  # trừ 1 từ index 0..3

result = get_result(diff)
print("\nDifference Array result:", result)
# Index: 0  1  2  3  4  5  6  7
# Ops:  -1 +3-1 +2-1 +2-1 +3+2 +2  +2   0
# Expected: [-1, 2, 4, 3, 5, 2, 2, 0]


# ============================================================
# SUBARRAY SUM EQUALS K — Prefix Sum + HashMap
# ============================================================

def subarray_sum_k(arr, k):
    """
    Đếm số subarray có tổng bằng k.
    
    Insight: prefix[j] - prefix[i] = k
             => prefix[i] = prefix[j] - k
    
    Dùng HashMap đếm số lần xuất hiện của từng prefix sum.
    Time: O(n), Space: O(n)
    """
    count = 0
    current_prefix = 0
    # Khởi tạo với {0: 1} — prefix sum = 0 đã xuất hiện 1 lần
    # (để handle subarray bắt đầu từ index 0)
    prefix_count = {0: 1}
    
    for num in arr:
        current_prefix += num
        # Tìm số subarray kết thúc tại đây có tổng = k
        needed = current_prefix - k
        count += prefix_count.get(needed, 0)
        # Ghi nhận prefix sum hiện tại
        prefix_count[current_prefix] = prefix_count.get(current_prefix, 0) + 1
    
    return count


# Demo
arr = [1, 1, 1]
print("\nSubarray sum = 2:", subarray_sum_k(arr, 2))  # 2: [1,1] ở vị trí 0-1 và 1-2

arr2 = [1, 2, 3, -3, 1]
print("Subarray sum = 3:", subarray_sum_k(arr2, 3))  # 3
```

---

## 6. Khi nào dùng / Không dùng

### Dùng Prefix Sum khi:
- Cần trả lời **nhiều range sum queries** trên mảng tĩnh (static array)
- Bài toán **subarray sum** với điều kiện — kết hợp với HashMap
- **Image processing**: tính tổng pixel trong vùng chữ nhật (2D prefix sum)
- **Competitive programming**: bài yêu cầu O(1) query sau O(n) preprocessing
- **Sliding window có tổng cố định**: prefix sum giúp kiểm tra nhanh

### Không dùng Prefix Sum khi:
- Array **thay đổi thường xuyên** → dùng Fenwick Tree (Binary Indexed Tree) hoặc Segment Tree
- Chỉ cần **1 query duy nhất** — không đáng build prefix array
- Cần **range max/min** thay vì range sum → Sparse Table hoặc Segment Tree
- Memory bị giới hạn chặt (prefix array dùng thêm O(n) space)

### Prefix Sum vs Sliding Window

| Tiêu chí | Prefix Sum | Sliding Window |
|----------|-----------|----------------|
| Window có fixed size | Cả 2 đều OK | Sliding window gọn hơn |
| Window size thay đổi | Prefix sum linh hoạt hơn | Khó áp dụng |
| Nhiều queries | Prefix sum (O(1) query) | Sliding window O(n) mỗi lần |
| Negative numbers | Prefix sum vẫn OK | Sliding window thường assume dương |
| Mảng thay đổi | Cả 2 đều kém | Cả 2 đều kém |

---

## 7. So sánh với các cấu trúc liên quan

| Cấu trúc | Build | Query | Update | Use case |
|----------|-------|-------|--------|----------|
| Naive loop | O(1) | O(n) | O(1) | Ít queries |
| Prefix Sum | O(n) | O(1) | O(n) rebuild | Nhiều queries, static array |
| Difference Array | O(n) | O(n) rebuild | O(1) | Nhiều range updates |
| Fenwick Tree (BIT) | O(n log n) | O(log n) | O(log n) | Dynamic updates + queries |
| Segment Tree | O(n) | O(log n) | O(log n) | Dynamic, range queries phức tạp |
| Sparse Table | O(n log n) | O(1) | O(n log n) | Idempotent ops (max, min, gcd) |

**Quy tắc chọn:**
- Static array + range sum → Prefix Sum
- Dynamic array + range sum → Fenwick Tree
- Range min/max → Sparse Table (static) hoặc Segment Tree (dynamic)
- Nhiều range updates + ít queries → Difference Array

---

## 8. Common Pitfalls

### Pitfall 1: Off-by-one error trong indexing
```python
# SAI: quên offset khi dùng 1-indexed prefix
prefix = [0] * (n + 1)
for i in range(1, n + 1):
    prefix[i] = prefix[i-1] + arr[i]  # arr[i] nên là arr[i-1]!

# ĐÚNG:
for i in range(1, n + 1):
    prefix[i] = prefix[i-1] + arr[i-1]  # arr là 0-indexed, prefix là 1-indexed
```

### Pitfall 2: Quên sentinel value trong Subarray Sum = K
```python
# SAI: thiếu {0: 1} ban đầu
prefix_count = {}  # nếu thiếu, bỏ qua subarray từ đầu mảng

# ĐÚNG:
prefix_count = {0: 1}  # prefix sum 0 đã "xuất hiện" trước khi duyệt
```

### Pitfall 3: Difference array — quên kiểm tra boundary
```python
# SAI: có thể gây IndexError
diff[r + 1] -= val  # nếu r = n-1, r+1 = n vượt giới hạn

# ĐÚNG:
if r + 1 < len(diff):
    diff[r + 1] -= val
# Hoặc tạo diff với size n+1 ngay từ đầu
```

### Pitfall 4: Nhầm công thức 2D
```python
# SAI: quên cộng lại góc
total = prefix[r2+1][c2+1] - prefix[r1][c2+1] - prefix[r2+1][c1]
# Thiếu + prefix[r1][c1] → kết quả sai vì trừ góc trên-trái 2 lần

# ĐÚNG: inclusion-exclusion đầy đủ
total = (prefix[r2+1][c2+1] - prefix[r1][c2+1]
         - prefix[r2+1][c1] + prefix[r1][c1])
```

### Pitfall 5: Prefix sum không phù hợp cho range operations khác
```python
# Prefix sum chỉ work cho PHÉP CỘNG (và subtraction, XOR, OR...)
# KHÔNG work cho max/min vì max(A[l..r]) ≠ P[r] - P[l]
# Dùng Sparse Table cho range max/min
```

---

## 9. Câu hỏi phỏng vấn hay gặp

**Q1. Viết hàm trả về range sum trong O(1) sau O(n) preprocessing.**
→ Implement prefix sum array, dùng `P[r+1] - P[l]`.

**Q2. LeetCode 560 — Subarray Sum Equals K.**
→ Prefix sum + HashMap. Tại sao cần `{0: 1}` ban đầu? (để handle subarray bắt đầu từ index 0)

**Q3. LeetCode 304 — Range Sum Query 2D.**
→ 2D prefix sum, nhớ công thức inclusion-exclusion.

**Q4. Khi nào dùng Difference Array thay vì Prefix Sum?**
→ Khi có nhiều range updates (cộng giá trị vào đoạn [l,r]) mà ít queries. Difference array cho O(1) update, prefix sum cho O(1) query.

**Q5. Prefix sum có áp dụng được cho XOR không?**
→ Có. `XOR(l, r) = prefix_xor[r+1] XOR prefix_xor[l]` vì XOR là self-inverse.

**Q6. Tại sao Prefix Sum không dùng được cho dynamic array?**
→ Khi update `A[i]`, toàn bộ `P[i+1..n]` đều thay đổi → O(n) rebuild. Dùng Fenwick Tree để O(log n) update + query.

**Q7. LeetCode 238 — Product of Array Except Self.**
→ Dùng prefix product + suffix product, không cần chia. Tương tự prefix sum nhưng cho phép nhân.

**Q8. Phân biệt prefix sum và running sum?**
→ Running sum thường chỉ track tổng liên tục (không dùng để query range). Prefix sum là array đầy đủ cho phép query bất kỳ range.

---

## 10. Dạng Bài Thường Gặp Trong Thi Cử / Phỏng Vấn

### Dạng 1: Range Sum Query — trả lời nhiều truy vấn tổng đoạn

**Nhận dạng đề:** "Cho mảng n phần tử và Q truy vấn, mỗi truy vấn hỏi tổng từ index l đến r. Tối ưu hóa." Hoặc có cụm từ "multiple queries", "Q queries", "sum of subarray from l to r".

**Approach chuẩn:** Build prefix sum O(n), mỗi query O(1). Tổng O(n + Q) thay vì O(n×Q).

**LeetCode tiêu biểu:** "Range Sum Query - Immutable" (LC 303)

---

### Dạng 2: Subarray Sum Equals K — đếm subarray có tổng bằng k

**Nhận dạng đề:** "Đếm số subarray có tổng bằng k", "tổng bằng target", "tổng bằng bội số của k". Mảng có thể chứa số âm.

**Approach chuẩn:** Prefix sum + HashMap. `count += hashmap[current_prefix - k]`. Khởi tạo `{0: 1}` để xử lý subarray bắt đầu từ index 0.

**LeetCode tiêu biểu:** "Subarray Sum Equals K" (LC 560), "Continuous Subarray Sum" (LC 523)

---

### Dạng 3: 2D Range Sum Query — tổng vùng chữ nhật trong matrix

**Nhận dạng đề:** "Tìm tổng tất cả phần tử trong hình chữ nhật từ (r1,c1) đến (r2,c2)", bài toán trên matrix với nhiều query.

**Approach chuẩn:** 2D prefix sum với inclusion-exclusion. `P[i][j] = A[i-1][j-1] + P[i-1][j] + P[i][j-1] - P[i-1][j-1]`. Query: `P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]`.

**LeetCode tiêu biểu:** "Range Sum Query 2D - Immutable" (LC 304), "Count Submatrices With All Ones" (LC 1504)

---

### Dạng 4: Difference Array — range update O(1)

**Nhận dạng đề:** "Thực hiện Q thao tác, mỗi thao tác cộng v vào tất cả phần tử từ l đến r", "increment range", "apply updates then output final array". Nhiều range updates, ít query.

**Approach chuẩn:** `D[l] += v; D[r+1] -= v`. Sau tất cả updates, chạy prefix sum trên D để thu kết quả. O(1) mỗi update, O(n) rebuild.

**LeetCode tiêu biểu:** "Car Pooling" (LC 1094), "Corporate Flight Bookings" (LC 1109), "Brightest Position on Street" (LC 2021)

---

### Dạng 5: Prefix Sum kết hợp với Sliding Window

**Nhận dạng đề:** "Tìm subarray dài nhất/ngắn nhất có tổng ≤ k", "tổng trong cửa sổ kích thước cố định k", kết hợp điều kiện về độ dài và tổng.

**Approach chuẩn:** Prefix sum để tính nhanh tổng bất kỳ đoạn, sliding window để duyệt các cửa sổ. Khi window size cố định: `sum = prefix[i+k] - prefix[i]`.

**LeetCode tiêu biểu:** "Maximum Average Subarray I" (LC 643), "Minimum Size Subarray Sum" (LC 209)

---

### Dạng 6: Prefix XOR — đếm subarray với XOR bằng k

**Nhận dạng đề:** "Đếm subarray có XOR bằng k", "số cặp (i,j) sao cho XOR từ i đến j bằng target". Dấu hiệu: có XOR và subarray.

**Approach chuẩn:** Tương tự subarray sum = k nhưng dùng XOR thay cộng. `XOR(l,r) = prefix_xor[r+1] XOR prefix_xor[l]`. HashMap lưu số lần xuất hiện của từng prefix XOR.

**LeetCode tiêu biểu:** "Count Triplets That Can Form Two Arrays of Equal XOR" (LC 1442), "Subarray XOR Equal to K" (bài custom trong contest)

---

### Dạng 7: Product of Array Except Self — prefix product + suffix product

**Nhận dạng đề:** "Tính tích tất cả phần tử ngoại trừ chính nó, không dùng phép chia, O(n) time."

**Approach chuẩn:** Chạy prefix product từ trái sang, suffix product từ phải sang. Kết quả[i] = prefix[i] × suffix[i]. Có thể optimize về O(1) space bằng cách dùng output array làm prefix.

**LeetCode tiêu biểu:** "Product of Array Except Self" (LC 238)

---

### Dạng 8: Equilibrium Index — tìm vị trí tổng trái = tổng phải

**Nhận dạng đề:** "Tìm index i sao cho sum(A[0..i-1]) = sum(A[i+1..n-1])", "pivot index", "balance point".

**Approach chuẩn:** Tính total sum trước. Duyệt từ trái, giữ left_sum. Tại mỗi i: `right_sum = total - left_sum - A[i]`. Kiểm tra `left_sum == right_sum`. O(n) time, O(1) space.

**LeetCode tiêu biểu:** "Find Pivot Index" (LC 724), "Find the Middle Index in Array" (LC 1991)

---

### Dạng 9: Subarray với điều kiện chia hết — prefix sum modulo

**Nhận dạng đề:** "Đếm subarray có tổng chia hết cho k", "tổng là bội số của k". Dấu hiệu: có từ "divisible", "multiple of k", "modulo".

**Approach chuẩn:** `prefix[j] % k == prefix[i] % k` thì `sum(i..j)` chia hết cho k. HashMap lưu số lần xuất hiện của `prefix % k`. Cẩn thận với số âm: dùng `(prefix % k + k) % k`.

**LeetCode tiêu biểu:** "Subarray Sums Divisible by K" (LC 974), "Continuous Subarray Sum" (LC 523)

---

### Dạng 10: Khi nào cần Fenwick Tree/Segment Tree thay vì Prefix Sum

**Nhận dạng đề:** Array thay đổi giữa các query — "update element then query range sum", "dynamic array", "point update + range query".

**Approach chuẩn:**
- Static array + range sum → Prefix Sum O(n) build, O(1) query
- Dynamic (point update) + range sum → Fenwick Tree O(n) build, O(log n) update + query
- Range update + range query → Segment Tree with lazy propagation O(n) build, O(log n) cả hai
- Nhận ra dấu hiệu: nếu đề cho cả "update" và "query" xen kẽ → KHÔNG dùng prefix sum

**LeetCode tiêu biểu:** "Range Sum Query - Mutable" (LC 307 — Fenwick Tree), "Range Sum Query 2D - Mutable" (LC 308 — Segment Tree)
