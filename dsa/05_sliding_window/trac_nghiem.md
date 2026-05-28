# Trắc nghiệm — Sliding Window

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Sliding Window đặc biệt phù hợp cho bài toán xử lý gì?

- A. Subsequence không liên tiếp
- B. Subarray hoặc substring liên tiếp
- C. Tìm phần tử trong cây
- D. Tìm đường đi ngắn nhất trong đồ thị

> **Đáp án: B**  
> **Giải thích:** Sliding window duy trì một cửa sổ liên tiếp [left, right]. Với subsequence không liên tiếp (ví dụ "longest increasing subsequence"), cửa sổ không thể trượt liên tục → phải dùng DP.

---

**Câu 2:** Khi tính tổng K phần tử liên tiếp với sliding window, mỗi lần trượt 1 bước cần bao nhiêu phép cộng/trừ?

- A. K
- B. 2 (1 cộng, 1 trừ)
- C. 1
- D. K-1

> **Đáp án: B**  
> **Giải thích:** Cộng phần tử mới vào, trừ phần tử cũ ra. Đây chính là lý do sliding window đạt O(n) thay vì O(n×k).

---

**Câu 3:** Time complexity của bài "Tìm tổng lớn nhất của k phần tử liên tiếp" bằng sliding window là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n × k)

> **Đáp án: C**  
> **Giải thích:** Mỗi phần tử được thêm vào và xóa khỏi window đúng 1 lần → tổng cộng O(n) operations.

---

**Câu 4:** Sliding Window thuộc loại nào trong các kỹ thuật sau?

- A. Divide and Conquer
- B. Dynamic Programming
- C. Two Pointers (biến thể cùng hướng)
- D. Backtracking

> **Đáp án: C**  
> **Giải thích:** Sliding Window là trường hợp đặc biệt của two pointers cùng hướng, chuyên cho subarray/substring liên tiếp. Hai con trỏ left, right đều di chuyển từ trái sang phải.

---

**Câu 5:** Trong fixed-size window, mối quan hệ giữa left và right là?

- A. left luôn bằng 0
- B. right - left + 1 = K (hằng số)
- C. right = 2 × left
- D. left = right - log(K)

> **Đáp án: B**  
> **Giải thích:** Theo định nghĩa fixed-size, cửa sổ luôn có đúng K phần tử → right - left + 1 = K. Cả hai con trỏ tiến cùng tốc độ sau khi đạt độ rộng K.

---

**Câu 6:** Bài "Longest substring without repeating characters" dùng sliding window và cần thêm cấu trúc dữ liệu nào?

- A. Stack
- B. Hash Map / Hash Set
- C. Binary Search Tree
- D. Heap

> **Đáp án: B**  
> **Giải thích:** Cần kiểm tra ký tự lặp trong window O(1) → hash set/map là tự nhiên. Stack không phù hợp vì không hỗ trợ tìm kiếm O(1). BST O(log n), Heap không hỗ trợ tìm kiếm theo key.

---

**Câu 7:** Variable-size window khác fixed-size window ở điểm nào?

- A. Variable-size không có left pointer
- B. Variable-size cho phép cửa sổ co giãn theo điều kiện
- C. Variable-size luôn nhanh hơn
- D. Variable-size không cần update incremental

> **Đáp án: B**  
> **Giải thích:** Variable-size mở rộng right đến khi vi phạm invariant, rồi co left để khôi phục. Fixed-size luôn giữ độ rộng K cố định.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Cho mảng `[1, 4, 2, 10, 2, 3, 1, 0, 20]` và K = 4. Tổng lớn nhất của K phần tử liên tiếp là?

- A. 17
- B. 24
- C. 27
- D. 30

> **Đáp án: B**  
> **Giải thích:** Cửa sổ [10, 2, 3, 1] = 16, [2, 3, 1, 0] = 6, [3, 1, 0, 20] = 24, [1, 4, 2, 10] = 17, [4, 2, 10, 2] = 18, [2, 10, 2, 3] = 17. Lớn nhất = 24.

---

**Câu 9:** Tại sao sliding window không áp dụng trực tiếp cho bài "subarray sum equals K" khi mảng có số âm?

- A. Vì số âm không thể cộng trừ
- B. Vì tổng cửa sổ không còn tăng đơn điệu khi mở rộng → mất monotonicity
- C. Vì số âm phá vỡ time complexity O(n)
- D. Vì hash map không lưu được số âm

> **Đáp án: B**  
> **Giải thích:** Sliding window co/dãn dựa trên giả định "khi right tiến, sum tăng; khi left tiến, sum giảm". Với số âm, mở rộng cửa sổ có thể làm sum giảm → không biết khi nào nên co left. Phải dùng prefix sum + hashmap.

