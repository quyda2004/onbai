# Trắc nghiệm — Big O Notation

> **Tổng số câu:** 22
> **Mức độ:** Cơ bản (27%) · Trung bình (41%) · Nâng cao (32%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích tại sao.

---

## Phần 1 — Cơ bản (câu 1–6)

---

**Câu 1.** Big O Notation dùng để đo lường điều gì?

A. Thời gian chạy tính bằng giây của một chương trình  
B. Số lượng dòng code trong chương trình  
C. Tốc độ tăng của thời gian chạy (hoặc bộ nhớ) theo kích thước input  
D. Lượng bộ nhớ RAM tối đa mà chương trình sử dụng  

**Đáp án: C**

> **Giải thích:** Big O mô tả *xu hướng tăng* của tài nguyên (time/space) khi n → ∞, không đo giá trị tuyệt đối. Đây là lý do ta bỏ hằng số: O(5n) = O(n) vì cả hai đều tăng tuyến tính.
> - A sai: Big O không tính giây — phụ thuộc vào phần cứng
> - B sai: số dòng code không liên quan đến Big O
> - D sai: đó là peak memory usage, không phải Big O

---

**Câu 2.** Thuật toán nào sau đây có độ phức tạp O(1)?

A. Tìm phần tử lớn nhất trong mảng chưa sort  
B. Kiểm tra phần tử đầu tiên của mảng có bằng 0 không  
C. Tìm kiếm nhị phân trong mảng đã sort  
D. Đếm số phần tử trong mảng bằng cách duyệt qua từng phần tử  

**Đáp án: B**

> **Giải thích:** Kiểm tra `arr[0] == 0` chỉ cần 1 phép truy cập bộ nhớ, bất kể mảng có bao nhiêu phần tử.
> - A sai: phải xem qua tất cả n phần tử → O(n)
> - C sai: binary search là O(log n)
> - D sai: đếm bằng cách duyệt là O(n). Python `len()` là O(1) vì lưu sẵn, nhưng "đếm bằng cách duyệt" là O(n)

---

**Câu 3.** Sắp xếp các complexity sau theo thứ tự từ nhanh nhất đến chậm nhất:
O(n!), O(2ⁿ), O(n log n), O(n²), O(log n), O(1), O(n)

A. O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)  
B. O(1) < O(n) < O(log n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)  
C. O(log n) < O(1) < O(n) < O(n log n) < O(2ⁿ) < O(n²) < O(n!)  
D. O(1) < O(log n) < O(n) < O(n²) < O(n log n) < O(2ⁿ) < O(n!)  

**Đáp án: A**

> **Giải thích:** Thứ tự chuẩn: O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!). Đây là thứ tự bắt buộc phải thuộc lòng.
> - B sai: O(log n) < O(n), không phải ngược lại
> - C sai: O(1) < O(log n), và n² < 2ⁿ với n đủ lớn
> - D sai: O(n log n) < O(n²)

---

**Câu 4.** Tại sao ta viết O(n) thay vì O(3n + 5)?

A. Vì 3 và 5 là số lẻ không quan trọng  
B. Vì khi n đủ lớn, hằng số và lower-order terms không ảnh hưởng đáng kể đến xu hướng tăng  
C. Vì quy ước viết của các nhà toán học, không có lý do kỹ thuật  
D. Vì chúng ta không biết giá trị chính xác của hằng số  

**Đáp án: B**

> **Giải thích:** Khi n = 10⁶: `3n + 5 = 3,000,005` và `n = 1,000,000`. Tỉ lệ là ~3 — một hằng số không thay đổi dù n có lớn bao nhiêu. Big O quan tâm đến *growth rate*, không phải giá trị tuyệt đối.
> - A sai: lý do không phải vì số lẻ hay chẵn
> - C sai: có lý do toán học rõ ràng (formal definition với hằng số c)
> - D sai: ta biết hằng số nhưng cố tình bỏ vì nó phụ thuộc vào phần cứng

---

**Câu 5.** Đoạn code sau có độ phức tạp thời gian là bao nhiêu?

```python
for i in range(n):
    print(i)
```

A. O(1)  
B. O(log n)  
C. O(n)  
D. O(n²)  

**Đáp án: C**

