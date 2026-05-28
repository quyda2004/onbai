# Trắc Nghiệm: Recursion & Recurrence Relation

---

## Câu hỏi

---

### Câu 1 [Cơ bản]
Đệ quy cần có bao nhiêu thành phần bắt buộc để hoạt động đúng?

A. 1 — chỉ cần recursive case  
B. 2 — base case và recursive case  
C. 3 — base case, recursive case, và accumulator  
D. 4 — base case, recursive case, return value, và parameter

---

### Câu 2 [Cơ bản]
Điều gì xảy ra nếu một hàm đệ quy không có base case?

A. Hàm trả về None  
B. Hàm chạy đúng nhưng chậm  
C. Stack overflow — call stack lấp đầy và chương trình crash  
D. Hàm tự dừng khi stack đầy

---

### Câu 3 [Cơ bản]
Hàm đệ quy `f(n) = n * f(n-1)` với `f(0) = 1` là hàm tính gì?

A. Fibonacci  
B. Power of 2  
C. Factorial  
D. Sum of first n numbers

---

### Câu 4 [Cơ bản]
Số Fibonacci thứ 6 (fib(0)=0, fib(1)=1) là:

A. 5  
B. 8  
C. 13  
D. 6

---

### Câu 5 [Cơ bản]
Trong Python, giới hạn default cho độ sâu đệ quy là bao nhiêu?

A. 100  
B. 500  
C. 1000  
D. 10000

---

### Câu 6 [Cơ bản]
Recurrence relation `T(n) = T(n-1) + O(1)` có solution là:

A. O(log n)  
B. O(n)  
C. O(n log n)  
D. O(2^n)

---

### Câu 7 [Trung bình]
Recurrence relation của Merge Sort là gì? Và kết quả là gì?

A. T(n) = T(n-1) + O(n) → O(n²)  
B. T(n) = 2T(n/2) + O(n) → O(n log n)  
C. T(n) = 2T(n/2) + O(1) → O(n)  
D. T(n) = T(n/2) + O(1) → O(log n)

---

### Câu 8 [Trung bình]
Tail recursion khác non-tail recursion ở điểm nào quan trọng nhất?

A. Tail recursion luôn nhanh hơn  
B. Tail recursion là recursive call cuối cùng, không cần computation sau đó — cho phép TCO  
C. Tail recursion không cần base case  
D. Tail recursion tự động dùng memoization

---

### Câu 9 [Trung bình]
Tower of Hanoi với n đĩa cần bao nhiêu bước tối thiểu?

A. n²  
B. 2n  
C. n log n  
D. 2^n - 1

---

### Câu 10 [Trung bình]
Memoization giúp tối ưu Fibonacci từ O(2^n) xuống O(n) vì:

A. Giảm độ sâu đệ quy xuống log n  
B. Mỗi subproblem `fib(i)` chỉ tính đúng 1 lần, kết quả được cache  
C. Dùng iteration thay vì đệ quy  
D. Dùng tail recursion

---

### Câu 11 [Trung bình]
Stack frame khi gọi đệ quy KHÔNG chứa thứ gì sau đây?

A. Return address  
B. Local variables  
C. Global variables  
D. Function parameters

---

### Câu 12 [Trung bình]
Xét hàm sau:
```python
def f(n):
    if n == 0: return 0
    return f(n-1) + f(n-1)
```
Complexity là bao nhiêu?

A. O(n)  
B. O(n log n)  
C. O(2^n)  
D. O(n²)

---

### Câu 13 [Trung bình]
Recurrence `T(n) = T(n/2) + O(1)` (binary search) có solution là:

A. O(n)  
B. O(log n)  
C. O(n log n)  
D. O(1)

---

### Câu 14 [Trung bình]
`@lru_cache` trong Python làm gì khi áp dụng cho hàm đệ quy?

A. Tự động chuyển sang tail recursion  
B. Cache kết quả của các sub-calls, tránh tính lại — memoization tự động  
C. Tăng recursion limit  
D. Chuyển đệ quy thành iteration

---

### Câu 15 [Nâng cao]
Theo Master Theorem, `T(n) = 4T(n/2) + O(n)` có solution là:

A. O(n log n)  
B. O(n²)  
C. O(n² log n)  
D. O(n^1.5)

---

### Câu 16 [Nâng cao]
Xét đoạn code sau — vấn đề là gì?
```python
def fib(n, memo={}):
    if n in memo: return memo[n]
    if n <= 1: return n
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```

