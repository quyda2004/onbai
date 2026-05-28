# Dynamic Programming — Quy Hoạch Động

## 1. Giải thích cho người mới hoàn toàn

Tưởng tượng bạn đang leo một cầu thang 10 bậc. Mỗi lần bạn được bước 1 bậc hoặc 2 bậc. Hỏi: **có bao nhiêu cách lên đến bậc 10**?

**Cách ngây thơ (recursion):** Thử mọi tổ hợp 1 và 2 sao cho tổng = 10. Số lượng tổ hợp tăng theo hàm mũ — với 10 bậc còn được, với 50 bậc thì máy tính cũng chậm.

**Quan sát quan trọng:** Để đến bậc 10, bạn phải đến từ bậc 9 (bước 1 bậc) hoặc từ bậc 8 (bước 2 bậc). Vậy:
> **Số cách lên bậc 10 = Số cách lên bậc 9 + Số cách lên bậc 8**

Tổng quát: `f(n) = f(n-1) + f(n-2)`. Bắt đầu từ `f(1) = 1`, `f(2) = 2`, ta tính dần lên đến `f(10)`.

```
f(1)=1, f(2)=2, f(3)=3, f(4)=5, f(5)=8, f(6)=13, f(7)=21, f(8)=34, f(9)=55, f(10)=89
```

Đó là **Dynamic Programming** — DP.

### Bản chất của DP

DP = **giải bài lớn bằng cách giải các bài nhỏ hơn rồi lưu lại kết quả để không tính lại**.

Có 2 yếu tố then chốt:
1. **Bài toán con (subproblem)** chồng lấn nhau — cùng 1 subproblem được hỏi nhiều lần.
2. **Cấu trúc tối ưu** — kết quả bài lớn phụ thuộc kết quả bài nhỏ theo công thức xác định.

Nếu chỉ có (1), gọi là **memoization** (lưu để tránh tính lại). Cộng cả (2), gọi là **DP**.

### Ví dụ đời thường khác

Bạn đang nấu cơm, làm món A, B, C. Mỗi món cần các bước con. Bước "vo gạo" xuất hiện trong cả A và B. Thay vì vo gạo 2 lần, bạn vo **1 lần rồi dùng cho cả 2**. Đó chính là tinh thần DP — không lặp lại công việc đã làm.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Khi nào áp dụng DP?

Một bài toán có thể giải bằng DP khi nó có 2 thuộc tính:

**1. Overlapping Subproblems** — các bài toán con bị lặp lại nhiều lần trong cây đệ quy.  
Ví dụ Fibonacci đệ quy: `fib(5)` gọi `fib(4)` và `fib(3)`; `fib(4)` lại gọi `fib(3)` và `fib(2)` — `fib(3)` bị tính 2 lần. Quy mô n thì số lần tính lại tăng theo hàm mũ.

**2. Optimal Substructure** — lời giải tối ưu của bài lớn có thể được xây từ lời giải tối ưu của bài nhỏ.  
Ví dụ shortest path: nếu đường ngắn nhất từ A đến C đi qua B, thì A→B phải là đường ngắn nhất từ A đến B.

→ Hai thuộc tính này phân biệt DP với:
- **Divide-and-Conquer** (chia bài lớn thành bài nhỏ độc lập, không overlap — như merge sort).
- **Greedy** (có optimal substructure nhưng không cần lưu trữ subproblem).

### Hai cách triển khai

**Top-Down (Memoization)**:
- Viết hàm đệ quy tự nhiên theo công thức truy hồi.
- Thêm bảng cache (dict, array) lưu kết quả đã tính.
- Khi gặp subproblem đã tính → trả về từ cache.
- Ưu: trực quan, dễ viết theo công thức; chỉ tính các state thực sự cần.
- Nhược: recursion overhead, có thể stack overflow.

