# Array & String — Lý Thuyết Đầy Đủ

---

## 1. Vấn đề đặt ra

Khi lập trình, nhu cầu cơ bản nhất là lưu trữ và xử lý **nhiều giá trị cùng loại**. Bạn cần lưu điểm thi của 30 học sinh, tên của 1000 sản phẩm, hay dãy ký tự trong một đoạn văn bản. Không thể tạo ra 30 biến riêng lẻ — cần một cấu trúc có thể chứa tất cả và truy cập nhanh theo vị trí.

Array và String là hai cấu trúc dữ liệu cơ bản nhất, xuất hiện trong hầu hết mọi bài toán lập trình. Hiểu sâu về chúng — đặc biệt là cách bộ nhớ hoạt động — là nền tảng để học tất cả các cấu trúc dữ liệu khác.

---

## 2. Giải thích bằng hình ảnh đời thường

**Array như một dãy ghế trong rạp chiếu phim:**

Hàng ghế được đánh số từ 1 đến 100. Mỗi ghế có đúng một số ghế (index). Bạn muốn ngồi ghế số 47? Đi thẳng đến đó — không cần hỏi từng ghế từ đầu. Nhưng nếu muốn thêm một ghế vào giữa hàng, bạn phải dịch chuyển toàn bộ ghế từ đó đến cuối sang phải — rất tốn sức.

**String như một chuỗi vòng ngọc:**

Mỗi vòng ngọc là một ký tự. Toàn bộ vòng cổ là chuỗi "HELLO". Muốn kiểm tra vòng cổ có chứa chữ "ELL" không? Phải trượt dọc từng bộ 3 ngọc liên tiếp và so sánh. Một khi đã đeo lên cổ (immutable), bạn không thể thay đổi từng ngọc — phải tháo ra và làm lại toàn bộ.

---

## 3. Khái niệm cơ bản

**Array** là cấu trúc dữ liệu lưu trữ các phần tử tại các **ô nhớ liên tiếp** trong RAM. Mỗi phần tử có index (chỉ số) bắt đầu từ 0.

```
Bộ nhớ:  [addr 100] [addr 104] [addr 108] [addr 112] [addr 116]
Giá trị:     10         20         30         40         50
Index:         0          1          2          3          4
```

Truy cập `arr[2]`: địa chỉ = 100 + 2 × 4 = 108 → lấy giá trị 30. Luôn O(1).

**String** trong Python là **immutable sequence of characters** — chuỗi ký tự không thay đổi được sau khi tạo.

---

## 4. Ví dụ từng bước (step-by-step)

**Bài toán:** Đảo ngược chuỗi "hello" → "olleh"

**Bước 1:** String "hello" immutable, cần chuyển sang list trước:
```
chars = ['h', 'e', 'l', 'l', 'o']
         0    1    2    3    4
```

**Bước 2:** Đặt left = 0 (đầu), right = 4 (cuối). Swap và tiến vào giữa:

- Vòng 1: left=0, right=4 → swap `chars[0]='h'` và `chars[4]='o'`
  - Kết quả: `['o', 'e', 'l', 'l', 'h']`
  - left=1, right=3

- Vòng 2: left=1, right=3 → swap `chars[1]='e'` và `chars[3]='l'`
  - Kết quả: `['o', 'l', 'l', 'e', 'h']`
  - left=2, right=2

- Vòng 3: left=2, right=2 → left >= right → dừng (phần tử giữa không cần swap)

**Bước 3:** `''.join(chars)` = "olleh"

Tại sao dừng khi `left >= right`? Đã swap xong mọi cặp đối xứng. Chạy thêm sẽ swap ngược lại.

---

## 5. Code Python đơn giản nhất

```python
# Đảo ngược chuỗi — O(n) time, O(n) space
def reverse_string(s):
    chars = list(s)               # String → list để có thể swap
    left, right = 0, len(s) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1
    return ''.join(chars)         # List → String

# Tìm phần tử trong array — O(n) time, O(1) space
def linear_search(arr, target):
    for i, val in enumerate(arr):
        if val == target:
            return i              # Trả về index đầu tiên tìm thấy
    return -1                     # Không tìm thấy

print(reverse_string("hello"))         # "olleh"
print(linear_search([3, 7, 1, 9], 7)) # 1
```

---

## 6. Mở rộng dần

### Xử lý edge cases

```python
def reverse_string_safe(s):
    if len(s) <= 1:   # Empty hoặc single character không cần đảo
        return s
    chars = list(s)
    left, right = 0, len(s) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1
    return ''.join(chars)
```

### Các thao tác phổ biến và complexity của chúng

