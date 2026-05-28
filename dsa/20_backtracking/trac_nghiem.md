# Trắc nghiệm — Backtracking

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Backtracking thực chất là?

- A. Tìm đường ngắn nhất
- B. DFS trên cây trạng thái có thao tác "undo" khi quay lui
- C. BFS với visited set
- D. Greedy chọn local optimal

> **Đáp án: B**  
> **Giải thích:** Backtracking = DFS đi xuống cây quyết định + khôi phục state khi quay lên (undo). Đây là điểm khác biệt với DFS thông thường (thường không undo, chỉ đánh dấu visited cố định).

---

**Câu 2:** Template chung của backtracking gồm những thành phần then chốt nào?

- A. Sort, search, return
- B. State representation, candidates generator, validation/pruning
- C. Min, max, sum
- D. Init, transition, base case

> **Đáp án: B**  
> **Giải thích:** 3 thành phần: state (cách biểu diễn lời giải đang xây), candidates (lựa chọn ở mỗi bước), validation hoặc pruning (loại bỏ nhánh không khả thi). D giống DP, không phải backtracking.

---

**Câu 3:** Số subset của tập n phần tử là?

- A. n
- B. n²
- C. 2ⁿ
- D. n!

> **Đáp án: C**  
> **Giải thích:** Mỗi phần tử có 2 lựa chọn (có hoặc không trong subset) → 2ⁿ. Subsets backtracking phải duyệt tất cả → Ω(2ⁿ).

---

**Câu 4:** Số hoán vị của n phần tử khác nhau là?

- A. n
- B. 2ⁿ
- C. n!
- D. n²

> **Đáp án: C**  
> **Giải thích:** n × (n-1) × (n-2) × ... × 1 = n!. Permutations backtracking là Ω(n!).

---

**Câu 5:** Trong backtracking, vì sao phải copy state khi append vào kết quả?

- A. Để tăng tốc
- B. Vì sau khi đệ quy quay về, state sẽ bị undo → nếu giữ reference thì kết quả bị thay đổi
- C. Vì Python yêu cầu
- D. Không cần thiết

> **Đáp án: B**  
> **Giải thích:** `path` là biến mutable dùng chung suốt đệ quy. Nếu append reference, các path đã lưu cũng bị undo theo. Phải `path[:]` hoặc `list(path)` để copy snapshot.

---

**Câu 6:** "Pruning" trong backtracking nghĩa là?

- A. Sắp xếp input
- B. Cắt bỏ nhánh không thể dẫn đến lời giải hợp lệ → tiết kiệm thời gian
- C. In kết quả
- D. Khởi tạo state

> **Đáp án: B**  
> **Giải thích:** Pruning là điểm phân biệt backtracking với brute force. Cắt nhánh sớm dựa trên ràng buộc, bound, hoặc symmetry.

---

**Câu 7:** Bài N-Queens với N = 4 có bao nhiêu nghiệm?

- A. 0
- B. 1
- C. 2
- D. 4

> **Đáp án: C**  
> **Giải thích:** 2 nghiệm phân biệt cho 4-Queens. N-Queens không có nghiệm với N = 2, 3; có nghiệm với N = 1, 4, 5, 6, 7, 8, ...

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Khi input có duplicate (vd `[1, 1, 2]`) và cần liệt kê các permutation **khác nhau**, kỹ thuật để tránh trùng là?

- A. Dùng set lưu các permutation đã thấy
- B. Sort + skip: tại mỗi vị trí, nếu nums[i] == nums[i-1] và nums[i-1] chưa được dùng (đã quay lại) → skip
- C. Sort + random shuffle
- D. Không có cách nào

> **Đáp án: B**  
> **Giải thích:** Sort trước để các giá trị bằng nhau kề nhau. Trong cùng "level" của cây đệ quy, nếu đã thử nums[i-1] (vừa undo), thì nums[i] tương đương → skip để tránh sinh permutation trùng. A dùng được nhưng tốn O(n!) space cho set.

---

**Câu 9:** Bài Generate Parentheses (n cặp). Tại mỗi bước, ta được phép:

- A. Đặt `(` nếu `open < n`, đặt `)` nếu `close < open`
- B. Đặt `(` hoặc `)` bất kỳ
- C. Đặt `(` luôn trước `)`
- D. Đặt bất kỳ chuỗi nào

