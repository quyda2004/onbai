# Trắc nghiệm — Binary Search

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Điều kiện tiên quyết để áp dụng binary search trên mảng là?

- A. Mảng có ít nhất 100 phần tử
- B. Mảng đã được sắp xếp (sorted) hoặc có tính monotonic
- C. Mảng chỉ chứa số dương
- D. Mảng không có duplicate

> **Đáp án: B**  
> **Giải thích:** Binary search dựa vào việc loại nửa không chứa target dựa trên so sánh với mid. Không sort → so sánh không loại được gì. Duplicate không phá vỡ binary search (chỉ ảnh hưởng vị trí trả về nếu cần lower/upper bound).

---

**Câu 2:** Time complexity của binary search là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: B**  
> **Giải thích:** Mỗi bước giảm không gian tìm kiếm đi 1/2 → số bước ≤ log₂(n). Mỗi bước O(1) → tổng O(log n).

---

**Câu 3:** Cho mảng `[1, 3, 5, 7, 9, 11, 13]` và target = 9. Binary search trả về chỉ số nào?

- A. 3
- B. 4
- C. 5
- D. -1

> **Đáp án: B**  
> **Giải thích:** arr[4] = 9. Trace: lo=0, hi=6, mid=3 → arr[3]=7 < 9, lo=4. lo=4, hi=6, mid=5 → arr[5]=11 > 9, hi=4. lo=4, hi=4, mid=4 → arr[4]=9 ✓ return 4.

---

**Câu 4:** Space complexity của binary search iterative là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: A**  
> **Giải thích:** Chỉ dùng 2-3 biến (lo, hi, mid) → O(1) auxiliary space. Recursive version mới là O(log n) do call stack.

---

**Câu 5:** Với mảng 1 triệu phần tử, binary search cần tối đa bao nhiêu bước?

- A. 10
- B. 20
- C. 100
- D. 1,000,000

> **Đáp án: B**  
> **Giải thích:** log₂(1,000,000) ≈ 20. So sánh với linear O(n) = 1 triệu bước → binary nhanh hơn 50,000 lần.

---

**Câu 6:** Hàm Python tương đương `lower_bound` là?

- A. `list.index`
- B. `bisect.bisect_left`
- C. `bisect.bisect_right`
- D. `sorted`

> **Đáp án: B**  
> **Giải thích:** `bisect_left` trả về chỉ số đầu tiên i sao cho arr[i] >= target — đúng định nghĩa lower bound. `bisect_right` là upper bound. `list.index` là linear O(n).

---

**Câu 7:** Khi nào binary search KHÔNG nhanh hơn linear search?

- A. Khi mảng rất nhỏ (vài phần tử)
- B. Khi cần tìm phần tử ở đầu mảng và mảng chưa sort
- C. Khi sort trước rồi tìm 1 lần
- D. Cả A và B đều đúng

> **Đáp án: D**  
> **Giải thích:** A: với n=3-5, overhead binary có thể lớn hơn lợi ích. B: nếu cần tìm 1 lần và mảng chưa sort, sort O(n log n) + search O(log n) > linear O(n). Linear thắng khi 1 query duy nhất + không sort sẵn.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Cho `arr = [1, 2, 2, 2, 3, 4]`. `bisect_left(arr, 2)` trả về?

- A. 0
- B. 1
- C. 3
- D. 4

> **Đáp án: B**  
> **Giải thích:** Lower bound = chỉ số đầu tiên có giá trị >= 2. arr[1] = 2 (lần đầu tiên xuất hiện) → trả về 1. `bisect_right` trả về 4 (sau lần xuất hiện cuối).

---

**Câu 9:** Đếm số lần xuất hiện của giá trị x trong sorted array bằng công thức?

- A. `upper_bound(x) - lower_bound(x)`
- B. `lower_bound(x) - upper_bound(x)`
- C. `upper_bound(x) + lower_bound(x)`
- D. `binary_search(x) * 2`

> **Đáp án: A**  
> **Giải thích:** Lower bound = vị trí xuất hiện đầu, upper bound = vị trí sau xuất hiện cuối → hiệu = số lần xuất hiện. Cả 2 ops O(log n) → tổng O(log n).

---

**Câu 10:** Lỗi `(lo + hi) // 2` có thể gây overflow trong ngôn ngữ nào?

- A. Python (int unbounded)
- B. Java/C++ với int 32-bit khi lo + hi vượt 2^31 - 1
- C. JavaScript (Number 64-bit)
- D. Không ngôn ngữ nào có vấn đề