> **Giải thích:** Vòng lặp chạy đúng n lần, mỗi lần O(1) → tổng O(n).
> - A sai: nếu là O(1), thời gian không đổi khi n tăng — nhưng n=1000 sẽ in 1000 dòng
> - B sai: O(log n) sẽ cần chia đôi hoặc nhân đôi bước nhảy
> - D sai: O(n²) cần 2 vòng lặp lồng nhau

---

**Câu 6.** Space complexity "auxiliary" khác với "total space" như thế nào?

A. Auxiliary là bộ nhớ của input, total là bộ nhớ thêm vào  
B. Auxiliary là bộ nhớ thêm ngoài input, total là auxiliary + input  
C. Auxiliary và total là như nhau  
D. Auxiliary dùng cho stack, total dùng cho heap  

**Đáp án: B**

> **Giải thích:** Auxiliary space = extra memory beyond the input. Ví dụ merge sort: input O(n), auxiliary O(n) (mảng tạm), total = O(n) + O(n) = O(n). In-place sort có auxiliary O(1) nhưng total O(n) vì còn input.
> - A sai: ngược lại
> - C sai: chúng khác nhau khi input lớn
> - D sai: phân chia không theo stack/heap

---

## Phần 2: Trung Bình (Câu 7–14)

---

**Câu 7.** Độ phức tạp của đoạn code sau là bao nhiêu?

```python
i = 1
while i < n:
    i *= 2
```

A. O(n)  
B. O(n²)  
C. O(log n)  
D. O(n/2)  

**Đáp án: C**

> **Giải thích:** i tăng theo: 1, 2, 4, 8, ..., 2^k cho đến 2^k ≥ n → k = log₂(n) bước. Đây là pattern điển hình O(log n): biến thay đổi theo **nhân/chia**, không phải **cộng/trừ**.
> - A sai: O(n) khi i tăng theo i += 1
> - B sai: O(n²) cần 2 vòng lặp lồng nhau
> - D sai: O(n/2) = O(n), không phải O(log n)

---

**Câu 8.** Đoạn code sau có độ phức tạp thời gian là bao nhiêu?

```python
for i in range(n):
    for j in range(n):
        print(i, j)
```

A. O(n)  
B. O(2n)  
C. O(n²)  
D. O(n³)  

**Đáp án: C**

> **Giải thích:** Vòng ngoài chạy n lần, vòng trong chạy n lần cho mỗi lần ngoài → n × n = n² lần print. Đây là pattern 2 vòng lặp lồng nhau điển hình.
> - A sai: O(n) chỉ có 1 vòng lặp đơn
> - B sai: O(2n) = O(n) (2 vòng tuần tự, không lồng nhau)
> - D sai: O(n³) cần 3 vòng lồng nhau

---

**Câu 9.** Sự khác biệt giữa Big-O (O) và Theta (Θ) là gì?

A. O là upper bound, Θ là tight bound (vừa là upper vừa là lower bound)  
B. O là cho time complexity, Θ là cho space complexity  
C. O dùng cho worst case, Θ dùng cho average case  
D. O và Θ có nghĩa giống nhau, chỉ khác ký hiệu  

**Đáp án: A**

> **Giải thích:** O(g(n)) chỉ đảm bảo f(n) ≤ c·g(n) (không tệ hơn). Θ(g(n)) đảm bảo c₁·g(n) ≤ f(n) ≤ c₂·g(n) (tăng đúng như g(n)). Ví dụ: linear search là O(n) và Ω(1) nhưng không phải Θ(n) vì best case là O(1).
> - B sai: cả hai đều dùng cho time và space
> - C sai: Θ không tương đương average case
> - D sai: chúng có nghĩa khác nhau rõ ràng

---

**Câu 10.** Đây là đoạn code gì, và complexity là bao nhiêu?

```python
for i in range(n):
    for j in range(i, n):
        print(i, j)
```

A. O(n) vì vòng trong ngắn dần  
B. O(n log n) vì có pattern chia đôi  
C. O(n²) vì tổng = n + (n-1) + ... + 1 = n(n+1)/2  
D. O(n/2) vì vòng trong trung bình chạy n/2 lần  

**Đáp án: C**

> **Giải thích:** Tổng số iterations = n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 = O(n²). Dù vòng trong "ngắn dần", tổng cộng vẫn là bậc 2. O(n/2 × n) = O(n²/2) = O(n²).
> - A sai: O(n) sẽ là tổng thẳng không phải triangular number
> - B sai: không có pattern nhân đôi
> - D sai: O(n/2) = O(n), không phản ánh đúng (vì còn nhân với n vòng ngoài)

