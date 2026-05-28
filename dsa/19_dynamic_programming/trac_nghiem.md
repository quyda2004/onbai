# Trắc nghiệm — Dynamic Programming

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Hai thuộc tính bắt buộc của một bài toán DP là?

- A. Linear time và constant space
- B. Optimal substructure và overlapping subproblems
- C. Sortable input và unique elements
- D. Recursive definition và base case

> **Đáp án: B**  
> **Giải thích:** Optimal substructure: tối ưu lớn xây từ tối ưu nhỏ. Overlapping: cùng subproblem được hỏi nhiều lần. Có (1) mà không (2) → divide-and-conquer. Có (2) mà không (1) → memoization đơn thuần.

---

**Câu 2:** Sự khác nhau giữa memoization và tabulation?

- A. Memoization là top-down (recursion + cache), tabulation là bottom-up (vòng lặp điền bảng)
- B. Memoization nhanh hơn
- C. Tabulation chỉ áp dụng cho bài 1D
- D. Không khác nhau

> **Đáp án: A**  
> **Giải thích:** Memoization viết tự nhiên theo công thức truy hồi, recursion + cache. Tabulation lặp tuần tự từ base case. Cùng complexity asymptotic, nhưng tabulation thường nhanh hơn 2-5x trong practice.

---

**Câu 3:** Fibonacci đệ quy thuần (không memo) có complexity?

- A. O(n)
- B. O(n log n)
- C. O(2ⁿ)
- D. O(n²)

> **Đáp án: C**  
> **Giải thích:** Mỗi `fib(n)` gọi 2 lần đệ quy → cây đệ quy có khoảng 2ⁿ node. Memo hoặc tabulation giảm xuống O(n).

---

**Câu 4:** Climbing Stairs (n bậc, mỗi bước 1 hoặc 2 bậc) có công thức truy hồi:

- A. f(n) = f(n-1) × f(n-2)
- B. f(n) = f(n-1) + f(n-2)
- C. f(n) = 2 × f(n-1)
- D. f(n) = f(n/2) + 1

> **Đáp án: B**  
> **Giải thích:** Để đến bậc n: hoặc từ bậc n-1 (bước 1) hoặc từ n-2 (bước 2). Tổng số cách = f(n-1) + f(n-2). Bản chất giống Fibonacci.

---

**Câu 5:** Tối ưu không gian DP thường dùng kỹ thuật?

- A. Hashmap
- B. Rolling array (chỉ giữ vài state gần nhất)
- C. Binary search
- D. Sort lại bảng dp

> **Đáp án: B**  
> **Giải thích:** Nếu dp[i] chỉ phụ thuộc dp[i-1], dp[i-2] (vd Fibonacci), không cần giữ cả mảng. Chỉ cần 2 biến → O(1) thay vì O(n).

---

**Câu 6:** Maximum Subarray (Kadane) complexity time là?

- A. O(1)
- B. O(n)
- C. O(n log n)
- D. O(n²)

> **Đáp án: B**  
> **Giải thích:** 1 vòng lặp, mỗi bước update curr và best trong O(1) → O(n) time, O(1) space.

---

**Câu 7:** Bài "tìm số cách lên n bậc với bước 1, 2, hoặc 3" có công thức?

- A. f(n) = f(n-1) + f(n-2) + f(n-3)
- B. f(n) = f(n-1) × f(n-2) × f(n-3)
- C. f(n) = 3 × f(n-1)
- D. f(n) = f(n/3) + 1

> **Đáp án: A**  
> **Giải thích:** Mở rộng tự nhiên: từ bậc n-1 (bước 1), n-2 (bước 2), n-3 (bước 3). Cộng các cách lại.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** House Robber: cho `[2, 7, 9, 3, 1]`, không rob 2 nhà liền kề. Tối đa rob được?

- A. 11
- B. 12
- C. 13
- D. 19

> **Đáp án: B**  
> **Giải thích:** dp[i] = max(dp[i-1], dp[i-2] + nums[i]). Trace: 2, 7, max(7, 11)=11, max(11, 10)=11, max(11, 12)=12. Tổ hợp tối ưu: 2 + 9 + 1 = 12.

---

**Câu 9:** Knapsack 0/1 dùng rolling 1D array, vì sao phải duyệt ngược (w từ W về weight[i])?

- A. Để code ngắn hơn
- B. Vì duyệt xuôi sẽ làm dp[w-weight[i]] đã bị cập nhật với item i → biến thành unbounded knapsack (item dùng nhiều lần)
- C. Duyệt ngược nhanh hơn
- D. Không có lý do, đó là quy ước