> **Đáp án: B**  
> **Giải thích:** Java/C++ int 32-bit max ~2.1 tỷ. Mảng dài >1 tỷ → lo + hi có thể overflow. Sửa: `lo + (hi - lo) // 2`. Python int không bị overflow.

---

**Câu 11:** Search in rotated sorted array (vd `[4, 5, 6, 7, 0, 1, 2]`). Tại mỗi bước, ta cần xác định:

- A. Pivot tại đâu
- B. Nửa nào (trái hoặc phải mid) là sorted thuần — sau đó kiểm tra target có nằm trong nửa đó không
- C. Toàn bộ mảng đã sort chưa
- D. Mảng có duplicate không

> **Đáp án: B**  
> **Giải thích:** Ít nhất 1 trong 2 nửa luôn sorted (so sánh `arr[lo] <= arr[mid]`). Nếu target nằm trong khoảng giá trị của nửa sorted → search nửa đó; ngược lại search nửa kia. O(log n).

---

**Câu 12:** Find peak element trong `[1, 2, 1, 3, 5, 6, 4]`. Bằng binary search, ta luôn đi về phía:

- A. Phần tử lớn hơn (đi về phía có gradient tăng)
- B. Phần tử nhỏ hơn
- C. Phần tử ở giữa
- D. Random

> **Đáp án: A**  
> **Giải thích:** So sánh nums[mid] với nums[mid+1]. Nếu nums[mid] < nums[mid+1], peak chắc chắn ở nửa phải (tiếp tục đi lên). Ngược lại peak ở nửa trái (kể cả mid). Lý do hợp lệ ngay cả khi mảng không monotonic toàn cục.

---

**Câu 13:** Bài "Koko Eating Bananas" sử dụng kỹ thuật nào?

- A. Binary search trên answer space (số chuối ăn/giờ)
- B. Sliding window
- C. Two pointers
- D. DP

> **Đáp án: A**  
> **Giải thích:** Answer = k (chuối/giờ) có khoảng [1, max(piles)]. Hàm `check(k) = (tổng giờ với speed k) ≤ h` là monotonic theo k (k tăng → giờ cần giảm). Bisect trên k tìm min k thỏa.

---

**Câu 14:** Binary search trên 2D matrix sorted thuần (theo row-major) có complexity?

- A. O(m + n)
- B. O(log(m × n)) = O(log m + log n)
- C. O(m × n)
- D. O(m × log n)