---

**Câu 11.** Merge sort có Master Theorem: T(n) = 2T(n/2) + O(n). Kết quả là gì?

A. O(n)  
B. O(n log n)  
C. O(n²)  
D. O(log n)  

**Đáp án: B**

> **Giải thích:** Áp dụng Master Theorem: a=2, b=2, log_b(a) = log₂(2) = 1. f(n) = O(n) = O(n¹). Vì f(n) = Θ(n^log_b(a)) = Θ(n¹), đây là Case 2 → T(n) = Θ(n log n).
> - A sai: O(n) nếu không có bước merge O(n)
> - C sai: O(n²) là bubble sort
> - D sai: O(log n) là binary search, chỉ có 1 subproblem

---

**Câu 12.** Python `list.append()` có amortized complexity là O(1). Điều này có nghĩa là gì?

A. Mỗi lần append đều chính xác tốn O(1)  
B. Đôi khi append tốn O(n) (khi resize), nhưng trung bình trên n operations là O(1)  
C. Append chỉ tốn O(1) với list có ít hơn 1000 phần tử  
D. O(1) amortized và O(1) worst case là như nhau  

**Đáp án: B**

> **Giải thích:** Khi list đầy, Python tạo mảng mới gấp đôi và copy toàn bộ — O(n). Nhưng điều này xảy ra ngày càng ít hơn. Tổng cost cho n appends = O(n) → amortized O(1) mỗi append.
> - A sai: đôi khi tốn O(n) khi resize
> - C sai: resize xảy ra bất kể size, không có ngưỡng 1000
> - D sai: worst case vẫn là O(n), amortized mới là O(1)

---

**Câu 13.** Tính complexity của hàm sau:

```python
def find_pairs(arr):
    n = len(arr)
    count = 0
    for i in range(n):
        for j in range(n):
            if arr[i] + arr[j] == 0:
                count += 1
    return count
```

A. O(n) — chỉ đếm, không sort  
B. O(n log n) — vì có nested loop  
C. O(n²) — hai vòng lặp lồng nhau  
D. O(2n) — hai biến i và j  

**Đáp án: C**

> **Giải thích:** Vòng ngoài n lần × vòng trong n lần = n² iterations. Mỗi iteration O(1) → tổng O(n²). Đây là pattern two-sum brute force điển hình.
> - A sai: nested loop không thể O(n)
> - B sai: O(n log n) cần pattern chia đôi
> - D sai: O(2n) = O(n), không phải O(n²)

---

**Câu 14.** Cho đoạn code sau, complexity là bao nhiêu?

```python
def process(n):
    for i in range(n):       # Loop 1
        print(i)
    for j in range(n):       # Loop 2
        for k in range(n):   # Loop 3 (nested in 2)
            print(j, k)
```

A. O(n³) — 3 vòng lặp  
B. O(n²) — loop 1 là O(n), loop 2+3 là O(n²), tổng O(n + n²) = O(n²)  
C. O(2n²) — cộng 2 phần  
D. O(n² + n) — không được đơn giản hóa  

**Đáp án: B**

> **Giải thích:** Khi cộng các phần: O(n) + O(n²) = O(n²) vì O(n²) dominate O(n) khi n lớn. Rule: giữ dominant term. O(2n²) = O(n²) vì bỏ hằng số. D là cách viết trung gian — kỹ thuật là đúng nhưng convention là simplify.
> - A sai: 3 vòng lặp không lồng hết — loop 1 tách biệt với loop 2 và 3
> - C sai: O(2n²) = O(n²) sau khi bỏ hằng số
> - D sai về convention — ta luôn simplify về dominant term

---

## Phần 3: Nâng Cao (Câu 15–20)

---

**Câu 15.** Hàm đệ quy sau có time complexity và space complexity là bao nhiêu?

```python
def sum_array(arr, n):
    if n == 0:
        return 0
    return arr[n-1] + sum_array(arr, n-1)
```

A. Time: O(n), Space: O(1)  
B. Time: O(n), Space: O(n) — do call stack  
C. Time: O(n²), Space: O(n)  
D. Time: O(log n), Space: O(log n)  

**Đáp án: B**