> **Đáp án: B**  
> **Giải thích:** Trong 0/1 knapsack, mỗi item chỉ dùng tối đa 1 lần. Cập nhật dp[w] cần dp[w-weight[i]] **của vòng item i-1**. Duyệt ngược đảm bảo dp[w-weight[i]] chưa bị đè bởi vòng item i hiện tại.

---

**Câu 10:** LIS (Longest Increasing Subsequence) bằng DP O(n²) có công thức?

- A. dp[i] = max(dp[j] + 1) cho mọi j < i mà nums[j] < nums[i]
- B. dp[i] = dp[i-1] + 1
- C. dp[i] = 2 × dp[i-1]
- D. dp[i] = dp[i/2] + 1

> **Đáp án: A**  
> **Giải thích:** dp[i] = độ dài LIS kết thúc tại i. Để mở rộng LIS, cần tìm j < i với nums[j] < nums[i], chọn dp[j] lớn nhất + 1. Loop double → O(n²).

---

**Câu 11:** LIS O(n log n) dùng kỹ thuật?

- A. DP đệ quy có memo
- B. Patience sorting: duy trì mảng `tails`, mỗi giá trị mới dùng binary search để thay thế hoặc append
- C. Sort rồi đếm
- D. Hash map đếm tần suất

> **Đáp án: B**  
> **Giải thích:** Mảng `tails`: tails[i] = giá trị nhỏ nhất kết thúc 1 LIS độ dài i+1. Với mỗi nums[i]: dùng `bisect_left` tìm vị trí cần thay (hoặc append nếu lớn hơn tất cả). Tổng O(n log n).

---

**Câu 12:** Edit Distance giữa "horse" và "ros" là?

- A. 2
- B. 3
- C. 4
- D. 5

> **Đáp án: B**  
> **Giải thích:** horse → rorse (replace h→r) → rose (delete r) → ros (delete e). 3 operations.

---

**Câu 13:** Coin Change 2 (đếm số cách dùng các loại coin để tạo amount). Để đếm **combination** (không phân biệt thứ tự), thứ tự loop là?

- A. Loop ngoài amount, loop trong coin
- B. Loop ngoài coin, loop trong amount
- C. Cả hai đều cho cùng kết quả
- D. Không thứ tự nào đúng

> **Đáp án: B**  
> **Giải thích:** Loop ngoài coin → mỗi coin được thêm "theo lượt" → tránh đếm cùng tổ hợp với thứ tự khác. A đếm permutation (vd amount=3, coins=[1,2]: 1+2 và 2+1 bị đếm thành 2). Đây là pitfall classic.

---

**Câu 14:** Bài toán Knapsack 0/1 có complexity O(n × W) gọi là gì?

- A. Polynomial
- B. Pseudo-polynomial (vì W là giá trị, không phải kích thước input — encode W cần log W bits)
- C. Exponential
- D. Logarithmic

> **Đáp án: B**  
> **Giải thích:** Pseudo-polynomial: polynomial theo giá trị numerical của input, nhưng exponential theo độ dài encoding. Knapsack là NP-hard nếu W rất lớn (không fit trong polynomial của log W).

---

**Câu 15:** Top-down DP (memoization) có nhược điểm so với bottom-up?

- A. Code dài hơn
- B. Recursion overhead + nguy cơ stack overflow với n lớn
- C. Không đảm bảo đúng
- D. Không thể tối ưu space

> **Đáp án: B**  
> **Giải thích:** Mỗi gọi đệ quy có frame overhead. Python recursion limit ~1000 → bài n=10⁵ phải `setrecursionlimit` hoặc chuyển sang tabulation. Tabulation chạy thuần loop, nhanh hơn và không stack overflow.

---

**Câu 16:** State của Knapsack 0/1 chuẩn cần những dimension nào?

- A. dp[i] — chỉ số item
- B. dp[w] — capacity còn lại
- C. dp[i][w] — đã xét đến item i, capacity còn lại w
- D. dp[i][j][w] — i item, j chọn, w capacity

> **Đáp án: C**  
> **Giải thích:** Cần biết "đã xét đến đâu" (i) và "còn bao nhiêu sức chứa" (w). dp[i][w] = max value khi chỉ dùng item từ 1 đến i và capacity w. D thừa dimension.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích đoạn code:

```python
def f(s, t):
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s[i-1] == t[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]
```

Bài toán gì?