---

**Câu 10:** Trong bài "Longest substring with at most K distinct characters", khi số ký tự distinct vượt K, ta phải:

- A. Reset toàn bộ window
- B. Co left về phía right đến khi số ký tự distinct ≤ K
- C. Tăng right bỏ qua ký tự đó
- D. Sort lại chuỗi

> **Đáp án: B**  
> **Giải thích:** Variable-size window: khi invariant (≤ K distinct) bị phá, co left từ từ và cập nhật counter; khi 1 ký tự có count về 0 → giảm số distinct. Dừng co khi distinct ≤ K trở lại.

---

**Câu 11:** Sliding Window Maximum (max của mỗi cửa sổ độ rộng k) đạt O(n) khi dùng:

- A. Stack
- B. Monotonic Deque (deque giảm dần)
- C. Max Heap
- D. Sorted Set

> **Đáp án: B**  
> **Giải thích:** Monotonic deque giữ index theo thứ tự giá trị giảm dần. Front luôn là max. Mỗi phần tử push/pop đúng 1 lần → O(n) amortized. Max Heap O(n log k), Sorted Set O(n log k).

---

**Câu 12:** Cho chuỗi `"abcabcbb"`. Longest substring without repeating có độ dài là?

- A. 2
- B. 3
- C. 4
- D. 8

> **Đáp án: B**  
> **Giải thích:** "abc" có độ dài 3 — không lặp. Khi gặp 'a' tiếp theo (index 3), left phải nhảy đến index 1. Window mới "bca" cũng dài 3. Tiếp tục đến cuối không có window nào > 3.

---

**Câu 13:** Trong bài "Smallest subarray sum ≥ target" (số dương), khi `total >= target`:

- A. Right tiếp tục mở rộng
- B. Cập nhật best, sau đó co left và trừ nums[left] khỏi total
- C. Dừng vòng lặp
- D. Reset total về 0

> **Đáp án: B**  
> **Giải thích:** Khi đã đạt target, có thể có window ngắn hơn cũng đạt → co left, kiểm tra liên tục, cập nhật best mỗi khi vẫn còn ≥ target.

---

**Câu 14:** Time complexity tổng của Min Window Substring là?

- A. O(n × m)
- B. O(n + m)
- C. O(n × log m)
- D. O(n²)

> **Đáp án: B**  
> **Giải thích:** n = len(s), m = len(t). Build counter O(m). Two pointers trên s: mỗi vị trí được left và right visit tối đa 1 lần → O(n) amortized. Tổng O(n + m).

---

**Câu 15:** Trick "Subarrays with exactly K distinct = atMost(K) - atMost(K-1)" dựa trên ý tưởng gì?

- A. Inclusion-exclusion: subarrays với ≤ K trừ subarrays với ≤ K-1 = chính xác K
- B. Binary search
- C. Divide and conquer
- D. DP top-down

> **Đáp án: A**  
> **Giải thích:** "Exactly K" = "at most K" trừ "at most K-1". Cả hai bài "at most" đều giải được bằng sliding window O(n). Đây là kỹ thuật cổ điển cho bài exactly K khó hơn at most K.

---

**Câu 16:** Khi nào nên dùng Prefix Sum + HashMap thay vì Sliding Window cho bài subarray sum?

- A. Khi mảng chỉ có số dương
- B. Khi mảng có số âm hoặc cần đếm số subarray (không chỉ tồn tại)
- C. Khi K rất nhỏ
- D. Khi mảng đã sort

> **Đáp án: B**  
> **Giải thích:** Số âm phá monotonicity của sliding window. Đếm số subarray sum = K cũng cần lưu tất cả prefix sums đã thấy → hashmap. Sliding window chỉ giải được dạng "tồn tại" hoặc "min/max length" với số dương.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích đoạn code sau, nó giải bài gì?

```python
def f(s, k):
    cnt = {}
    left = 0
    best = 0
    for right, ch in enumerate(s):
        cnt[ch] = cnt.get(ch, 0) + 1
        while len(cnt) > k:
            cnt[s[left]] -= 1
            if cnt[s[left]] == 0:
                del cnt[s[left]]
            left += 1
        best = max(best, right - left + 1)
    return best
```

- A. Longest substring without repeating characters
- B. Longest substring with at most K distinct characters
- C. Min window substring chứa K ký tự
- D. Đếm số subarray có K ký tự distinct

> **Đáp án: B**  
> **Giải thích:** `cnt` đếm tần suất từng ký tự trong window. `len(cnt)` = số ký tự distinct. Khi vượt K, co left. Theo dõi `best` = độ dài window lớn nhất hợp lệ.