```python
arr = [1, 2, 3, 4, 5]

# O(1) — thao tác cuối mảng
arr.append(6)        # Thêm vào cuối: [1, 2, 3, 4, 5, 6]
arr.pop()            # Xóa cuối: [1, 2, 3, 4, 5]
arr[-1]              # Truy cập phần tử cuối

# O(n) — thao tác đầu/giữa (phải shift phần tử)
arr.insert(0, 0)     # Thêm vào đầu: tất cả phần tử shift sang phải
arr.pop(0)           # Xóa đầu: tất cả phần tử shift sang trái
arr.insert(2, 99)    # Thêm vào giữa index 2

# O(k) — slice tạo copy mới
sub = arr[1:4]       # Copy 3 phần tử [1:4]

# O(n) — tìm kiếm trong list
print(3 in arr)      # Linear search
print(arr.index(3))  # Linear search, trả về index đầu tiên
```

### String immutability — vấn đề hiệu suất

```python
# LỖI NGHIÊM TRỌNG — O(n²) vì mỗi += tạo string mới và copy toàn bộ
result = ""
words = ["hello", "world", "foo", "bar"]
for word in words:
    result += word + " "   # Mỗi lần copy toàn bộ result cũ

# ĐÚNG — O(n) dùng join
result = " ".join(words)

# Hoặc dùng list buffer rồi join cuối
parts = []
for word in words:
    parts.append(word)
result = " ".join(parts)
```

---

## 7. Độ phức tạp (Time & Space Complexity)

### Array (Python list)

| Thao tác | Best | Average | Worst | Space | Ghi chú |
|----------|------|---------|-------|-------|---------|
| Access `arr[i]` | O(1) | O(1) | O(1) | O(1) | Index trực tiếp |
| Search (unsorted) | O(1) | O(n) | O(n) | O(1) | Best: phần tử đầu |
| Search (sorted) | O(1) | O(log n) | O(log n) | O(1) | Dùng binary search |
| Append (cuối) | O(1) | O(1) | O(n) | O(1) amortized | Worst: khi resize |
| Insert (đầu/giữa) | O(n) | O(n) | O(n) | O(1) | Phải shift phần tử |
| Delete (cuối) | O(1) | O(1) | O(1) | O(1) | Không cần shift |
| Delete (đầu/giữa) | O(n) | O(n) | O(n) | O(1) | Phải shift phần tử |
| Slice `arr[i:j]` | O(j-i) | O(j-i) | O(n) | O(j-i) | Tạo copy mới |

### String Operations

| Thao tác | Complexity | Ghi chú |
|----------|------------|---------|
| `s[i]` | O(1) | Index trực tiếp |
| `len(s)` | O(1) | Python cache sẵn |
| `s1 + s2` | O(n+m) | Tạo string mới, copy cả hai |
| `s[i:j]` | O(j-i) | Tạo string mới |
| `s.find(sub)` | O(n×m) | Naive search: n×m so sánh |
| `''.join(list)` | O(n) | n là tổng độ dài |
| `s.split()` | O(n) | |

---

## 8. Patterns & Variations

### Pattern 1: Two Pointers

```python
# Kiểm tra palindrome — O(n) time, O(1) space
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

print(is_palindrome("racecar"))  # True
print(is_palindrome("hello"))    # False
```

### Pattern 2: Sliding Window (preview)

```python
# Tổng tối đa của subarray độ dài k — O(n)
def max_subarray_sum(arr, k):
    window_sum = sum(arr[:k])   # Khởi tạo cửa sổ đầu tiên
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]  # Trượt cửa sổ sang phải
        max_sum = max(max_sum, window_sum)
    return max_sum
```

### Pattern 3: HashMap để tối ưu

```python
# Kiểm tra anagram — O(n) thay vì O(n log n) với sort
from collections import Counter
def is_anagram(s, t):
    return Counter(s) == Counter(t)

# Two Sum — O(n) thay vì O(n²) brute force
def two_sum(nums, target):
    seen = {}                           # value → index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

---

## 9. Code nâng cao / Optimized

```python
from typing import List

def rotate_array(nums: List[int], k: int) -> None:
    """
    Xoay mảng phải k bước. In-place O(1) extra space.
    
    Trick: xoay k bước phải = đảo ngược 3 lần:
    [1,2,3,4,5,6,7], k=3
    → reverse all:   [7,6,5,4,3,2,1]
    → reverse [0:k]: [5,6,7,4,3,2,1]
    → reverse [k:]:  [5,6,7,1,2,3,4] ← đúng
    """
    n = len(nums)
    k = k % n   # Xử lý k > n

    def reverse(left, right):
        while left < right:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
            right -= 1

    reverse(0, n - 1)
    reverse(0, k - 1)
    reverse(k, n - 1)


