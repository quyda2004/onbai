# Sliding Window — Cửa Sổ Trượt

## 1. Giải thích cho người mới hoàn toàn

Tưởng tượng bạn đang đi trên đường và cầm trong tay một khung ảnh hình chữ nhật. Bạn nhìn vào trong khung và chỉ thấy được phần đang lọt vào khung. Khi bạn đi tiếp một bước:
- **Một mảnh ở mép trước** vừa lọt vào khung.
- **Một mảnh ở mép sau** vừa bị bỏ ra khỏi khung.

Bạn **không cần nhìn lại toàn bộ cảnh vật**, chỉ cần biết hai thay đổi nhỏ đó là đủ để cập nhật "khung hình mới".

Đó chính là sliding window.

### Ví dụ cụ thể — Trung bình điểm 3 ngày liên tiếp

Bạn có điểm thi của một học sinh trong 7 ngày: `[6, 8, 7, 5, 9, 10, 4]`.  
Hỏi: Tính trung bình điểm của **mọi cửa sổ 3 ngày liên tiếp**.

**Cách ngây thơ:** Mỗi cửa sổ tính lại tổng → mỗi cửa sổ tốn 3 phép cộng, có 5 cửa sổ → 15 phép. Nếu cửa sổ là 1000 và mảng dài 10000 → 10 triệu phép.

**Cách Sliding Window:**  
- Cửa sổ đầu tiên `[6, 8, 7]`: tổng = 21, trung bình = 7.
- Trượt 1 bước → bỏ ra `6`, thêm vào `5` → tổng mới = 21 - 6 + 5 = 20, trung bình ≈ 6.67.
- Trượt tiếp → bỏ ra `8`, thêm vào `9` → tổng = 20 - 8 + 9 = 21, trung bình = 7.
- ...

Mỗi bước chỉ **2 phép tính** (bớt 1, cộng 1), không phụ thuộc vào kích thước cửa sổ.  Tổng cộng O(n) thay vì O(n × k).

**Bản chất:** Đừng tính lại từ đầu mỗi lần trượt — chỉ cập nhật những gì đã thay đổi.

---

## 2. Giải thích nâng cao cho người chuyên ngành

**Sliding Window** là kỹ thuật xử lý subarray/substring liên tiếp bằng cách duy trì một "cửa sổ" có 2 biên — `left` và `right` — di chuyển trên mảng từ trái sang phải. Khi cửa sổ trượt, ta cập nhật trạng thái (sum, count, hash) một cách **gia tăng (incremental)** thay vì tính lại toàn bộ.

### Hai biến thể chính

**1. Fixed-size window (cửa sổ kích thước cố định)**
- Window luôn có đúng K phần tử.
- Right tiến 1 bước → left tiến 1 bước.
- Áp dụng cho: max sum of K consecutive elements, average of K days, first negative number in window of size K.

**2. Variable-size window (cửa sổ co giãn động)**
- Right liên tục mở rộng đến khi vi phạm điều kiện (ví dụ tổng vượt target, số ký tự distinct vượt K).
- Khi vi phạm, left co lại đến khi cửa sổ hợp lệ trở lại.
- Áp dụng cho: longest substring with at most K distinct, minimum window substring, smallest subarray sum ≥ target.

### Khi nào dùng được Sliding Window

Điều kiện cần (rule of thumb):
- Bài toán hỏi về **subarray/substring liên tiếp** (không phải subsequence).
- Tồn tại **hàm trạng thái monotonic** theo độ dài cửa sổ: khi cửa sổ tăng, một đại lượng nào đó (sum, distinct count, max char freq) cũng tăng hoặc giảm có quy luật → cho phép biết khi nào co/dãn.
- Update khi trượt phải có chi phí O(1) hoặc O(log n) (amortized).

Nếu **không có monotonicity** (ví dụ: sum có cả số âm → tăng kích thước không nhất thiết tăng sum) → sliding window không trực tiếp áp dụng, phải đổi sang prefix sum + hashmap.

### Phân tích amortized

Trong variable-size window, dù mỗi bước right có thể yêu cầu left di chuyển nhiều lần để khôi phục invariant, **tổng số lần left tiến trong toàn bộ thuật toán ≤ n**. Cộng với right tiến tối đa n lần → tổng cộng ≤ 2n thao tác → O(n) amortized.

### Sliding Window + cấu trúc dữ liệu phụ

- **Hash counter**: đếm tần suất ký tự/giá trị trong window (longest substring without repeating, anagram).
- **Monotonic deque**: duy trì max/min trong window O(1) (sliding window maximum).
- **TreeMap / sorted multiset (C++/Java)**: lấy max/min khi cần xóa phần tử cụ thể, O(log k).

### Trade-off so với các kỹ thuật khác

- **Sliding Window vs Prefix Sum**: Cả hai đều xử lý subarray sum. Sliding Window O(n) space O(1) nhưng yêu cầu monotonicity. Prefix Sum O(n) space O(n), không yêu cầu monotonicity, hỗ trợ query bất kỳ subarray sau O(1).
- **Sliding Window vs Brute Force**: Brute force enum mọi (l, r) là O(n²). Sliding Window O(n) khi điều kiện cho phép.