> **Giải thích:** Hàm gọi đệ quy n lần → O(n) time. Nhưng mỗi lần gọi tạo một stack frame mới, và tất cả n frame cùng tồn tại trên call stack cho đến khi đến base case → O(n) space. Đây là lý do luôn phải tính call stack với đệ quy.
> - A sai: space không phải O(1) — stack frame tích lũy
> - C sai: không có nested loop hay nested recursion
> - D sai: không có pattern chia đôi

---

**Câu 16.** Đây là code gì và tại sao?

```python
def mystery(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = mystery(arr[:mid])
    right = mystery(arr[mid:])
    # merge takes O(n)
    return merge(left, right)
```

A. Binary search — O(log n)  
B. Merge sort — O(n log n)  
C. Quick sort — O(n²) worst case  
D. Heap sort — O(n log n)  

**Đáp án: B**

> **Giải thích:** Pattern điển hình của merge sort: chia đôi (log n levels) + merge O(n) mỗi level = O(n log n). Đặc điểm nhận dạng: chia đôi + merge với O(n) combine step.
> - A sai: binary search không merge, chỉ tìm kiếm
> - C sai: quicksort partition không merge, là in-place
> - D sai: heapsort dùng heap, không divide-and-conquer kiểu này

---

**Câu 17.** Với HashMap, khi nào worst case O(n) xảy ra và có thể phòng tránh như thế nào?

A. Khi HashMap có hơn 1000 phần tử — không phòng tránh được  
B. Khi tất cả keys hash về cùng một bucket (hash collision) — phòng tránh bằng hash function tốt và hash randomization  
C. Khi HashMap được dùng trong multi-threaded environment — dùng ConcurrentHashMap  
D. Khi keys là float thay vì integer — dùng keys là string  

**Đáp án: B**

> **Giải thích:** Worst case O(n) xảy ra khi tất cả n keys hash về cùng bucket → phải duyệt linked list dài n. Đây là cơ sở của hash flooding attack. Python 3.3+ thêm hash randomization (seed ngẫu nhiên mỗi lần chạy) để phòng ngừa. Java 8+ chuyển bucket từ LinkedList sang Red-Black Tree khi dài > 8 → O(log n) thay vì O(n).
> - A sai: size không phải nguyên nhân, load factor mới quan trọng
> - C sai: multi-threading là vấn đề concurrency, không phải hash collision
> - D sai: type của key không trực tiếp gây collision

---

**Câu 18.** Phân tích đoạn code sau. Complexity thực sự là bao nhiêu?

```python
def count_bits(n):
    count = 0
    while n:
        count += n & 1
        n >>= 1
    return count
```

A. O(n) — vòng lặp chạy n lần  
B. O(log n) — số bit của n là log₂(n)  
C. O(1) — chỉ làm phép toán bitwise  
D. O(n²) — hai thao tác mỗi iteration  

**Đáp án: B**

> **Giải thích:** Số bit của số nguyên n là ⌊log₂(n)⌋ + 1. Vòng lặp chạy đúng bằng số bit → O(log n). Đây là pattern quan trọng: khi biến dịch phải (`>>=1`), số iterations là log₂(n). Tương tự khi nhân đôi là O(log n) theo chiều ngược.
> - A sai: không chạy n lần — n=8 chỉ cần 4 bước (1000 → 100 → 10 → 1 → 0)
> - C sai: O(1) chỉ khi số iterations không phụ thuộc n
> - D sai: số operations mỗi iteration không ảnh hưởng đến complexity (chỉ thêm hằng số)

---

**Câu 19.** Trong ngữ cảnh phỏng vấn, khi phỏng vấn viên hỏi "optimize your solution", họ thường muốn gì?

A. Viết code ngắn hơn (ít dòng hơn)  
B. Giảm time complexity, thường từ O(n²) xuống O(n) hoặc O(n log n), đôi khi đánh đổi space  
C. Chỉ giảm space complexity  
D. Dùng built-in functions thay vì tự implement  

**Đáp án: B**

> **Giải thích:** "Optimize" trong phỏng vấn hầu như luôn có nghĩa là giảm time complexity. Pattern phổ biến: O(n²) → O(n) bằng hash map; O(n) → O(log n) bằng binary search trên sorted data. Space là secondary — thường chấp nhận đánh đổi O(n) space để có O(n) time.
> - A sai: code ngắn không phải mục tiêu chính
> - C sai: space ít khi là mục tiêu chính
> - D sai: built-in không đương nhiên tốt hơn

