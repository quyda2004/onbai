# Backtracking — Quay Lui

## 1. Giải thích cho người mới hoàn toàn

Tưởng tượng bạn đi vào một mê cung tìm lối ra. Chiến lược của bạn:
1. Đi vào lối nào đó.
2. Nếu thấy đường cụt → **quay lại** ngã rẽ gần nhất, thử lối khác.
3. Nếu còn đi được → đi tiếp.
4. Nếu thử hết mọi lối ở ngã rẽ → quay lại ngã rẽ trước nữa.

Đó là **backtracking**: thử từng khả năng, gặp ngõ cụt thì **quay lui** thử khả năng khác.

### Ví dụ — Mật khẩu 4 chữ số

Bạn quên mật khẩu thẻ ATM 4 chữ số. Bạn muốn thử tất cả tổ hợp:
```
0000, 0001, 0002, ..., 0009,
0010, 0011, ..., 0099,
0100, ...
...
9999
```

Cách "viết code" tự nhiên: 4 vòng for lồng nhau. Nhưng nếu là mật khẩu **n chữ số** (n bất kỳ), bạn không thể viết n vòng for. → Phải đệ quy: "thử mỗi giá trị cho chữ số đầu, với mỗi giá trị đó, đệ quy thử tiếp cho các chữ số còn lại."

Đó là cấu trúc backtracking.

### Ví dụ — N-Queens

Đặt N quân hậu trên bàn cờ N×N sao cho không quân nào "ăn" quân nào (không cùng hàng, cột, đường chéo).

Backtracking:
1. Đặt hậu vào hàng 1, cột 1.
2. Sang hàng 2, thử cột 1 — bị ăn → thử cột 2 — bị ăn → ... → tìm được cột hợp lệ → đi tiếp.
3. Đến lúc nào đó không tìm được cột nào hợp lệ → **quay lại hàng trước**, đổi vị trí cũ sang vị trí khác.
4. Cứ thế cho đến khi đặt được N quân, hoặc đã thử hết khả năng.

### Bản chất

Backtracking = DFS trên cây các khả năng + "undo" khi đi ngược ra. Mỗi node trong cây là một state (lựa chọn đã thực hiện đến giờ). Mỗi cạnh là một quyết định mới. Khi state không còn dẫn đến lời giải → cắt nhánh, quay lui.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Mô hình tổng quát

Backtracking giải bài toán có dạng "tìm tất cả / đếm số / kiểm tra tồn tại các cấu hình thỏa điều kiện". Mỗi cấu hình được xây dần theo các "quyết định" tuần tự.

Template chung:
```
def backtrack(state):
    if is_solution(state):
        record(state)
        return
    for choice in candidates(state):
        if is_valid(state, choice):
            apply(state, choice)         # bước "đi"
            backtrack(state)
            undo(state, choice)          # bước "quay lui"
```

3 thành phần then chốt:
1. **State representation**: cách biểu diễn lời giải đang xây.
2. **Candidates**: lựa chọn khả thi tại mỗi bước.
3. **Pruning** (cắt nhánh): từ chối nhánh không thể dẫn đến lời giải hợp lệ → tiết kiệm thời gian khủng khiếp.

### Các bài toán kinh điển

**Sinh tổ hợp / hoán vị**
- Subsets: liệt kê tất cả 2ⁿ tập con.
- Permutations: liệt kê n! hoán vị.
- Combinations: chọn k phần tử từ n, C(n, k).
- Subsets II, Permutations II: có duplicate, cần skip để tránh trùng.

**Constraint Satisfaction**
- N-Queens, Sudoku Solver.
- Word Search trong matrix.
- Graph Coloring.

**Phân hoạch / chia tập**
- Partition to Equal Sum Subsets.
- Palindrome Partitioning.

**Sinh chuỗi/cây**
- Generate Parentheses.
- Restore IP Addresses.
- Letter Combinations of Phone Number.

### Pruning — Cắt nhánh

Đây là điểm phân biệt backtracking thực sự với brute force. Các kiểu pruning:

1. **Feasibility pruning**: hiện tại đã vi phạm ràng buộc → cắt (vd N-Queens conflict).
2. **Bound pruning** (branch and bound): ước lượng best có thể đạt từ nhánh này; nếu kém hơn best đã biết → cắt.
3. **Symmetry pruning**: 2 lựa chọn đối xứng → chỉ thử 1 (vd N-Queens hàng đầu chỉ thử nửa cột).
4. **Duplicate pruning**: sort + skip giá trị bằng nhau ở cùng level.

Với bài có pruning tốt, complexity thực tế nhỏ hơn worst-case rất nhiều.

### Backtracking vs DP vs Greedy