---

## 3. Định nghĩa chính xác

**Sliding Window**: Một kỹ thuật thiết kế thuật toán duy trì một cửa sổ con liên tiếp `[left, right]` trên một dãy tuần tự, di chuyển hai biên theo quy tắc giúp giải bài toán trong O(n) thay vì O(n²), bằng cách tận dụng tính chất incremental của trạng thái cửa sổ.

**Window invariant**: Một bất biến (ví dụ "tổng cửa sổ ≤ T" hoặc "số ký tự distinct ≤ K") được duy trì trong suốt quá trình trượt. Khi right phá vỡ invariant, left phải co lại để khôi phục.

**Amortized O(n)**: Tổng chi phí qua tất cả các vòng lặp được chia đều, dù mỗi vòng đơn lẻ có thể O(k).

---

## 4. Bảng Độ phức tạp đầy đủ

### Theo loại bài toán

| Bài toán | Time | Auxiliary Space | Loại window | Ghi chú |
|----------|------|-----------------|-------------|---------|
| Max sum of K consecutive | O(n) | O(1) | Fixed | Chỉ cần sum incremental |
| Average of K days | O(n) | O(1) | Fixed | Tương tự max sum |
| First negative in each K-window | O(n) | O(k) | Fixed | Dùng deque |
| Longest substring no repeat | O(n) | O(min(n, charset)) | Variable | Hash set/map |
| Longest substring ≤ K distinct | O(n) | O(K) | Variable | Hash counter |
| Min window substring | O(n+m) | O(m) | Variable | m = pattern len |
| Smallest subarray sum ≥ S (positive) | O(n) | O(1) | Variable | Yêu cầu số dương |
| Sliding window maximum | O(n) | O(k) | Fixed | Monotonic deque |
| Permutation in string | O(n+m) | O(charset) | Fixed | k = m |
| Subarrays with K distinct (exactly) | O(n) | O(K) | Variable | Tricks: atMost(K) - atMost(K-1) |

**So với brute force**:

| Bài | Brute Force | Sliding Window |
|-----|-------------|----------------|
| Max sum K consecutive | O(n × k) | O(n) |
| Longest substring no repeat | O(n² × charset) | O(n) |
| Min window substring | O(n × m) | O(n + m) |
| Sliding window max | O(n × k) | O(n) với deque |

---

## 5. Code mẫu

### Fixed Window — Max sum of K consecutive

```python
def max_sum_window(arr, k):
    """
    Tìm tổng lớn nhất của k phần tử liên tiếp.
    """
    if len(arr) < k:
        return None
    window_sum = sum(arr[:k])
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]  # cộng phần mới, trừ phần cũ
        max_sum = max(max_sum, window_sum)
    return max_sum

print(max_sum_window([2, 1, 5, 1, 3, 2], 3))  # 9 (5+1+3)
```

### Variable Window — Longest substring without repeating

```python
def longest_no_repeat(s):
    """
    Độ dài chuỗi con dài nhất không có ký tự lặp.
    """
    seen = {}  # char -> last index
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in seen and seen[ch] >= left:
            left = seen[ch] + 1  # nhảy left qua vị trí cũ của ch
        seen[ch] = right
        best = max(best, right - left + 1)
    return best

print(longest_no_repeat("abcabcbb"))  # 3 ("abc")
print(longest_no_repeat("pwwkew"))    # 3 ("wke")
```

### Variable Window — Smallest subarray sum ≥ target (positive numbers)

```python
def min_subarray_sum(nums, target):
    """
    nums: số nguyên dương. Tìm độ dài subarray ngắn nhất có tổng ≥ target.
    Trả về 0 nếu không có.
    """
    left = 0
    total = 0
    best = float('inf')
    for right in range(len(nums)):
        total += nums[right]
        while total >= target:
            best = min(best, right - left + 1)
            total -= nums[left]
            left += 1
    return 0 if best == float('inf') else best

print(min_subarray_sum([2, 3, 1, 2, 4, 3], 7))  # 2 ([4,3])
```

### Variable Window + Hash Counter — Min Window Substring

```python
from collections import Counter

def min_window(s, t):
    """
    Tìm substring nhỏ nhất của s chứa tất cả ký tự của t (kể cả tần suất).
    """
    if not t or not s:
        return ""
    need = Counter(t)
    missing = len(t)  # số ký tự còn thiếu (đếm cả lặp)
    left = start = end = 0
    for right, ch in enumerate(s, 1):  # right = vị trí kết thúc (1-based)
        if need[ch] > 0:
            missing -= 1
        need[ch] -= 1
        if missing == 0:
            while need[s[left]] < 0:  # ký tự ở left dư
                need[s[left]] += 1
                left += 1
            if end == 0 or right - left < end - start:
                start, end = left, right
            need[s[left]] += 1
            missing += 1
            left += 1
    return s[start:end]

print(min_window("ADOBECODEBANC", "ABC"))  # "BANC"
```

### Fixed Window + Monotonic Deque — Sliding Window Maximum