---

**Câu 18:** Tại sao monotonic deque cho Sliding Window Maximum đạt O(n) thay vì O(n × k)?

- A. Vì k luôn nhỏ
- B. Mỗi phần tử được push vào deque đúng 1 lần và pop đúng 1 lần → tổng thao tác ≤ 2n
- C. Vì deque có operation O(1)
- D. Vì duyệt theo binary search

> **Đáp án: B**  
> **Giải thích:** Phân tích amortized: dù vòng `while` bên trong có thể pop nhiều lần trong 1 iteration, tổng số pop qua toàn thuật toán ≤ n (vì mỗi index chỉ push 1 lần và pop 1 lần). Tổng chi phí ≤ 2n → O(n).

---

**Câu 19:** Cho mảng số nguyên `[1, -2, 3, 10, -4, 7, 2, -5]` và target = 15. Smallest subarray có tổng = 15 dùng cách nào tốt nhất?

- A. Sliding window cố định
- B. Sliding window co giãn
- C. Prefix sum + hashmap
- D. Brute force O(n²) là cách duy nhất

> **Đáp án: C**  
> **Giải thích:** Mảng có số âm → sliding window co giãn không đảm bảo đúng. Prefix sum + hashmap O(n): với mỗi prefix sum P[i], tìm xem có P[j] = P[i] - 15 trong các prefix đã thấy không. Lưu (prefix sum → index) trong hashmap.

---

**Câu 20:** Phân tích lỗi trong đoạn code sau (đếm số 1 liên tiếp dài nhất sau khi flip tối đa K số 0):

```python
def longest_ones(nums, k):
    left = 0
    zeros = 0
    best = 0
    for right in range(len(nums)):
        if nums[right] == 0:
            zeros += 1
        if zeros > k:
            if nums[left] == 0:
                zeros -= 1
            left += 1
        best = max(best, right - left + 1)
    return best
```

- A. Không có lỗi, code đúng và optimal
- B. Sai vì dùng `if zeros > k` thay vì `while zeros > k`; tuy nhiên với invariant này (mỗi iter zeros tăng tối đa 1), trường hợp `if` vẫn đúng vì zeros vượt k tối đa 1
- C. Sai vì không reset zeros khi gặp số 1
- D. Sai vì best phải tính sau khi loop kết thúc

> **Đáp án: B**  
> **Giải thích:** Đây là tinh tế. Dùng `if` vẫn cho kết quả đúng vì mỗi bước right chỉ làm zeros tăng tối đa 1 (vượt k đúng 1 đơn vị). Tuy nhiên, thay vào đó window có thể không co về kích thước hợp lệ — chỉ trượt cố định độ dài tối ưu đã đạt được. Đây là pattern phổ biến: window không cần thực sự thu nhỏ về hợp lệ, chỉ cần "không lớn hơn best đã thấy".

---

**Câu 21:** Permutation in String (kiểm tra s2 có chứa permutation của s1) dùng sliding window kích thước:

- A. Bất kỳ
- B. K = len(s1) cố định, kiểm tra 2 counter mỗi window
- C. K thay đổi tùy theo s1
- D. K = len(s2) - len(s1)

> **Đáp án: B**  
> **Giải thích:** Permutation của s1 có đúng độ dài len(s1) → fixed window K = len(s1). Mỗi window so sánh counter ký tự với counter của s1; trùng → tồn tại permutation. So sánh counter có thể tối ưu bằng đếm số ký tự "matched".

---

**Câu 22:** Bài "Substring với at most K replacements để toàn ký tự giống nhau" (Longest Repeating Character Replacement). Invariant chính của sliding window là?

- A. `right - left + 1 ≤ K`
- B. `(right - left + 1) - max_freq_in_window ≤ K`, tức số ký tự cần thay ≤ K
- C. Mỗi ký tự xuất hiện ≤ K lần
- D. Tổng số ký tự khác nhau ≤ K

> **Đáp án: B**  
> **Giải thích:** Trong window, để biến thành toàn ký tự giống nhau, ta giữ ký tự xuất hiện nhiều nhất (max_freq) và thay tất cả phần còn lại. Số phải thay = window_size - max_freq. Invariant: số phải thay ≤ K. Khi vi phạm, co left.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | B      | 13  | B      |
| 3   | C      | 14  | B      |
| 4   | C      | 15  | A      |
| 5   | B      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | B      | 19  | C      |
| 9   | B      | 20  | B      |
| 10  | B      | 21  | B      |
| 11  | B      | 22  | B      |