| | Backtracking | DP | Greedy |
|-|--------------|------|--------|
| Khi nào | Liệt kê/check tồn tại với ràng buộc cứng | Tối ưu có optimal substructure + overlap | Tối ưu có greedy property |
| Subproblems lưu | Không (thường) | Có | Không |
| Complexity | Mũ (worst case) | Polynomial | Linear/n log n |
| Đảm bảo optimal | Có (vét toàn bộ) | Có | Tùy bài |

Một số bài có thể giải bằng cả backtracking và DP — DP nhanh hơn khi có overlap. Backtracking phải khi không cần optimal mà cần liệt kê hoặc kiểm tra tồn tại.

### Iterative DFS vs Recursive

Backtracking thường viết đệ quy do code rất sạch. Iterative phải dùng stack thủ công kèm trạng thái "đã/đang thử lựa chọn nào" → cồng kềnh. Trừ trường hợp recursion depth quá lớn, giữ đệ quy.

### Memoization trên backtracking?

Khi backtracking gặp overlapping subproblems → biến thành DP. Word Break là ví dụ: viết backtracking trước, thêm memo → O(n²) thay vì exponential.

---

## 3. Định nghĩa chính xác

**Backtracking**: Kỹ thuật thuật toán xây dựng lời giải từng bước (incremental), tại mỗi bước thử mọi lựa chọn khả thi, và khi phát hiện không thể tiếp tục đến lời giải hợp lệ thì hoàn tác bước cuối (undo) và thử lựa chọn khác.

**Search tree**: Cây các state, root là state rỗng (chưa quyết định gì), mỗi node là một state, mỗi cạnh là một quyết định.

**Pruning**: Cắt bỏ subtree không cần khám phá (vì vi phạm ràng buộc hoặc không thể tốt hơn lời giải hiện tại).

**Branch and Bound**: Backtracking + cận trên/dưới ước lượng → cắt nhánh dựa trên cận.

---

## 4. Bảng Độ phức tạp đầy đủ

### Bài toán kinh điển

| Bài toán | Time (worst) | Space (excl output) | Output size | Ghi chú |
|----------|--------------|---------------------|-------------|---------|
| Subsets (n items) | O(n × 2ⁿ) | O(n) recur | 2ⁿ tập | n cho copy mỗi tập |
| Subsets II (có dup) | O(n × 2ⁿ) | O(n) | ≤ 2ⁿ | Skip duplicate |
| Permutations | O(n × n!) | O(n) | n! hoán vị | n cho copy mỗi hoán vị |
| Permutations II | O(n × n!) | O(n) | ≤ n! | Skip duplicate |
| Combinations C(n, k) | O(k × C(n,k)) | O(k) | C(n, k) | |
| N-Queens | O(n!) worst | O(n²) board | Số nghiệm | Pruning giúp nhiều |
| Sudoku Solver | O(9^m) | O(81) | 1 (nghiệm) | m = ô trống |
| Generate Parentheses (n cặp) | O(4ⁿ / √n) | O(n) | Cₙ Catalan | |
| Word Search | O(M × N × 4^L) | O(L) recur | True/False | M×N grid, L = length |
| Letter Combinations Phone | O(4ⁿ × n) | O(n) | Tích số letter | n = digits |
| Partition to K Equal Sum | O(k × 2ⁿ) | O(n) | True/False | Bitmask DP cải thiện |
| Palindrome Partitioning | O(n × 2ⁿ) | O(n) | Cấu hình | |
| Restore IP Addresses | O(1) chặn bởi 3⁴=81 | O(1) | ≤ 81 | Cố định 4 đoạn |
| Graph Coloring | O(m^V) | O(V) | Có hay không | V đỉnh, m màu |

### Tổng quan complexity theo loại

| Loại | Time |
|------|------|
| Liệt kê 2ⁿ subset | Ω(2ⁿ) |
| Liệt kê n! hoán vị | Ω(n!) |
| Liệt kê C(n,k) | Ω(C(n,k)) |
| N-Queens | NP-hard, exponential worst |
| Sudoku | NP-complete (generalized) |
| TSP nhỏ | Backtracking + bound; DP bitmask nhanh hơn O(n²·2ⁿ) |

Lưu ý: trong nhiều bài backtracking, complexity output đã là exponential → thuật toán không thể nhanh hơn output. Chỉ cải thiện được constant factor và pruning thực tế.

---

## 5. Code mẫu

### Template tổng quát

```python
def backtrack(state, results):
    if is_solution(state):
        results.append(copy(state))
        return
    for choice in candidates(state):
        if is_valid(state, choice):
            state.apply(choice)
            backtrack(state, results)
            state.undo(choice)
```

### Subsets (mọi tập con)