**Bottom-Up (Tabulation)**:
- Xác định thứ tự tính các state từ nhỏ đến lớn.
- Lặp tuần tự, không đệ quy.
- Ưu: không recursion, thường nhanh hơn 2-5x trong practice, dễ tối ưu không gian.
- Nhược: phải xác định thứ tự đúng; có thể tính cả các state không cần dùng.

### Các bước thiết kế DP

1. **Xác định state**: tham số nào đủ đại diện cho 1 subproblem? (vd: chỉ số i, hoặc (i, j), hoặc (i, capacity)).
2. **Xác định base case**: state nhỏ nhất, kết quả trivial.
3. **Xây transition (công thức truy hồi)**: dp[state] = f(dp[states con]).
4. **Xác định thứ tự tính** (bottom-up): topological order trên DAG của state dependencies.
5. **Trả về**: state nào tương ứng với câu trả lời cuối.

### Phân loại DP theo dạng state

**1D DP**: dp[i] phụ thuộc dp[i-1], dp[i-2], ...  
Ví dụ: Climbing Stairs, House Robber, Maximum Subarray (Kadane), Longest Increasing Subsequence (O(n²)).

**2D DP**: dp[i][j].  
Ví dụ: Edit Distance, LCS, Knapsack 0/1, Matrix Chain Multiplication, Unique Paths, Coin Change 2.

**DP trên interval**: dp[l][r] = optimal cho subarray [l, r].  
Ví dụ: Burst Balloons, Matrix Chain Multiplication, Palindrome Partitioning.

**DP trên bitmask**: dp[mask] = optimal khi đã chọn tập con (mask).  
Ví dụ: Travelling Salesman O(n² · 2ⁿ), bài toán tô màu.

**DP trên cây (Tree DP)**: dp[node] = optimal cho subtree gốc tại node. DFS hậu thứ tự.  
Ví dụ: Diameter of tree, Maximum Independent Set on Tree, Re-rooting technique.

**DP trên trạng thái**: Bài có "kế hoạch" với nhiều "lựa chọn"; state có thể phức tạp (vd ngày, holding/not holding stock).  
Ví dụ: Best Time to Buy and Sell Stock (mọi biến thể), Paint House.

### Tối ưu không gian

Khi dp[i] chỉ phụ thuộc dp[i-1] (hoặc vài state trước) → có thể dùng **rolling array** giảm O(n) xuống O(1) hoặc O(k).  
Ví dụ Fibonacci: chỉ cần 2 biến `prev, curr` thay vì mảng n phần tử.

Với 2D DP dp[i][j] chỉ phụ thuộc dp[i-1][...] và dp[i][...] → giảm xuống O(min(m, n)) bằng 2 mảng 1D hoặc 1 mảng + biến phụ.

### Trade-off vs các paradigm khác

| | DP | Greedy | Divide-Conquer | Brute Force |
|-|----|--------|----------------|-------------|
| Optimal substructure | Có | Có | Có | N/A |
| Overlapping subproblems | Có | Không | Không | N/A |
| Đảm bảo optimal | Có | Chỉ khi greedy choice đúng | Có | Có |
| Time | Polynomial thường | Thường tốt hơn | Tùy bài | Mũ |
| Khi nào dùng | Có overlap + optimal sub | Greedy choice property | Subproblem độc lập | Bài nhỏ, baseline |

---

## 3. Định nghĩa chính xác

**Dynamic Programming**: Phương pháp giải bài toán tối ưu/đếm bằng cách phân rã thành các bài toán con chồng lấn, lưu kết quả bài toán con để tránh tính lại, và xây dựng lời giải cuối theo công thức truy hồi.

**State**: Tập tham số đủ để xác định một subproblem duy nhất. Tổng số state × thời gian tính mỗi state = total time complexity.

**Transition**: Công thức biểu diễn dp[state] qua dp[state nhỏ hơn].

**Memoization**: Kỹ thuật cache kết quả của hàm đệ quy theo input.

**Tabulation**: Kỹ thuật điền bảng dp theo thứ tự xác định, từ base case lên.

