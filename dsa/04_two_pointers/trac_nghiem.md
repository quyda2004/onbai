# Trắc nghiệm — Two Pointers

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)  
> Mỗi câu có 4 đáp án, đáp án đúng được giải thích chi tiết.

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Điều kiện tiên quyết phổ biến nhất để áp dụng kỹ thuật two pointers đối hướng (opposite-direction) trên mảng là gì?

- A. Mảng phải có ít nhất 100 phần tử
- B. Mảng phải được sắp xếp (sorted)
- C. Mảng phải chứa số nguyên dương
- D. Mảng phải không có phần tử trùng nhau

> **Đáp án: B**  
> **Giải thích:** Two pointers đối hướng dựa vào tính chất đơn điệu (monotonic) của mảng sorted: di chuyển con trỏ trái tăng tổng, di chuyển con trỏ phải giảm tổng. Nếu mảng chưa sort, logic này không đúng. A và C không liên quan; D không bắt buộc (chỉ cần xử lý duplicate khi cần).

---

**Câu 2:** Complexity time của bài toán Two Sum trên sorted array dùng two pointers là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: C**  
> **Giải thích:** Mỗi bước, ít nhất một con trỏ di chuyển 1 đơn vị. Khoảng cách giữa 2 con trỏ ban đầu là n-1, mỗi bước giảm ít nhất 1 → tối đa n-1 bước → O(n).

---

**Câu 3:** Đảo ngược chuỗi `"abcde"` bằng two pointers sẽ tráo đổi bao nhiêu cặp ký tự?

- A. 1
- B. 2
- C. 4
- D. 5

> **Đáp án: B**  
> **Giải thích:** Hai con trỏ tiến vào nhau từ 2 đầu. Với n = 5, số swap = n/2 (lấy phần nguyên) = 2 (tráo a↔e, b↔d, để c đứng yên ở giữa).

---

**Câu 4:** Floyd's Cycle Detection (tortoise and hare) sử dụng:

- A. 2 con trỏ đi cùng tốc độ
- B. 2 con trỏ, slow đi 1 bước, fast đi 2 bước
- C. 1 con trỏ và 1 hash set
- D. 2 con trỏ đi ngược chiều nhau

> **Đáp án: B**  
> **Giải thích:** Fast đi gấp đôi slow. Nếu có cycle, fast sẽ "đuổi kịp" slow trong vòng tối đa n bước. Nếu không cycle, fast sẽ chạm null. C là cách dùng hash set (O(n) space) — đúng nhưng không phải Floyd. D sai về cơ chế.

---

**Câu 5:** Space complexity của bài Remove Duplicates from Sorted Array dùng two pointers (in-place) là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: A**  
> **Giải thích:** Chỉ dùng 2 biến chỉ số slow và fast, sửa trực tiếp trên mảng đầu vào → O(1) auxiliary space.

---

**Câu 6:** Trong bài Container With Most Water (cột nước), khi gặp `height[left] < height[right]`, ta nên:

- A. Di chuyển con trỏ phải sang trái
- B. Di chuyển con trỏ trái sang phải
- C. Di chuyển cả hai con trỏ
- D. Dừng vòng lặp

> **Đáp án: B**  
> **Giải thích:** Chiều cao container giới hạn bởi cột thấp hơn. Nếu di chuyển con trỏ phải (cột cao hơn), chiều cao không cải thiện mà width giảm → không thể tốt hơn. Di chuyển con trỏ trái mới có khả năng tìm cột cao hơn để cải thiện diện tích.

---

**Câu 7:** Kỹ thuật nào sau đây KHÔNG phải biến thể của two pointers?

- A. Sliding window
- B. Tortoise and hare
- C. Merge step trong merge sort
- D. Binary search trên mảng sorted

> **Đáp án: D**  
> **Giải thích:** Binary search là divide-and-conquer dựa trên 1 khoảng [low, high] (low và high chỉ là biên), không có 2 "con trỏ" di chuyển đồng thời theo nghĩa two pointers. A, B, C đều là các biến thể two pointers hợp lệ.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Cho mảng đã sort `[1, 2, 3, 4, 6]` và target = 6. Two pointers trả về cặp index nào?

- A. (0, 4)
- B. (1, 3)
- C. (2, 4)
- D. Không có cặp nào

> **Đáp án: B**  
> **Giải thích:** L=0, R=4 → 1+6=7 > 6 → R=3. L=0, R=3 → 1+4=5 < 6 → L=1. L=1, R=3 → 2+4=6 = target → trả về (1, 3).

---

**Câu 9:** Bài 3Sum (tìm tất cả triplet a+b+c=0) có complexity tốt nhất hiện biết là?

- A. O(n log n)
- B. O(n²)
- C. O(n² log n)
- D. O(n³)