> **Đáp án: B**  
> **Giải thích:** Map index 1D i → (i // n, i % n) và áp dụng binary search trên mảng độ dài m*n → O(log(mn)) = O(log m + log n).

---

**Câu 15:** Pattern code sau đây giải bài gì?

```python
lo, hi = 0, len(arr)
while lo < hi:
    mid = (lo + hi) // 2
    if arr[mid] < target:
        lo = mid + 1
    else:
        hi = mid
return lo
```

- A. Vanilla binary search (tìm chính xác)
- B. Lower bound
- C. Upper bound
- D. Tìm peak

> **Đáp án: B**  
> **Giải thích:** Khi arr[mid] >= target, ta dồn về phía trái (hi = mid, không -1) để tìm vị trí đầu tiên thỏa. Trả về lo = chỉ số đầu tiên có arr[i] >= target = lower bound. Khoảng `[lo, hi)` cho phép trả về len nếu không có.

---

**Câu 16:** Median of Two Sorted Arrays (m + n) tối ưu có complexity?

- A. O(m + n)
- B. O((m + n) log(m + n))
- C. O(log(min(m, n)))
- D. O(log(m) × log(n))

> **Đáp án: C**  
> **Giải thích:** Kỹ thuật partition: binary search trên mảng ngắn hơn, tìm vị trí cắt sao cho left_max ≤ right_min của cả 2 mảng. Chỉ log(min(m,n)) bước, mỗi bước O(1) → O(log(min(m,n))).

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Vì sao trong rotated sorted array, kiểm tra `arr[lo] <= arr[mid]` (dùng `<=` chứ không `<`) để xác định nửa trái sorted?

- A. `<=` dễ nhớ hơn
- B. Khi mảng có nửa trái chỉ 1 phần tử (lo == mid), `arr[lo] == arr[mid]` đương nhiên — phải coi là sorted trivially
- C. `<` và `<=` cho cùng kết quả
- D. Để xử lý overflow

> **Đáp án: B**  
> **Giải thích:** Khi mid = lo (khoảng còn 1 phần tử), `arr[lo] < arr[mid]` = False, nhưng nửa trái chỉ có 1 phần tử nên trivially sorted. Dùng `<=` xử lý đúng case này. Với mảng có duplicate, cần thêm xử lý đặc biệt (loại bỏ trùng ở 2 đầu).

---

**Câu 18:** Aggressive Cows: đặt k con bò vào n chuồng (vị trí cho trước) sao cho khoảng cách nhỏ nhất giữa 2 con là **lớn nhất**. Cách giải?

- A. DP O(n × k)
- B. Greedy O(n log n)
- C. Binary search trên answer (khoảng cách d), check(d) = "có thể đặt k con cách nhau ≥ d không" — đơn điệu giảm theo d → bisect
- D. Brute force tất cả tổ hợp

> **Đáp án: C**  
> **Giải thích:** Answer space [1, max_distance]. check(d) đếm tham lam số bò đặt được khi giữ khoảng cách ≥ d (≥ k thì True). Hàm này đơn điệu giảm theo d. Bisect tìm d lớn nhất sao cho check = True. O(n log n) sort + O(n log(max_d)) bisect.

---

**Câu 19:** Phân tích code sau:

```python
def f(nums, target):
    lo, hi = 0, len(nums) - 1
    res = -1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            res = mid
            hi = mid - 1
        elif nums[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return res
```

Tìm gì?

- A. Vị trí xuất hiện cuối của target
- B. Vị trí xuất hiện đầu của target (hoặc -1 nếu không có)
- C. Vị trí bất kỳ
- D. Tổng số xuất hiện

> **Đáp án: B**  
> **Giải thích:** Khi tìm thấy target, lưu `res = mid` rồi tiếp tục thu hẹp về bên trái (hi = mid - 1) để tìm xuất hiện sớm hơn. Cuối cùng res = vị trí đầu tiên. Đây là cách thay thế lower_bound (có thêm check trùng giá trị).

---

**Câu 20:** Floating-point binary search (vd tìm sqrt(x) với eps = 1e-9). Số iteration cần?

- A. O(1)
- B. O(log((hi - lo) / eps))
- C. O(x)
- D. O(sqrt(x))

> **Đáp án: B**  
> **Giải thích:** Khoảng [lo, hi] chia đôi mỗi bước. Sau k bước, kích thước khoảng = (hi-lo)/2^k. Dừng khi ≤ eps → k = log₂((hi-lo)/eps). Với x = 10⁹ và eps = 1e-9 → ~60 bước, vẫn rất nhanh.

---

**Câu 21:** Khi nào dùng `mid = (lo + hi + 1) // 2` (làm tròn lên) thay vì `(lo + hi) // 2`?

- A. Khi cập nhật `lo = mid` (không +1) để tránh vòng lặp vô hạn khi `hi = lo + 1`
- B. Khi mảng có số chẵn phần tử
- C. Khi target âm
- D. Không bao giờ cần thiết

> **Đáp án: A**  
> **Giải thích:** Nếu `lo = mid` (giữ mid là candidate hợp lệ) và mid được làm tròn xuống, khi `hi = lo + 1` thì `mid = lo`, sau cập nhật `lo = mid = lo` không đổi → infinite loop. Làm tròn lên giúp `mid = hi`, sau cập nhật `lo = mid = hi` → vòng lặp kết thúc.

---

**Câu 22:** Galloping search (exponential search) trên mảng vô hạn / kích thước không biết:

- A. Linear scan đến khi vượt target, rồi binary search trong khoảng đó. Tổng O(log p) với p = vị trí cần tìm
- B. Bắt đầu với khoảng 1, gấp đôi mỗi lần cho đến khi arr[i] >= target, rồi binary search trong [i/2, i]. O(log p)
- C. Phải biết trước kích thước
- D. Không thể search trên mảng vô hạn

> **Đáp án: B**  
> **Giải thích:** Galloping (exponential): check arr[1], arr[2], arr[4], arr[8], ... đến khi arr[i] >= target. Khoảng cuối là [i/2, i]. Binary search trong khoảng đó. Cả 2 phase đều O(log p). Hữu dụng khi không biết length hoặc khi target ở gần đầu mảng.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | A      |
| 2   | B      | 13  | A      |
| 3   | B      | 14  | B      |
| 4   | A      | 15  | B      |
| 5   | B      | 16  | C      |
| 6   | B      | 17  | B      |
| 7   | D      | 18  | C      |
| 8   | B      | 19  | B      |
| 9   | A      | 20  | B      |
| 10  | B      | 21  | A      |
| 11  | B      | 22  | B      |