```python
def subsets(nums):
    res = []
    path = []
    def bt(start):
        res.append(path[:])  # copy
        for i in range(start, len(nums)):
            path.append(nums[i])
            bt(i + 1)
            path.pop()
    bt(0)
    return res

print(subsets([1, 2, 3]))
# [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

### Permutations

```python
def permute(nums):
    res = []
    n = len(nums)
    used = [False] * n
    path = []
    def bt():
        if len(path) == n:
            res.append(path[:])
            return
        for i in range(n):
            if used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            bt()
            path.pop()
            used[i] = False
    bt()
    return res

print(permute([1, 2, 3]))
# [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

### Permutations II (có duplicate)

```python
def permute_unique(nums):
    res = []
    nums.sort()
    n = len(nums)
    used = [False] * n
    path = []
    def bt():
        if len(path) == n:
            res.append(path[:])
            return
        for i in range(n):
            if used[i]:
                continue
            # Skip duplicate: nếu nums[i] == nums[i-1] và nums[i-1] chưa dùng
            # (chưa dùng nghĩa là cây nhánh "đi trước" đã xét) → tránh trùng
            if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
                continue
            used[i] = True
            path.append(nums[i])
            bt()
            path.pop()
            used[i] = False
    bt()
    return res

print(permute_unique([1, 1, 2]))
# [[1,1,2], [1,2,1], [2,1,1]]
```

### Combinations C(n, k)

```python
def combine(n, k):
    res = []
    path = []
    def bt(start):
        if len(path) == k:
            res.append(path[:])
            return
        # Pruning: cần thêm k - len(path) số, max bắt đầu là n - (k - len(path)) + 1
        for i in range(start, n - (k - len(path)) + 2):
            path.append(i)
            bt(i + 1)
            path.pop()
    bt(1)
    return res

print(combine(4, 2))
# [[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]
```

### N-Queens

```python
def solve_n_queens(n):
    res = []
    cols = set()
    diag1 = set()  # row - col
    diag2 = set()  # row + col
    placement = []

    def bt(row):
        if row == n:
            res.append(['.' * c + 'Q' + '.' * (n - c - 1) for c in placement])
            return
        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            cols.add(col)
            diag1.add(row - col)
            diag2.add(row + col)
            placement.append(col)
            bt(row + 1)
            cols.remove(col)
            diag1.remove(row - col)
            diag2.remove(row + col)
            placement.pop()

    bt(0)
    return res

print(len(solve_n_queens(4)))  # 2
print(len(solve_n_queens(8)))  # 92
```

### Sudoku Solver

```python
def solve_sudoku(board):
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    empties = []
    for r in range(9):
        for c in range(9):
            v = board[r][c]
            if v == '.':
                empties.append((r, c))
            else:
                rows[r].add(v); cols[c].add(v); boxes[(r//3)*3 + c//3].add(v)

    def bt(idx):
        if idx == len(empties):
            return True
        r, c = empties[idx]
        b = (r // 3) * 3 + c // 3
        for v in '123456789':
            if v in rows[r] or v in cols[c] or v in boxes[b]:
                continue
            rows[r].add(v); cols[c].add(v); boxes[b].add(v)
            board[r][c] = v
            if bt(idx + 1):
                return True
            rows[r].remove(v); cols[c].remove(v); boxes[b].remove(v)
            board[r][c] = '.'
        return False

    bt(0)
    return board
```

### Generate Parentheses

```python
def generate_parentheses(n):
    res = []
    def bt(s, open_cnt, close_cnt):
        if len(s) == 2 * n:
            res.append(s)
            return
        if open_cnt < n:
            bt(s + '(', open_cnt + 1, close_cnt)
        if close_cnt < open_cnt:
            bt(s + ')', open_cnt, close_cnt + 1)
    bt('', 0, 0)
    return res

print(generate_parentheses(3))
# ['((()))', '(()())', '(())()', '()(())', '()()()']
```

### Word Search trong matrix