> **Đáp án: A**  
> **Giải thích:** Điều kiện đảm bảo "valid prefix": (1) tổng `(` ≤ n; (2) số `)` không vượt số `(` đã đặt (nếu không sẽ tạo `)(`...). Đây là pruning chính của bài.

---

**Câu 10:** N-Queens dùng set để check conflict thay vì duyệt mỗi vị trí O(n). Set nào cho đường chéo "/"?

- A. `row + col` (tất cả ô trên cùng / có row + col bằng nhau)
- B. `row - col`
- C. `row × col`
- D. `row / col`

> **Đáp án: A**  
> **Giải thích:** Trên đường chéo "/", khi row tăng 1 thì col giảm 1, nên `row + col` không đổi. Đường chéo "\" có `row - col` không đổi.

---

**Câu 11:** Sudoku solver dùng backtracking có complexity worst-case?

- A. O(1)
- B. O(81²)
- C. O(9^m) với m = số ô trống
- D. O(9!)

> **Đáp án: C**  
> **Giải thích:** Mỗi ô trống có thể thử tối đa 9 chữ số → 9^m nhánh worst case. Pruning (skip khi vi phạm constraint) làm thực tế rất nhỏ.

---

**Câu 12:** Trong Word Search trên matrix, kỹ thuật "đánh dấu visited" tối ưu là?

- A. Dùng set lưu (i, j) đã thăm, copy/undo set
- B. Tạm thời thay board[i][j] bằng ký tự đặc biệt (vd '#'), sau đệ quy khôi phục
- C. Tạo board copy mới mỗi bước
- D. Không cần đánh dấu

> **Đáp án: B**  
> **Giải thích:** Cách B là idiom kinh điển: O(1) thay đổi, O(1) khôi phục, không tốn space cho set. Cách A đúng nhưng overhead. Cách C tệ — O(MN) mỗi bước. D sai — sẽ đi vòng vô hạn.

---

**Câu 13:** Backtracking có thể biến thành DP khi nào?

- A. Khi state space có overlapping subproblems → thêm memoization
- B. Khi input đã sort
- C. Khi bài có nhiều nghiệm
- D. Không bao giờ

> **Đáp án: A**  
> **Giải thích:** Word Break là ví dụ kinh điển: backtracking thuần exponential, thêm memo[i] = "s[i:] có break được không?" → O(n²). Bản chất: nhận diện state lặp lại trong search tree.

---

**Câu 14:** Bài Combination Sum (cho mảng `[2, 3, 6, 7]`, target = 7, mỗi số dùng được vô hạn lần). Backtracking cần truyền tham số gì?

- A. (start, remaining)
- B. (used set, current sum)
- C. (depth)
- D. Không cần tham số

> **Đáp án: A**  
> **Giải thích:** `start` để tránh trùng tổ hợp (chỉ chọn các số từ index start trở đi); `remaining` để biết khi nào dừng (= 0 → tìm thấy; < 0 → backtrack). Dùng số nhiều lần thì gọi đệ quy với `start = i` (không phải `i+1`).

---

**Câu 15:** Restore IP Addresses (chuỗi "25525511135" → các IP hợp lệ) backtracking có bao nhiêu nghiệm tối đa cho chuỗi bất kỳ?

- A. 1
- B. Vô hạn
- C. Bị giới hạn bởi 3⁴ = 81 (mỗi đoạn có 1-3 ký tự, có 4 đoạn)
- D. n!

> **Đáp án: C**  
> **Giải thích:** IP có 4 đoạn, mỗi đoạn 1-3 chữ số. Tổng số cách chia tối đa 3⁴ = 81. Đây là bài backtracking với output size cố định nhỏ → rất nhanh trong thực tế.

---

**Câu 16:** Recursion depth khi backtrack Subsets với n = 20 là?

- A. 1
- B. log₂(20) ≈ 5
- C. 20
- D. 2²⁰ ≈ 1M

> **Đáp án: C**  
> **Giải thích:** Cây đệ quy depth = n (xét từng phần tử có/không). 2²⁰ là số node lá (tức số subset), không phải depth. Recursion depth = n = 20 → an toàn cho Python default.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích code, nó giải bài gì?

```python
def f(n, k):
    res = []
    path = []
    def bt(start):
        if len(path) == k:
            res.append(path[:])
            return
        for i in range(start, n - (k - len(path)) + 2):
            path.append(i)
            bt(i + 1)
            path.pop()
    bt(1)
    return res
```