def product_except_self(nums: List[int]) -> List[int]:
    """
    Tích tất cả phần tử ngoại trừ phần tử tại index i.
    O(n) time, O(1) extra space (không dùng phép chia).
    
    Ý tưởng: result[i] = (tích tất cả bên trái i) × (tích tất cả bên phải i)
    """
    n = len(nums)
    result = [1] * n

    # Pass 1: tích prefix — result[i] = nums[0] * ... * nums[i-1]
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]

    # Pass 2: nhân suffix vào — suffix = nums[i+1] * ... * nums[n-1]
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]

    return result


def longest_common_prefix(strs: List[str]) -> str:
    """
    Tìm prefix chung dài nhất của tất cả strings.
    O(S) với S là tổng số ký tự.
    
    Trick: prefix chung chỉ cần so sánh min và max string
    (về lexicographic order) — nếu phù hợp 2 extreme thì phù hợp tất cả.
    """
    if not strs:
        return ""
    min_str = min(strs)
    max_str = max(strs)
    for i, char in enumerate(min_str):
        if char != max_str[i]:
            return min_str[:i]
    return min_str
```

---

## 10. Khi nào dùng / Không dùng

**Dùng Array khi:**
- Cần truy cập ngẫu nhiên theo index thường xuyên
- Kích thước dữ liệu ổn định hoặc chỉ thêm vào cuối
- Cần cache locality tốt (xử lý tuần tự)
- Làm nền tảng cho two pointers, sliding window, prefix sum

**Không dùng Array khi:**
- Insert/delete thường xuyên ở đầu/giữa → dùng Linked List hoặc deque
- Cần tìm kiếm nhanh theo giá trị → dùng HashMap/Set
- Kích thước thay đổi liên tục và không đoán được

**String immutable — lưu ý:**
- Xây dựng string dần dần → dùng list buffer + join cuối
- Cần nhiều string manipulation → `io.StringIO` hoặc list of chars

---

## 11. So sánh với các cấu trúc dữ liệu liên quan

| Tiêu chí | Array/list | Linked List | HashMap | String |
|----------|-----------|-------------|---------|--------|
| Truy cập index | O(1) | O(n) | N/A | O(1) |
| Tìm kiếm giá trị | O(n) | O(n) | O(1) avg | O(n) |
| Thêm vào cuối | O(1) amortized | O(1) | O(1) avg | O(n) — immutable |
| Thêm vào đầu | O(n) | O(1) | N/A | O(n) |
| Bộ nhớ liên tiếp | Có | Không | Không | Có |
| Cache friendly | Rất tốt | Kém | Trung bình | Rất tốt |

---

## 12. Pitfalls & Câu hỏi phỏng vấn

### Lỗi thường gặp

1. **Off-by-one error:** Index hợp lệ là 0 đến `len(arr) - 1`. `arr[len(arr)]` → IndexError.

2. **String concatenation trong loop:**
   ```python
   # SAI — O(n²):
   result = ""
   for s in strings: result += s
   
   # ĐÚNG — O(n):
   result = "".join(strings)
   ```

3. **Modify list khi đang duyệt:**
   ```python
   # Nguy hiểm — bỏ qua phần tử:
   for i, x in enumerate(arr):
       if x % 2 == 0: arr.pop(i)
   
   # An toàn:
   arr = [x for x in arr if x % 2 != 0]
   ```

4. **`arr[:]` tạo shallow copy:** Thay đổi list copy không ảnh hưởng original, nhưng các object bên trong vẫn là cùng reference.

5. **`in` trên list là O(n), trên set là O(1):**
   ```python
   if x in my_list:  # O(n)
   if x in my_set:   # O(1) avg
   ```

### Câu hỏi phỏng vấn hay gặp

1. **"Xóa duplicates trong sorted array in-place?"** — Two pointers: slow pointer giữ vị trí ghi, fast pointer duyệt. O(n) time, O(1) space.

2. **"Tìm phần tử thiếu trong mảng 1..n?"** — Công thức: `n*(n+1)//2 - sum(arr)`. O(n) time, O(1) space.

3. **"Two sum?"** — HashMap: với mỗi x, kiểm tra `target-x` trong map. O(n) time, O(n) space.

4. **"Tại sao string Python immutable?"** — An toàn làm dict key (hash không đổi), thread-safe, tối ưu bộ nhớ qua string interning.

5. **"Độ phức tạp của `sorted()` và `list.sort()`?"** — Cả hai O(n log n), đều dùng Timsort. Khác biệt: `sorted()` trả về list mới O(n) space; `list.sort()` in-place O(1) extra space.

6. **"Subarray sum bằng k?"** — Prefix sum + HashMap: O(n) time, O(n) space.