---

## 4. Bảng Độ phức tạp đầy đủ

### Bài toán DP kinh điển

| Bài toán | Time | Space | Space optimized | Ghi chú |
|----------|------|-------|-----------------|---------|
| Fibonacci | O(n) | O(n) | O(1) | Rolling 2 biến |
| Climbing Stairs | O(n) | O(n) | O(1) | Tương tự Fib |
| House Robber | O(n) | O(n) | O(1) | dp[i] = max(dp[i-1], dp[i-2]+nums[i]) |
| Maximum Subarray (Kadane) | O(n) | O(1) | O(1) | dp[i] = max(nums[i], dp[i-1]+nums[i]) |
| Longest Increasing Subseq (DP) | O(n²) | O(n) | O(n) | dp[i] = max LIS kết thúc tại i |
| LIS với binary search | O(n log n) | O(n) | O(n) | Patience sorting |
| Edit Distance | O(m × n) | O(m × n) | O(min(m, n)) | Levenshtein |
| LCS (Longest Common Subseq) | O(m × n) | O(m × n) | O(min(m, n)) | |
| Knapsack 0/1 | O(n × W) | O(n × W) | O(W) | Pseudo-polynomial |
| Unbounded Knapsack | O(n × W) | O(W) | O(W) | |
| Coin Change (min coins) | O(amount × #coins) | O(amount) | O(amount) | |
| Coin Change 2 (# ways) | O(amount × #coins) | O(amount) | O(amount) | |
| Matrix Chain Multiplication | O(n³) | O(n²) | O(n²) | DP interval |
| Burst Balloons | O(n³) | O(n²) | O(n²) | DP interval |
| Travelling Salesman (bitmask) | O(n² · 2ⁿ) | O(n · 2ⁿ) | - | Exponential, n ≤ ~20 |
| Word Break | O(n²) | O(n) | O(n) | + cost gọi trie/set |
| Palindrome Partitioning (min cuts) | O(n²) | O(n²) | O(n²) | + DP precompute palindromes |
| Best Time Buy/Sell Stock (k trans) | O(n × k) | O(n × k) | O(k) | k = số giao dịch |

### Tóm tắt complexity theo state

| State dimension | Bài toán điển hình | Time | Space |
|-----------------|---------------------|------|-------|
| O(n) state, O(1) transition | Fib, House Robber | O(n) | O(n) → O(1) |
| O(n) state, O(n) transition | LIS DP | O(n²) | O(n) |
| O(n²) state, O(1) transition | LCS, Edit Distance | O(n²) | O(n²) → O(n) |
| O(n²) state, O(n) transition | Matrix Chain, Burst Balloons | O(n³) | O(n²) |
| O(2ⁿ) state | TSP, bitmask | O(n² · 2ⁿ) | O(n · 2ⁿ) |

---

## 5. Code mẫu

### Fibonacci — 3 phiên bản

```python
# 1. Recursion thuần — O(2^n), không khả thi
def fib_rec(n):
    if n < 2:
        return n
    return fib_rec(n - 1) + fib_rec(n - 2)

# 2. Top-down memo — O(n) time, O(n) space
def fib_memo(n, memo=None):
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n < 2:
        return n
    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]

# 3. Bottom-up tabulation rolling — O(n) time, O(1) space
def fib_dp(n):
    if n < 2:
        return n
    prev, curr = 0, 1
    for _ in range(2, n + 1):
        prev, curr = curr, prev + curr
    return curr
```

### House Robber

```python
def rob(nums):
    """Không được rob 2 nhà liền kề. Tối đa rob được bao nhiêu?"""
    prev2 = prev1 = 0
    for x in nums:
        prev2, prev1 = prev1, max(prev1, prev2 + x)
    return prev1

print(rob([2, 7, 9, 3, 1]))  # 12 = 2 + 9 + 1
```

### Kadane (Maximum Subarray)

```python
def max_subarray(nums):
    best = curr = nums[0]
    for x in nums[1:]:
        curr = max(x, curr + x)  # tiếp tục hoặc bắt đầu lại
        best = max(best, curr)
    return best

print(max_subarray([-2, 1, -3, 4, -1, 2, 1, -5, 4]))  # 6
```

### LIS (Longest Increasing Subsequence) — O(n log n)

```python
from bisect import bisect_left

def length_of_lis(nums):
    tails = []  # tails[i] = giá trị nhỏ nhất kết thúc 1 LIS độ dài i+1
    for x in nums:
        idx = bisect_left(tails, x)
        if idx == len(tails):
            tails.append(x)
        else:
            tails[idx] = x
    return len(tails)

print(length_of_lis([10, 9, 2, 5, 3, 7, 101, 18]))  # 4 ([2,3,7,18])
```

### Edit Distance (Levenshtein)

```python
def edit_distance(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j],    # delete
                                    dp[i][j-1],   # insert
                                    dp[i-1][j-1]) # replace
    return dp[m][n]

print(edit_distance("horse", "ros"))  # 3
```

### Knapsack 0/1 (rolling 1D)

```python
def knapsack_01(weights, values, W):
    n = len(weights)
    dp = [0] * (W + 1)
    for i in range(n):
        # Duyệt ngược để không dùng item i nhiều lần
        for w in range(W, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    return dp[W]

print(knapsack_01([2, 3, 4, 5], [3, 4, 5, 6], 5))  # 7
```

### Coin Change (min coins)

```python
def coin_change(coins, amount):
    INF = float('inf')
    dp = [0] + [INF] * amount
    for x in range(1, amount + 1):
        for c in coins:
            if c <= x:
                dp[x] = min(dp[x], dp[x - c] + 1)
    return -1 if dp[amount] == INF else dp[amount]

print(coin_change([1, 2, 5], 11))  # 3 (5+5+1)
```

### LCS (Longest Common Subsequence)

```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]

print(lcs("abcde", "ace"))  # 3
```

### Top-down memoization (functools)

```python
from functools import lru_cache

def climbing_stairs(n):
    @lru_cache(None)
    def go(i):
        if i <= 1:
            return 1
        return go(i - 1) + go(i - 2)
    return go(n)
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Bài toán tối ưu (min/max) hoặc đếm số cách.
- Có optimal substructure: tối ưu lớn xây từ tối ưu nhỏ.
- Có overlapping subproblems: cây đệ quy có node lặp.
- State space "đủ nhỏ" để cache (tránh state O(2ⁿ) trừ khi n nhỏ).
- Brute force / recursion thuần quá chậm.

**Không dùng khi:**
- Có greedy choice property → greedy đơn giản hơn (vd Huffman, MST, Activity Selection).
- Bài toán có thể giải bằng công thức đóng (closed-form).
- Subproblem không overlap (vd merge sort) → D&C đủ.
- State space quá lớn không thể cache (vd cần lưu mọi permutation cụ thể).
- Không có cấu trúc rõ ràng — đôi khi phải dùng các paradigm khác (branch and bound, heuristic).

---

## 7. So sánh với các paradigm liên quan

| Tiêu chí | DP | Greedy | Divide-and-Conquer | Backtracking |
|----------|-----|--------|---------------------|--------------|
| Optimal substructure | Yêu cầu | Yêu cầu | Yêu cầu | Không yêu cầu |
| Overlapping subproblems | Có | Không | Không | Có thể |
| Subproblems được lưu | Có | Không | Không (không cần) | Hiếm |
| Phương pháp | Bottom-up hoặc memo | Local optimal | Recursion độc lập | Trial + undo |
| Đảm bảo optimal | Có | Chỉ nếu chọn đúng | Có | Có (nếu duyệt hết) |
| Time điển hình | Polynomial | Linear → n log n | n log n thường | Mũ |
| Ví dụ | Knapsack, LIS, Edit Dist | Activity Selection, MST | Merge Sort, Binary Search | N-Queens, Sudoku |

**DP vs Greedy**: Greedy chọn tốt nhất tại mỗi bước local; chỉ đúng nếu có "greedy choice property". DP xét tất cả lựa chọn (qua transition) và lấy tốt nhất → luôn đúng nếu có optimal substructure.

**DP vs Backtracking**: Backtracking duyệt mọi possibility, undo khi không hợp lệ. DP cộng dồn kết quả từ subproblem. Một số bài giải được cả hai (Word Break), nhưng DP nhanh hơn rất nhiều khi có overlap.

**Memoization vs Tabulation**: Cùng complexity, khác cách triển khai. Memoization dễ viết hơn, tabulation nhanh hơn trong practice và dễ tối ưu space.

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Xác định state thiếu thông tin** → transition không đúng. Ví dụ House Robber II (vòng tròn): cần thêm thông tin "có rob nhà đầu không" → 2 lần chạy DP với 2 sub-array.

2. **Sai thứ tự duyệt trong tabulation** → đọc state chưa được tính. Đặc biệt với 2D DP, phải xác định rõ thứ tự i, j.

3. **Quên base case** hoặc base case sai → toàn bộ kết quả lệch.

4. **Knapsack 0/1 duyệt xuôi trong rolling 1D** → cùng 1 item được dùng nhiều lần (biến thành unbounded knapsack). Phải duyệt ngược từ W về weight[i].

5. **State explosion**: dùng quá nhiều dimension không cần thiết. Ví dụ thêm dimension "step" khi không cần.

6. **Recursion depth** với Python: dp đệ quy n = 10000+ → stack overflow. Dùng `sys.setrecursionlimit` hoặc chuyển sang tabulation.

7. **Lru_cache với mutable state** (list, dict): không hash được → lỗi. Phải convert sang tuple/frozenset.

8. **Coin Change 2 (đếm số cách) bị nhầm thứ tự loop**: nếu loop ngoài là amount, trong là coin → đếm permutation (cùng tập coin nhưng thứ tự khác coi là khác). Cách đúng: loop ngoài coin, trong amount → đếm combination.

9. **Tối ưu space sai**: rolling array nhưng update sai thứ tự → đọc giá trị mới khi cần giá trị cũ.

10. **Floating-point trong DP** với cộng/nhân lặp nhiều → tích lũy sai số. Cần dùng modular arithmetic nếu bài đếm modulo.

11. **Không xét base case 0 element / chuỗi rỗng** trong LCS, Edit Distance → off-by-one trong shape của dp.

---

## 9. Câu hỏi phỏng vấn hay gặp

- Khi nào một bài có thể giải bằng DP? (2 thuộc tính)
- So sánh top-down memoization và bottom-up tabulation. Trade-off?
- Climbing Stairs, Fibonacci — viết 3 phiên bản (recursion, memo, tabulation), so sánh complexity.
- House Robber I, II, III — phân tích biến thể (mảng thẳng → vòng tròn → cây).
- Kadane's algorithm — chứng minh đúng đắn.
- LIS — phương án O(n²) DP và O(n log n) patience.
- LCS — viết DP, in cả subsequence.
- Edit Distance (Levenshtein) — viết, giải thích 3 operation.
- Knapsack 0/1 vs Unbounded — khác biệt trong loop direction.
- Coin Change (min coins) vs Coin Change 2 (count ways) — khác biệt loop order.
- Matrix Chain Multiplication — DP interval, complexity O(n³).
- Word Break — DP + trie/set.
- Best Time to Buy/Sell Stock — phân tích state holding/cash, k transactions.
- Tối ưu không gian DP — kỹ thuật rolling array.
- DP trên cây — viết tree DP cho diameter.
- Khi nào greedy thay được DP? (greedy choice property).
- Bitmask DP cho subset sum / TSP.
- Pseudo-polynomial complexity của Knapsack — vì sao O(nW) không thực sự polynomial?