---

**Câu 20.** Đoạn code này có vẻ là O(n) nhưng thực ra là O(n²) trong Python. Tại sao?

```python
def build_string(words):
    result = ""
    for word in words:      # n words
        result += word      # This line!
    return result
```

A. Vì Python strings là objects, tốn overhead  
B. Vì Python strings là immutable — mỗi `+=` tạo string mới và copy toàn bộ nội dung cũ → O(1 + 2 + 3 + ... + n) = O(n²) total  
C. Vì vòng lặp `for` trong Python chậm  
D. Vì `words` có thể là generator, không phải list  

**Đáp án: B**

> **Giải thích:** Python strings là immutable. `result += word` tương đương với `result = result + word` — tạo string mới, copy toàn bộ `result` (dài i ký tự) + `word`. Tổng bytes copied = 0 + L + 2L + 3L + ... + (n-1)L ≈ O(n²·L) = O(n²) khi coi mỗi word dài L. Fix: dùng `"".join(words)` — O(n).
> - A sai: overhead là constant, không tạo ra O(n²)
> - C sai: for loop overhead là O(1) per iteration
> - D sai: generator hay list không ảnh hưởng đến string concatenation behavior

---

**Câu 21.** Tính complexity của Fibonacci đệ quy có memoization:

```python
memo = {}
def fib(n):
    if n in memo: return memo[n]
    if n <= 1: return n
    memo[n] = fib(n-1) + fib(n-2)
    return memo[n]
```

A. O(2ⁿ) — vẫn có 2 recursive calls  
B. O(n) — mỗi subproblem tính đúng 1 lần  
C. O(n²) — n subproblems × O(n) mỗi cái  
D. O(log n) — giống binary search  

**Đáp án: B**

> **Giải thích:** Với memoization, mỗi `fib(k)` với k từ 0 đến n chỉ được tính đúng 1 lần. Có n+1 unique subproblems, mỗi cái O(1) → O(n) total. Space: O(n) cho memo dict + O(n) call stack.
> - A sai: lần thứ 2 gặp fib(k) sẽ return từ memo ngay, không đệ quy tiếp
> - C sai: không có nested computation
> - D sai: O(log n) cần chia đôi problem, Fibonacci chia n-1 và n-2

---

**Câu 22.** Khi nào nên dùng O(n log n) sort thay vì O(n²) sort?

A. Luôn luôn — O(n log n) luôn tốt hơn  
B. Khi n lớn (thường n > 20-50); với n nhỏ, O(n²) insertion sort có thể nhanh hơn do cache locality và ít overhead  
C. Khi dữ liệu là số nguyên  
D. Khi dữ liệu đã gần sorted  

**Đáp án: B**

> **Giải thích:** Với n nhỏ (< ~20), insertion sort (O(n²)) thường NHANH hơn merge sort (O(n log n)) vì: ít overhead, cache-friendly, đơn giản. Timsort (Python's sort) dùng insertion sort cho subarrays nhỏ. Với n lớn, O(n log n) vượt trội rõ ràng.
> - A sai: không luôn luôn — n nhỏ là ngoại lệ quan trọng
> - C sai: type không quyết định — radix sort O(n) cho integer, nhưng không phải insertion sort
> - D sai: khi gần sorted, insertion sort thậm chí O(n) — nhưng câu hỏi về trường hợp tổng quát

---

## Bảng Đáp Án Nhanh

| Câu | Đáp án | Chủ đề |
|-----|--------|--------|
| 1 | C | Định nghĩa Big O |
| 2 | B | O(1) operations |
| 3 | A | Thứ tự complexity |
| 4 | B | Tại sao bỏ hằng số |
| 5 | C | O(n) loop |
| 6 | B | Space complexity |
| 7 | C | O(log n) pattern |
| 8 | C | O(n²) nested loop |
| 9 | A | O vs Θ |
| 10 | C | Triangular sum |
| 11 | B | Master Theorem |
| 12 | B | Amortized analysis |
| 13 | C | Two-sum brute force |
| 14 | B | Sequential vs nested |
| 15 | B | Recursion + call stack |
| 16 | B | Merge sort pattern |
| 17 | B | HashMap worst case |
| 18 | B | Bit manipulation |
| 19 | B | Optimize in interview |
| 20 | B | String immutability |
| 21 | B | Memoization |
| 22 | B | Practical sort choice |