A. Thiếu base case  
B. Default mutable argument `{}` được share giữa tất cả function calls — bug tiềm ẩn  
C. Stack overflow vì không có memoization  
D. Không có vấn đề gì

---

### Câu 17 [Nâng cao]
Space complexity của đệ quy binary search là O(log n) vì:

A. Mỗi call tạo O(log n) variables  
B. Call stack có depth log n — mỗi call chia đôi input  
C. Cần O(log n) memory cho kết quả  
D. Array input có size O(log n)

---

### Câu 18 [Nâng cao]
Theo Master Theorem, `T(n) = 3T(n/3) + O(n)` có solution là:

A. O(n)  
B. O(n log n)  
C. O(n²)  
D. O(n^(log_3 3)) = O(n)

---

### Câu 19 [Nâng cao]
Tại sao Quick Sort có worst case O(n²) stack space?

A. Vì merge operation cần O(n²) memory  
B. Vì khi pivot luôn là phần tử lớn nhất/nhỏ nhất, partition không chia đôi — depth đệ quy là O(n) thay vì O(log n)  
C. Vì Quick Sort dùng 2 recursive calls giống Merge Sort  
D. Vì Quick Sort không có base case

---

### Câu 20 [Nâng cao]
Để chuyển đệ quy DFS trên tree sang iterative mà vẫn đúng thứ tự, ta dùng:

A. Queue  
B. Explicit Stack  
C. Heap  
D. HashMap

---

### Câu 21 [Nâng cao]
Xét recurrence `T(n) = 2T(n-1) + 1` (Tower of Hanoi). Khai triển: T(n) = 2T(n-1)+1 = 2(2T(n-2)+1)+1 = 4T(n-2)+3 = ... Kết quả closed form là:

A. T(n) = 2^n  
B. T(n) = 2^n - 1  
C. T(n) = n × 2^(n-1)  
D. T(n) = 2^(n+1)

---

### Câu 22 [Nâng cao]
Trong Python, cách tốt nhất để tránh stack overflow khi cần đệ quy depth O(n) với n lớn là:

A. Tăng `sys.setrecursionlimit(10**9)`  
B. Dùng iterative với explicit stack hoặc chuyển sang bottom-up DP  
C. Dùng @lru_cache  
D. Dùng tail recursion

---

## Giải thích đáp án

---

**Câu 1 — Đáp án: B**  
Chỉ cần 2 thành phần: base case (điều kiện dừng) và recursive case (gọi lại với bài toán nhỏ hơn). Accumulator và return value là chi tiết implementation.  
- A sai: không có base case → infinite recursion.  
- C sai: accumulator là optional (tail recursion pattern).  
- D sai: overspecified, không phải yêu cầu bắt buộc.

---

**Câu 2 — Đáp án: C**  
Không có base case → hàm gọi mình liên tục, mỗi lần tạo stack frame mới → call stack lấp đầy → Stack Overflow / RecursionError.  
- A sai: hàm không có cơ hội trả về None.  
- B sai: không "đúng" — kết quả sai hoặc crash.  
- D sai: call stack không tự dừng, sẽ throw exception.

---

**Câu 3 — Đáp án: C**  
`f(n) = n * f(n-1)` với `f(0) = 1` là định nghĩa chính xác của factorial: `n! = n × (n-1)!` với `0! = 1`.  
- A sai: Fibonacci: `f(n) = f(n-1) + f(n-2)`.  
- B sai: Power of 2: `f(n) = 2 * f(n-1)`.  
- D sai: Sum: `f(n) = n + f(n-1)`.

---

**Câu 4 — Đáp án: B**  
fib(0)=0, fib(1)=1, fib(2)=1, fib(3)=2, fib(4)=3, fib(5)=5, fib(6)=8.  
- A sai: fib(5)=5.  
- C sai: fib(7)=13.  
- D sai: 6 không là số Fibonacci.

---

**Câu 5 — Đáp án: C**  
Python default recursion limit là 1000. Kiểm tra bằng `sys.getrecursionlimit()`. Có thể tăng với `sys.setrecursionlimit()` nhưng không khuyến khích.  
- A, B, D sai: không phải giá trị default.

---

**Câu 6 — Đáp án: B**  
`T(n) = T(n-1) + O(1)`. Khai triển: T(n) = T(n-k) + k. Khi k=n: T(n) = T(0) + n = O(n). Ví dụ: duyệt linked list, factorial.  
- A sai: O(log n) khi chia đôi input.  
- C sai: O(n log n) khi chia đôi và có O(n) combine.  
- D sai: O(2^n) khi mỗi call tạo 2 sub-calls và không chia input.