- A. Permutations P(n, k)
- B. Combinations C(n, k) với pruning sớm `n - (k - len(path)) + 2`
- C. Subsets độ dài k
- D. Power set

> **Đáp án: B**  
> **Giải thích:** Sinh tổ hợp k phần tử từ {1, ..., n}. Pruning: cần thêm `(k - len(path))` số nữa, vị trí cuối cùng có thể bắt đầu là `n - (k - len(path)) + 1` (cộng 1 vì `range` exclusive) → giới hạn vòng lặp, không lãng phí nhánh.

---

**Câu 18:** Skip duplicate trong Subsets II (vd `[1, 2, 2]`): khi nào skip nums[i]?

- A. Khi `i > 0 và nums[i] == nums[i-1]` (bỏ qua trùng ở cùng level — tức không phải level đầu của vòng lặp tại bt(start))
- B. Khi `nums[i] đã được dùng trong path`
- C. Khi `i == 0`
- D. Khi `len(path) == 0`

> **Đáp án: A**  
> **Giải thích:** Sau khi sort, các trùng kề nhau. Trong loop `for i in range(start, n)`, skip khi `i > start và nums[i] == nums[i-1]`. Điều kiện `i > start` (không phải `i > 0`) đảm bảo chỉ skip ở cùng level, không skip qua các nhánh khác nhau của cây.

---

**Câu 19:** Generate Parentheses có số nghiệm là?

- A. n!
- B. 2ⁿ
- C. C(n) = Catalan(n) = (2n)! / ((n+1)! × n!)
- D. n²

> **Đáp án: C**  
> **Giải thích:** Số chuỗi ngoặc hợp lệ với n cặp là số Catalan thứ n. Approximation: 4ⁿ / (n^1.5 × √π). Với n=3: C(3) = 5 (((())), (()()), (())(), ()(()), ()()()).

---

**Câu 20:** Bài "Word Search II" (tìm nhiều từ trong matrix, mỗi từ trong wordlist). Tối ưu hóa quan trọng?

- A. Backtracking độc lập từng từ
- B. Build trie từ wordlist, DFS trên matrix với trie node song hành — share prefix tiết kiệm hàng nghìn lần
- C. Sort wordlist
- D. Hash mỗi từ

> **Đáp án: B**  
> **Giải thích:** Nhiều từ có prefix chung → trie cho phép pruning sớm: khi prefix không tồn tại trong trie thì dừng nhánh ngay. Optimization key cho LeetCode 212. Backtracking thuần từng từ O(W × M × N × 4^L).

---

**Câu 21:** Partition to K Equal Sum Subsets có 2 cách chia state. Cách bitmask DP tốt hơn backtracking khi?

- A. n ≤ 16 (2¹⁶ = 65K state, fit memory) — bitmask DP O(2ⁿ × n) chạy gọn
- B. n > 100
- C. Khi K = 2
- D. Không có khác biệt

> **Đáp án: A**  
> **Giải thích:** Bitmask DP dùng dp[mask] = "có thể chia mask thành các subset hợp lệ không", state = 2ⁿ. Với n ≤ 16, O(2ⁿ × n) ≈ 10⁶, chạy nhanh. Backtracking thuần có thể slow worse case dù pruning tốt.

---

**Câu 22:** Vì sao N-Queens cho N = 8 có 92 nghiệm trong khi 8! = 40320?

- A. Vì backtracking pruning loại bỏ rất nhiều nhánh không hợp lệ (đường chéo, cột conflict) — chỉ một phần rất nhỏ trong số n! cấu hình thỏa cả 3 ràng buộc
- B. Vì N-Queens không yêu cầu duy nhất
- C. Vì tính toán nhầm
- D. Vì 92 là số nghiệm "đặc biệt"

> **Đáp án: A**  
> **Giải thích:** n! hoán vị tương ứng với "đặt 1 hậu mỗi cột, mỗi hàng" — đã loại bỏ ràng buộc hàng/cột. Còn lại ràng buộc đường chéo loại bỏ thêm rất nhiều → 92 cấu hình. Đây là sức mạnh của pruning + reformulation.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | B      | 13  | A      |
| 3   | C      | 14  | A      |
| 4   | C      | 15  | C      |
| 5   | B      | 16  | C      |
| 6   | B      | 17  | B      |
| 7   | C      | 18  | A      |
| 8   | B      | 19  | C      |
| 9   | A      | 20  | B      |
| 10  | A      | 21  | A      |
| 11  | C      | 22  | A      |