> **Đáp án: B**  
> **Giải thích:** Sort O(n log n) + vòng ngoài O(n) × two pointers bên trong O(n) = O(n²). Vẫn là dạng polynomial nhưng tốt hơn brute force O(n³).

---

**Câu 10:** Trong 3Sum, để tránh kết quả trùng lặp, ta phải:

- A. Dùng set lưu các triplet đã thấy
- B. Skip các giá trị bằng nhau khi di chuyển từng con trỏ
- C. Chỉ duyệt nửa đầu mảng
- D. Đảo ngược mảng sau khi sort

> **Đáp án: B**  
> **Giải thích:** Sau khi sort, các giá trị bằng nhau nằm liền kề. Nếu skip duplicate ngay tại loop (cả vòng ngoài i và cả left/right) thì kết quả tự động unique mà không tốn O(n) bộ nhớ cho set. A đúng về mặt logic nhưng tốn thêm space và overhead hash.

---

**Câu 11:** Floyd's algorithm: sau khi slow và fast gặp nhau trong cycle, để tìm điểm bắt đầu cycle, ta:

- A. Tiếp tục cho fast đi 2x cho đến khi quay lại
- B. Đặt 1 con trỏ về head, cả 2 con trỏ đi 1 bước/lần đến khi gặp nhau
- C. Đếm độ dài cycle rồi dùng two pointers cách nhau k bước
- D. Cả B và C đều đúng

> **Đáp án: D**  
> **Giải thích:** Cách B (reset 1 về head, đi cùng tốc độ 1) là phổ biến nhất, dựa trên chứng minh khoảng cách head→start = khoảng cách meeting point→start (mod độ dài cycle). Cách C cũng hợp lệ: đếm L = độ dài cycle, đặt p1 và p2 cách nhau L bước, đi cùng tốc độ đến khi gặp.

---

**Câu 12:** Cho mảng `[0, 1, 0, 3, 12]`, bài Move Zeroes (giữ relative order) sau khi xử lý in-place sẽ là?

- A. `[1, 3, 12, 0, 0]`
- B. `[1, 0, 0, 3, 12]`
- C. `[0, 0, 1, 3, 12]`
- D. `[12, 3, 1, 0, 0]`

> **Đáp án: A**  
> **Giải thích:** Slow ghi vị trí kế tiếp cho non-zero. Fast quét toàn mảng. Sau khi fast chạy hết, từ slow đến cuối điền 0. Relative order của non-zero giữ nguyên: 1, 3, 12.

---

**Câu 13:** Trapping Rain Water bằng two pointers có space complexity là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: A**  
> **Giải thích:** Chỉ dùng 4 biến: left, right, left_max, right_max. Khác với approach DP precompute left_max[] và right_max[] arrays là O(n) space.

---

**Câu 14:** Khi nào HashMap thắng Two Pointers cho bài tìm cặp tổng bằng target?

- A. Khi mảng chưa sort và ta không được phép thay đổi thứ tự gốc
- B. Khi mảng có rất nhiều phần tử trùng nhau
- C. Khi memory không phải vấn đề
- D. Cả A và C

> **Đáp án: D**  
> **Giải thích:** Two Pointers cần sort trước → O(n log n) và mất thứ tự gốc. HashMap O(n) time, O(n) space, giữ index gốc. Nếu cần preserve order (như LeetCode Two Sum I yêu cầu trả về index gốc), HashMap là lựa chọn tự nhiên.

---

**Câu 15:** Cho linked list `1 → 2 → 3 → 4 → 5 → null`. Tìm middle node bằng two pointers (slow/fast). Kết quả là:

- A. Node 2
- B. Node 3
- C. Node 4
- D. Node null

> **Đáp án: B**  
> **Giải thích:** Slow di chuyển 1 bước, fast 2 bước. Khi fast tới cuối (5 hoặc null), slow ở giữa. Với 5 node, slow dừng tại node 3 (middle thực sự). Đây là kỹ thuật chuẩn để tìm midpoint linked list không cần biết độ dài.

---

**Câu 16:** Merge 2 mảng sorted in-place (vào mảng đầu, mảng đầu đủ chỗ trống ở cuối) hiệu quả nhất khi dùng two pointers theo hướng:

- A. Cả 2 con trỏ từ trái sang phải
- B. Cả 2 con trỏ từ phải sang trái
- C. 1 trái → phải, 1 phải → trái
- D. Bắt đầu từ giữa mỗi mảng

> **Đáp án: B**  
> **Giải thích:** Nếu merge từ trái sang phải, ghi vào đầu mảng đầu sẽ ghi đè phần tử chưa đọc. Merge từ phải sang trái (ghi vào cuối mảng đầu) tránh hoàn toàn collision vì các vị trí cuối còn trống.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Cho hàm sau (Python). Output là gì với input `nums = [1, 1, 2]`?

```python
def f(nums):
    slow = 0
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]
    return slow + 1, nums
```