---

**Câu 7 — Đáp án: B**  
Merge Sort chia mảng thành 2 nửa `(2T(n/2))`, merge tốn `O(n)`. Master Theorem Case 2: `f(n)=O(n)=n^(log_2 2)=n^1` → `T(n)=O(n log n)`.  
- A sai: đó là Insertion Sort.  
- C sai: merge không phải O(1), là O(n).  
- D sai: Binary Search, chỉ 1 sub-call.

---

**Câu 8 — Đáp án: B**  
Tail recursion: recursive call là bước CUỐI CÙNG, không có computation sau đó. Compiler có thể tái sử dụng stack frame hiện tại (TCO — Tail Call Optimization) → O(1) stack thay vì O(n).  
- A sai: tail recursion không nhất thiết nhanh hơn nếu không có TCO.  
- C sai: vẫn cần base case.  
- D sai: không liên quan memoization.

---

**Câu 9 — Đáp án: D**  
T(n) = 2T(n-1) + 1. Khai triển: T(1) = 1, T(n) = 2^(n-1) + 2^(n-2) + ... + 1 = 2^n - 1. Với 64 đĩa: 2^64 - 1 ≈ 1.8 × 10^19 bước.  
- A sai: n² tăng chậm hơn nhiều.  
- B sai: 2n tuyến tính.  
- C sai: n log n không phải dạng đệ quy nhân đôi.

---

**Câu 10 — Đáp án: B**  
Không có memo: fib(n-1) và fib(n-2) đều trigger toàn bộ sub-tree → nhiều sub-calls trùng nhau. Với memo: mỗi `fib(i)` chỉ tính đúng 1 lần (n unique subproblems) → O(n) tổng.  
- A sai: depth vẫn là O(n), không giảm xuống log n.  
- C sai: memoization vẫn dùng đệ quy.  
- D sai: không phải tail recursion.

---

**Câu 11 — Đáp án: C**  
Stack frame chứa: return address, local variables, function parameters, và saved registers. **Global variables** được lưu ở heap/data segment, không ở stack frame — tất cả calls cùng chia sẻ global variables.  
- A, B, D sai: đây đều là thành phần của stack frame.

---

**Câu 12 — Đáp án: C**  
`f(n) = f(n-1) + f(n-1)` = `2 * f(n-1)`. Recurrence: T(n) = 2T(n-1) + O(1) → T(n) = O(2^n). Cây đệ quy là cây nhị phân hoàn chỉnh depth n → 2^(n+1) - 1 nodes.  
- A sai: f(n) gọi f(n-1) hai lần riêng biệt (không phải một lần).  
- B sai: không có log factor.  
- D sai: n² là khi có vòng lặp nested.

---

**Câu 13 — Đáp án: B**  
T(n) = T(n/2) + O(1). Master Theorem: a=1, b=2, log_2(1)=0, f(n)=O(1)=O(n^0). Case 2: f(n)=Θ(n^0) → T(n)=Θ(n^0 × log n) = O(log n). Ví dụ: binary search.  
- A sai: O(n) khi T(n)=T(n-1)+O(1).  
- C sai: O(n log n) khi T(n)=2T(n/2)+O(n).  
- D sai: O(1) chỉ khi không có recursion.

---

**Câu 14 — Đáp án: B**  
`@lru_cache` là Python decorator tự động cache (key=args, value=return). Khi hàm được gọi lại với cùng args, trả về cached result ngay. Đây là memoization tự động.  
- A sai: không liên quan tail recursion.  
- C sai: không thay đổi recursion limit.  
- D sai: vẫn là đệ quy, không chuyển sang iteration.

---

**Câu 15 — Đáp án: B**  
T(n) = 4T(n/2) + O(n). Master Theorem: a=4, b=2, log_2(4)=2, f(n)=O(n^1). Vì n^1 = O(n^(2-1)) → Case 1 → T(n) = Θ(n^(log_b a)) = Θ(n²).  
- A sai: O(n log n) là Merge Sort case.  
- C sai: O(n² log n) là Case 2, cần f(n)=Θ(n²).  
- D sai: n^1.5 không xuất hiện ở đây.

---