```python
def exist(board, word):
    m, n = len(board), len(board[0])
    def bt(i, j, k):
        if k == len(word):
            return True
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != word[k]:
            return False
        tmp = board[i][j]
        board[i][j] = '#'  # đánh dấu visited
        found = (bt(i+1, j, k+1) or bt(i-1, j, k+1) or
                 bt(i, j+1, k+1) or bt(i, j-1, k+1))
        board[i][j] = tmp  # khôi phục
        return found

    for i in range(m):
        for j in range(n):
            if bt(i, j, 0):
                return True
    return False
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Cần liệt kê tất cả cấu hình hợp lệ (subsets, permutations, combinations).
- Constraint satisfaction (N-Queens, Sudoku, Graph Coloring).
- Tìm 1 cấu hình hợp lệ (Word Search, IP restore).
- Không tìm được công thức đóng / DP do state quá phức tạp hoặc thay đổi động.
- Có thể pruning mạnh — search tree bị cắt nhiều.

**Không dùng khi:**
- Có DP/greedy giải nhanh hơn (vd bài tối ưu có overlap).
- n quá lớn không cho phép exponential (vd subsets với n > 25 đã 33M tập).
- Bài không có cấu trúc "incremental decision" rõ ràng → kỹ thuật khác phù hợp hơn.
- Output size quá lớn — backtracking liệt kê hết thì không thể nhanh hơn output.

---

## 7. So sánh với các paradigm liên quan

| Tiêu chí | Backtracking | DFS | BFS | DP | Greedy |
|----------|--------------|-----|-----|-----|--------|
| Mục đích chính | Liệt kê/check cấu hình | Duyệt graph/tree | Duyệt graph/tree theo level | Tối ưu/đếm | Tối ưu |
| Có undo | Có (đặc trưng) | Không bắt buộc | Không | N/A | Không |
| Pruning | Có (key feature) | Có (visited) | Có (visited) | N/A | N/A |
| Optimal? | Có (vét hết) | N/A | N/A | Có | Tùy |
| Complexity điển hình | Exponential | O(V+E) | O(V+E) | Polynomial | Linear/n log n |

**Backtracking vs DFS**: DFS chỉ "đi sâu", backtracking là DFS trên cây trạng thái + undo. DFS thường được hiện thực với mảng visited cố định trong suốt traversal; backtracking đánh dấu rồi khôi phục.

**Backtracking vs Brute Force**: Brute force enum tất cả không có pruning. Backtracking có pruning, có cấu trúc đệ quy.

**Backtracking + Memoization = DP**: Khi search tree có overlapping subproblems, thêm cache biến backtracking thành DP top-down.

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Quên undo state sau đệ quy** → state "rò rỉ" sang nhánh khác, kết quả sai.

2. **Copy state khi append vào kết quả**: nếu append reference của `path`, sau khi undo path bị thay đổi → kết quả toàn rỗng/sai. Phải `path[:]` hoặc `list(path)`.

3. **Không skip duplicate đúng cách**: subsets/permutations với input có duplicate phải sort trước và skip khi `nums[i] == nums[i-1]` ở cùng level (tùy biến thể có thể cần điều kiện thêm).

4. **Không đánh dấu visited** trong Word Search / N-Queens → đi vòng lặp vô hạn hoặc dùng lại ô cùng path.

5. **Đặt return sai vị trí**: trong bài "tìm 1 nghiệm" (Sudoku), khi đệ quy trả True phải return ngay; nếu loop tiếp tục → mất nghiệm.

6. **Recursion depth lớn** với Python → stack overflow. `sys.setrecursionlimit(10**6)` hoặc chuyển iterative.

7. **Pruning quá nhẹ** → backtracking chạy thực tế gần brute force, không nhanh hơn đáng kể.

8. **Pruning quá mạnh** sai logic → cắt cả nhánh có nghiệm hợp lệ → kết quả thiếu.

9. **Nhầm "tất cả nghiệm" với "1 nghiệm"** — code khác nhau. Sudoku: return True ngay khi tìm thấy 1; N-Queens (mọi nghiệm): không return, append vào res.

10. **Quên copy board trước khi modify** trong Word Search — dùng kỹ thuật "đánh dấu bằng ký tự đặc biệt + khôi phục" thay vì set bên ngoài.

11. **N-Queens không tận dụng set diag** → check O(n) mỗi vị trí → tổng O(n!·n²). Set check O(1) → giảm xuống O(n!).

---

## 9. Câu hỏi phỏng vấn hay gặp

- Template chung của backtracking. 3 thành phần then chốt?
- Phân biệt backtracking với brute force, với DFS, với DP.
- Subsets — viết, complexity, không gian.
- Permutations và Permutations II — cách handle duplicate.
- Combinations C(n, k) — pruning sớm.
- N-Queens — set conflict cho cột, 2 đường chéo. Vì sao diag = row - col và row + col?
- Sudoku Solver — chiến lược chọn ô empty (vd MRV — minimum remaining values).
- Word Search — kỹ thuật "đánh dấu visited bằng ký tự đặc biệt + khôi phục".
- Generate Parentheses — vì sao cần điều kiện `close_cnt < open_cnt`?
- Palindrome Partitioning — kết hợp DP (precompute palindrome) + backtracking.
- Khi nào backtracking biến thành DP? Word Break là ví dụ.
- Letter Combinations of Phone Number — viết và phân tích complexity.
- Combination Sum (có repetition) vs Combination Sum II (mỗi số dùng 1 lần).
- Branch and bound — TSP nhỏ.
- Cách tránh ra trùng tổ hợp khi sinh subset/permutation.
- Iterative vs recursive backtracking — khi nào dùng iterative.