- A. `(3, [1, 1, 2])`
- B. `(2, [1, 2, 2])`
- C. `(2, [1, 1, 2])`
- D. `(3, [1, 2, 2])`

> **Đáp án: B**  
> **Giải thích:** Đây là remove duplicates in-place. fast=1: nums[1]=1=nums[0], skip. fast=2: nums[2]=2≠nums[0]=1 → slow=1, nums[1]=2 → mảng thành [1,2,2]. Return (slow+1=2, [1,2,2]).

---

**Câu 18:** Vì sao trong Floyd's algorithm, slow di chuyển 1 bước và fast 2 bước (chứ không phải 1 và 3, hoặc 2 và 3)?

- A. Đây là tốc độ duy nhất đảm bảo hai con trỏ gặp nhau
- B. Hiệu tốc độ = 1 là nhỏ nhất, giúp tránh "vượt qua nhau" mà không gặp; với cycle độ dài L, sau ≤ L bước phải gặp
- C. Đây là quy ước Floyd đặt ra không có lý do toán học cụ thể
- D. Vì 2 là số chẵn duy nhất phù hợp

> **Đáp án: B**  
> **Giải thích:** Với hiệu tốc độ d, khoảng cách trong cycle thu hẹp d đơn vị/bước. Nếu d=1 và L là độ dài cycle, sau tối đa L bước sẽ gặp. Với d=2, có rủi ro: nếu khoảng cách ban đầu lẻ và L chẵn, hai con trỏ có thể "nhảy qua nhau" mãi mà không trùng vị trí. d=1 đảm bảo gặp.

---

**Câu 19:** Với bài tổng quát kSum (tìm k số có tổng bằng target, không lặp), complexity tốt nhất dùng sort + recursion + two pointers ở đáy là?

- A. O(n^k)
- B. O(n^(k-1))
- C. O(n log n + k!)
- D. O(2^n)

> **Đáp án: B**  
> **Giải thích:** Sort O(n log n). Đệ quy fix k-2 vòng ngoài O(n^(k-2)), đáy là 2Sum bằng two pointers O(n) → tổng O(n^(k-1)). Ví dụ: 2Sum O(n), 3Sum O(n²), 4Sum O(n³).

---

**Câu 20:** Phân tích đoạn code sau. Nó giải bài gì và complexity thế nào?

```python
def g(s):
    l, r = 0, len(s) - 1
    while l < r:
        if not s[l].isalnum():
            l += 1
        elif not s[r].isalnum():
            r -= 1
        elif s[l].lower() != s[r].lower():
            return False
        else:
            l += 1
            r -= 1
    return True
```

- A. Đếm số ký tự alphanumeric, O(n)
- B. Kiểm tra palindrome (bỏ qua ký tự không phải chữ/số, case-insensitive), O(n)
- C. So sánh 2 chuỗi, O(n²)
- D. Đảo ngược chuỗi, O(n)

> **Đáp án: B**  
> **Giải thích:** Hai con trỏ đối hướng, skip ký tự không alphanumeric, so sánh lowercase. Đây là classic palindrome check. Mỗi con trỏ di chuyển tối đa n lần → O(n). O(1) space.

---

**Câu 21:** Cho mảng đã sort `[-4, -1, -1, 0, 1, 2]`. Số triplet (a, b, c) khác nhau có tổng bằng 0 (theo bài 3Sum chuẩn) là?

- A. 1
- B. 2
- C. 3
- D. 4

> **Đáp án: B**  
> **Giải thích:** Sau khi sort + chạy 3Sum với skip duplicate: [-1, -1, 2] (tổng 0) và [-1, 0, 1] (tổng 0). Chú ý không tính [-1, 0, 1] hai lần dù có 2 số -1; skip duplicate cho vòng ngoài i bỏ qua trường hợp trùng.

---

**Câu 22:** Một biến thể two pointers cùng hướng có thể được mở rộng để giải bài "longest substring with at most K distinct characters". Đây thực chất là kỹ thuật nào?

- A. Sliding window co giãn (variable-size)
- B. Binary search trên answer
- C. Monotonic stack
- D. Prefix sum + hashmap

> **Đáp án: A**  
> **Giải thích:** Two pointers cùng hướng với window co giãn — chính là sliding window biến thể variable-size. Right mở rộng đến khi vi phạm điều kiện (>K distinct), left co lại đến khi hợp lệ. Đây là cầu nối giữa two pointers tổng quát và chuyên đề `05_sliding_window`.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | A      |
| 2   | C      | 13  | A      |
| 3   | B      | 14  | D      |
| 4   | B      | 15  | B      |
| 5   | A      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | D      | 18  | B      |
| 8   | B      | 19  | B      |
| 9   | B      | 20  | B      |
| 10  | B      | 21  | B      |
| 11  | D      | 22  | A      |