**Câu 16 — Đáp án: B**  
Default mutable argument `memo={}` trong Python chỉ được tạo **một lần** khi function được định nghĩa, không phải mỗi lần gọi. Tất cả calls chia sẻ cùng dict → kết quả từ lần gọi trước ảnh hưởng lần sau. Dùng `memo=None` và `if memo is None: memo = {}`.  
- A sai: có base case `n<=1`.  
- C sai: có memoization, hoạt động.  
- D sai: đây là Python gotcha nổi tiếng.

---

**Câu 17 — Đáp án: B**  
Binary search đệ quy: mỗi call chia đôi array. Depth tối đa = log₂(n) (số lần chia đôi cho đến n=1). Mỗi stack frame O(1). Tổng stack: O(log n).  
- A sai: mỗi call chỉ tạo O(1) variables.  
- C sai: result là O(1).  
- D sai: array input O(n), không phải log n.

---

**Câu 18 — Đáp án: B**  
T(n) = 3T(n/3) + O(n). Master Theorem: a=3, b=3, log_3(3)=1, f(n)=O(n^1)=Θ(n^(log_b a)). Case 2 → T(n) = Θ(n log n).  
- A sai: O(n) khi f(n) tốt hơn n^(log_b a).  
- C sai: O(n²) khi f(n)=O(n²).  
- D sai: log_3(3)=1 nên n^1=n, nhưng Case 2 cho n log n, không phải n.

---

**Câu 19 — Đáp án: B**  
Quick Sort worst case: input đã sorted + pivot là first/last element. Partition một bên 0 element, một bên n-1 elements. Depth đệ quy = n. Stack space = O(n). Time cũng O(n²) do T(n) = T(n-1) + O(n).  
- A sai: Quick Sort không có merge step.  
- C sai: Quick Sort có 2 sub-calls nhưng size không đều (worst case: 0 và n-1).  
- D sai: có base case.

---

**Câu 20 — Đáp án: B**  
DFS dùng LIFO (Last-In-First-Out) — đúng là Stack. Explicit Stack trên heap thay thế call stack, tránh stack overflow. BFS dùng Queue.  
- A sai: Queue là FIFO → dùng cho BFS.  
- C sai: Heap (priority queue) cho Dijkstra, Best-First Search.  
- D sai: HashMap không phải cấu trúc duyệt.

---

**Câu 21 — Đáp án: B**  
T(n) = 2T(n-1) + 1. Khai triển:
- T(n) = 2T(n-1) + 1
- = 2(2T(n-2)+1) + 1 = 4T(n-2) + 2 + 1
- = 8T(n-3) + 4 + 2 + 1
- = 2^k × T(n-k) + (2^k - 1)

Khi k=n-1: T(n) = 2^(n-1) × T(1) + (2^(n-1) - 1) = 2^(n-1) + 2^(n-1) - 1 = 2^n - 1.  
- A sai: 2^n bỏ mất hằng số -1 (quan trọng cho tính chính xác).  
- C sai: n × 2^(n-1) là derivative không liên quan.  
- D sai: 2^(n+1) quá lớn.

---

**Câu 22 — Đáp án: B**  
Cách tốt nhất: dùng iterative với explicit stack (Stack trên heap không bị giới hạn như call stack) hoặc bottom-up DP (không cần stack).  
- A sai: tăng sys.setrecursionlimit có thể gây memory issues, không an toàn cho production.  
- C sai: @lru_cache giảm số lần gọi nhưng không giảm max stack depth cho linear recursion.  
- D sai: Python không có TCO nên tail recursion vẫn dùng O(n) stack.

---

## Bảng Đáp Án Nhanh

| Câu | Đáp án | Mức độ |
|-----|--------|--------|
| 1 | B | Cơ bản |
| 2 | C | Cơ bản |
| 3 | C | Cơ bản |
| 4 | B | Cơ bản |
| 5 | C | Cơ bản |
| 6 | B | Cơ bản |
| 7 | B | Trung bình |
| 8 | B | Trung bình |
| 9 | D | Trung bình |
| 10 | B | Trung bình |
| 11 | C | Trung bình |
| 12 | C | Trung bình |
| 13 | B | Trung bình |
| 14 | B | Trung bình |
| 15 | B | Nâng cao |
| 16 | B | Nâng cao |
| 17 | B | Nâng cao |
| 18 | B | Nâng cao |
| 19 | B | Nâng cao |
| 20 | B | Nâng cao |
| 21 | B | Nâng cao |
| 22 | B | Nâng cao |

**Phân bổ:** Cơ bản: 6 câu (27%) · Trung bình: 8 câu (36%) · Nâng cao: 8 câu (36%)