- A. Edit Distance
- B. Longest Common Subsequence (LCS)
- C. Longest Common Substring
- D. Number of distinct subsequences

> **Đáp án: B**  
> **Giải thích:** Khi 2 ký tự bằng: dp[i][j] = dp[i-1][j-1] + 1 (mở rộng LCS). Khi khác: lấy max của bỏ ký tự s[i-1] hoặc t[j-1]. Đây là công thức LCS classic. (Substring sẽ reset về 0 khi khác, không lấy max.)

---

**Câu 18:** Best Time to Buy and Sell Stock với k transactions có state DP tối ưu là?

- A. dp[i] — ngày i
- B. dp[i][k] — ngày i, đã làm k transaction
- C. dp[i][k][holding] — ngày i, đã làm k transaction, đang giữ stock hay không (0/1)
- D. dp[i][j] — buy ngày i, sell ngày j

> **Đáp án: C**  
> **Giải thích:** Cần 3 dimension: ngày, số transaction (đếm khi sell), trạng thái holding. Transition: hold = max(hold_yesterday, cash_yesterday - price); cash = max(cash_yesterday, hold_yesterday + price). Time O(n × k), space tối ưu O(k).

---

**Câu 19:** Bitmask DP cho Travelling Salesman (n thành phố) có complexity?

- A. O(n!)
- B. O(2ⁿ)
- C. O(n² × 2ⁿ)
- D. O(n³)

> **Đáp án: C**  
> **Giải thích:** State dp[mask][i] = chi phí tối thiểu thăm tập mask kết thúc ở i. Số state = 2ⁿ × n. Mỗi state transition O(n) (thử mỗi next city). Tổng O(n² × 2ⁿ). Vẫn exponential nhưng tốt hơn brute force O(n!) đáng kể (n=20: 20²×2²⁰ ≈ 4×10⁸ vs 20! ≈ 2.4×10¹⁸).

---

**Câu 20:** Burst Balloons (LeetCode 312) dùng DP interval. State dp[l][r] biểu diễn:

- A. Số bong bóng còn lại trong [l, r]
- B. Điểm tối đa khi nổ TẤT CẢ bong bóng trong (l, r) (loại trừ l và r — l, r là biên giữ lại)
- C. Số cách nổ
- D. Bong bóng cuối cùng còn lại

> **Đáp án: B**  
> **Giải thích:** Trick là chọn k là bong bóng **nổ cuối cùng** trong (l, r): khi đó các bong bóng kề k khi nó nổ chính là l và r (đã được giữ tới cuối). dp[l][r] = max(dp[l][k] + nums[l]×nums[k]×nums[r] + dp[k][r]) cho mọi k trong (l, r). O(n³).

---

**Câu 21:** Tree DP (vd Maximum Path Sum, Diameter of Tree) thường dùng DFS hậu thứ tự vì:

- A. Đó là quy ước
- B. Cần thông tin từ tất cả subtree con trước khi tính cho node hiện tại — đặc trưng của tabulation trên cây
- C. Tiền thứ tự nhanh hơn
- D. Tránh stack overflow

> **Đáp án: B**  
> **Giải thích:** Tree DP: dp[node] phụ thuộc dp[child]. Hậu thứ tự (post-order) đảm bảo child được xử lý trước. Tương tự bottom-up trong DP trên array.

---

**Câu 22:** Re-rooting technique (DP trên cây) giải bài "với mỗi root khả thi, tính dp" trong tổng cộng:

- A. O(n²) — chạy DP n lần với n root
- B. O(n) — chạy DP gốc 1 lần (bottom-up), sau đó "đẩy" thông tin từ parent xuống (top-down) cho mỗi node trong O(1)
- C. O(n log n)
- D. O(2ⁿ)

> **Đáp án: B**  
> **Giải thích:** Re-rooting: lần 1 DFS gốc cố định, tính dp[v] = thông tin cho subtree gốc v. Lần 2 DFS lại, kế thừa thông tin từ parent: f[v] = combine(parent's contribution, child's contribution). Mỗi cạnh "đẩy" O(1) → tổng O(n). Đây là kỹ thuật mạnh cho "sum of distances in tree" và similar.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | A      | 13  | B      |
| 3   | C      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | B      | 16  | C      |
| 6   | B      | 17  | B      |
| 7   | A      | 18  | C      |
| 8   | B      | 19  | C      |
| 9   | B      | 20  | B      |
| 10  | A      | 21  | B      |
| 11  | B      | 22  | B      |