```python
from collections import deque

def max_sliding_window(nums, k):
    """
    Trả về danh sách max của mỗi cửa sổ độ rộng k.
    """
    dq = deque()  # lưu index, giá trị giảm dần
    result = []
    for i, x in enumerate(nums):
        # bỏ phần tử ngoài cửa sổ
        if dq and dq[0] <= i - k:
            dq.popleft()
        # duy trì monotonic giảm dần
        while dq and nums[dq[-1]] < x:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result

print(max_sliding_window([1,3,-1,-3,5,3,6,7], 3))  # [3,3,5,5,6,7]
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Bài toán hỏi về **subarray hoặc substring liên tiếp** (contiguous).
- Cửa sổ có **kích thước cố định K** đã biết trước.
- Cửa sổ co giãn với điều kiện **monotonic** (sum tăng khi mở rộng, count tăng khi mở rộng, max char freq tăng khi mở rộng).
- Cần tổng/max/min/count incremental khi trượt.
- Mảng/chuỗi chỉ chứa số **dương** (cho variable window sum).

**Không dùng khi:**
- Bài hỏi về **subsequence không liên tiếp** — dùng DP/two pointers khác.
- Mảng có **số âm** trong variable window sum — vì tăng kích thước không đảm bảo tăng sum → mất monotonicity. Dùng prefix sum + hashmap (như Subarray Sum Equals K).
- Cần truy vấn ngẫu nhiên trên nhiều range → prefix sum / segment tree.
- Không có cấu trúc tuần tự (cây, đồ thị) — dùng DFS/BFS.

---

## 7. So sánh với các kỹ thuật liên quan

| Tiêu chí | Sliding Window | Two Pointers | Prefix Sum | Brute Force |
|----------|----------------|--------------|------------|-------------|
| Áp dụng | Subarray liên tiếp | Cặp phần tử trong mảng sorted hoặc symmetric | Sum range query | Mọi bài, baseline |
| Time | O(n) | O(n) hoặc O(n log n) | Build O(n), query O(1) | O(n²) trở lên |
| Space | O(1) hoặc O(charset) | O(1) | O(n) | O(1) |
| Yêu cầu sort | Không | Thường có | Không | Không |
| Hỗ trợ số âm | Hạn chế (variable window) | Có | Có | Có |
| Multiple queries | Không lợi | Không lợi | Rất lợi | Tệ |

**Sliding Window vs Two Pointers**: Sliding window là **trường hợp đặc biệt** của two pointers cùng hướng, chuyên biệt cho subarray/substring liên tiếp. Two pointers tổng quát hơn (có thể đối hướng, có thể trên 2 mảng).

**Sliding Window vs Prefix Sum**: Sliding window nhanh hơn (O(1) space) khi điều kiện monotonic được thỏa. Prefix sum thắng khi cần xử lý nhiều range query hoặc khi có số âm phá monotonicity.

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Áp dụng cho subarray sum khi mảng có số âm** — variable window đòi tổng phải tăng đơn điệu khi mở rộng. Số âm phá điều đó → kết quả sai. Đổi sang prefix sum + hashmap.

2. **Quên trừ phần tử cũ khi trượt fixed window** — `window_sum += arr[i]` nhưng quên `- arr[i-k]` → tổng phình lên.

3. **Off-by-one giữa độ dài cửa sổ và index** — `right - left + 1` là độ dài; viết nhầm `right - left` cho ra K-1.

4. **Xử lý sai khi co left** — quên cập nhật hash counter / window state khi left tiến → invariant lưu sai.

5. **Trường hợp len(arr) < K** — fixed window cần kiểm tra trước, tránh truy cập `arr[i-k]` âm.

6. **Min window substring: nhầm "đủ" vs "chính xác"** — cần ≥ tần suất từng ký tự của pattern (đủ), không phải bằng. Logic missing/need phải xử lý đúng.

7. **Sliding window maximum không dùng deque** — chỉ duy trì max biến đơn giản: khi max thoát khỏi cửa sổ thì không biết max mới → bắt buộc dùng monotonic deque.

8. **Khởi tạo `best = 0` thay vì `inf` cho min** — tìm min mà khởi tạo 0 → kết quả luôn = 0 (sai). Ngược lại tìm max khởi tạo `inf` cũng sai.

---

## 9. Câu hỏi phỏng vấn hay gặp

- Sự khác biệt giữa fixed-size và variable-size window? Cho ví dụ mỗi loại.
- Tại sao sliding window không áp dụng được trực tiếp cho subarray sum với số âm? Cách giải thay thế?
- Chứng minh sliding window maximum dùng monotonic deque là O(n) amortized (mỗi phần tử push/pop đúng 1 lần).
- Min Window Substring: vì sao dùng `missing` counter thay vì so sánh trực tiếp 2 dict mỗi vòng?
- "Longest Substring with At Most K Distinct Characters" — viết code, phân tích complexity.
- Subarrays with exactly K distinct — dùng trick atMost(K) - atMost(K-1). Giải thích tại sao đúng.
- Khi nào HashMap + Prefix Sum thắng Sliding Window?
- Tại sao "Permutation in String" có thể coi như fixed window?
