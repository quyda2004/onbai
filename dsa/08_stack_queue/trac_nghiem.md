# Trắc nghiệm — Stack & Queue

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Stack tuân theo nguyên tắc nào?

- A. FIFO — First In First Out
- B. LIFO — Last In First Out
- C. Random access
- D. Sorted

> **Đáp án: B**  
> **Giải thích:** Stack giống chồng đĩa: phần tử cuối cùng push vào sẽ là phần tử đầu tiên pop ra. push/pop đều tại đỉnh (top).

---

**Câu 2:** Queue tuân theo nguyên tắc?

- A. FIFO — First In First Out
- B. LIFO
- C. Random
- D. Priority

> **Đáp án: A**  
> **Giải thích:** Queue giống xếp hàng mua vé: ai đến trước được phục vụ trước. Enqueue ở đuôi (rear), dequeue ở đầu (front).

---

**Câu 3:** Time complexity của push/pop trên stack (implement bằng array động) là?

- A. O(1) amortized
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: A**  
> **Giải thích:** Push thêm vào cuối, pop xóa cuối — O(1). Khi array đầy phải resize O(n) nhưng amortized vẫn O(1) qua nhiều operation.

---

**Câu 4:** Implement queue bằng array thuần (không circular) gặp vấn đề gì?

- A. Push chậm
- B. Dequeue O(n) vì phải dịch toàn bộ phần tử về trước
- C. Không lưu được string
- D. Không có vấn đề

> **Đáp án: B**  
> **Giải thích:** Dequeue ở index 0 phải dịch n-1 phần tử về trước → O(n). Giải pháp: circular queue (front/rear wrap around), hoặc deque (Python `collections.deque`).

---

**Câu 5:** Python `list.pop(0)` có complexity là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: C**  
> **Giải thích:** Pop ở index 0 phải dịch n-1 phần tử → O(n). Dùng `collections.deque` cho FIFO với `popleft()` O(1).

---

**Câu 6:** Ứng dụng nào KHÔNG phù hợp với stack?

- A. Kiểm tra balanced parentheses
- B. Function call stack
- C. Undo operation (Ctrl+Z)
- D. Tìm đường đi ngắn nhất trong graph có trọng số

> **Đáp án: D**  
> **Giải thích:** Shortest path cần BFS (queue) hoặc Dijkstra (heap), không phải stack. A, B, C đều là use case kinh điển của stack.

---

**Câu 7:** BFS (Breadth-First Search) dùng cấu trúc dữ liệu nào?

- A. Stack
- B. Queue
- C. Heap
- D. Hash map

> **Đáp án: B**  
> **Giải thích:** BFS thăm node theo từng tầng. Queue FIFO đảm bảo node ở tầng k được xử lý trước tầng k+1. Stack (DFS) đi sâu trước, không phù hợp BFS.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Implement queue bằng 2 stack. Operation enqueue và dequeue có complexity là?

- A. Cả hai O(1) amortized
- B. Enqueue O(1), dequeue O(n)
- C. Cả hai O(n)
- D. Enqueue O(log n), dequeue O(log n)

> **Đáp án: A**  
> **Giải thích:** Stack in cho push, stack out cho pop. Enqueue: push vào in (O(1)). Dequeue: nếu out rỗng, đổ tất cả từ in sang out (O(n)), sau đó pop out. Mỗi phần tử di chuyển tối đa 2 lần → amortized O(1).

---

**Câu 9:** Monotonic Stack (stack tăng/giảm đơn điệu) thường dùng cho bài toán gì?

- A. Sort
- B. Next Greater Element, Next Smaller Element, Largest Rectangle in Histogram
- C. Binary search
- D. Graph traversal

> **Đáp án: B**  
> **Giải thích:** Monotonic stack maintain invariant ordering, hiệu quả tìm "phần tử lớn/nhỏ tiếp theo" trong O(n). Mỗi phần tử push/pop tối đa 1 lần.

---

**Câu 10:** Bài "Valid Parentheses" (kiểm tra `"({[]})"` hợp lệ) dùng stack vì?

- A. Stack đảm bảo thứ tự đóng/mở match đúng theo LIFO — ngoặc mở cuối phải đóng đầu
- B. Stack lưu được string
- C. Stack có sẵn trong Python
- D. Stack nhanh nhất

> **Đáp án: A**  
> **Giải thích:** Khi gặp `(`, `{`, `[` → push. Khi gặp `)`, `}`, `]` → pop và check match. Stack rỗng cuối cùng và mọi cặp match → hợp lệ. LIFO tự nhiên xử lý nested.

---

**Câu 11:** Bài "Largest Rectangle in Histogram" có complexity tối ưu là?

- A. O(n²)
- B. O(n log n)
- C. O(n) dùng monotonic stack
- D. O(n × max_height)

> **Đáp án: C**  
> **Giải thích:** Monotonic stack tăng dần lưu index. Khi gặp bar thấp hơn top stack, pop và tính diện tích với bar đó là chiều cao, width = i - prev_top - 1. Mỗi bar push/pop 1 lần → O(n).

---

**Câu 12:** Bài "Sliding Window Maximum" tối ưu dùng cấu trúc gì?

- A. Heap
- B. Monotonic Deque
- C. BST
- D. Hash map

> **Đáp án: B**  
> **Giải thích:** Deque (double-ended queue) giảm dần, front = max. Pop front nếu out of window, pop back nếu nhỏ hơn phần tử mới. Mỗi phần tử push/pop 1 lần → O(n). Heap O(n log k); BST O(n log k); hash không phù hợp.

---

**Câu 13:** Circular queue khác queue thường ở chỗ?

- A. Có nhiều phần tử hơn
- B. Khi rear vượt cuối array, wrap về đầu — dùng mod để tránh dịch phần tử
- C. Hỗ trợ ordering
- D. Có priority

> **Đáp án: B**  
> **Giải thích:** Circular queue dùng cố định array size N, front và rear wrap around bằng `(idx + 1) % N`. Tận dụng lại các slot đã dequeue, không cần dịch → enqueue/dequeue O(1) đúng nghĩa.

---

**Câu 14:** Phân biệt Queue và Deque (Double-ended queue)?

- A. Queue chỉ push tail/pop head; Deque hỗ trợ push/pop ở cả 2 đầu
- B. Deque là sorted queue
- C. Deque chỉ dùng cho graph
- D. Không khác biệt

> **Đáp án: A**  
> **Giải thích:** Deque tổng quát hơn queue. Python `collections.deque` cung cấp `append`, `appendleft`, `pop`, `popleft` đều O(1). Đa năng — implement được cả stack và queue.

---

**Câu 15:** Function call stack trong runtime của ngôn ngữ lập trình là?

- A. Stack lưu các activation record (local variable, return address) của function đang gọi
- B. Queue task chờ thực thi
- C. Heap lưu object
- D. Pool memory dùng chung

> **Đáp án: A**  
> **Giải thích:** Mỗi function call push 1 frame. Return → pop frame. LIFO tự nhiên: function cuối gọi sẽ return đầu. Recursion sâu → stack overflow.

---

**Câu 16:** Priority Queue khác Queue thường ở chỗ?

- A. Dequeue trả về phần tử có priority cao nhất (không phải phần tử vào trước nhất)
- B. Priority Queue chậm hơn
- C. Priority Queue chỉ lưu số
- D. Không khác

> **Đáp án: A**  
> **Giải thích:** Priority Queue thường implement bằng heap. Enqueue O(log n), dequeue (extract-min/max) O(log n). Không tuân FIFO mà tuân priority. Xem chuyên đề 11_heap.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích code, nó giải bài gì?

```python
def f(nums):
    stack = []
    result = [0] * len(nums)
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            j = stack.pop()
            result[j] = i - j
        stack.append(i)
    return result
```

- A. Sort mảng
- B. Daily Temperatures: với mỗi ngày, đếm bao nhiêu ngày phải chờ để gặp ngày ấm hơn
- C. Tìm duplicate
- D. Đếm số inversion

> **Đáp án: B**  
> **Giải thích:** Monotonic stack giảm dần lưu index. Khi gặp `x > nums[stack top]`, pop và set `result[j] = i - j` (khoảng cách đến ngày ấm hơn). Index nào còn lại trong stack → không có ngày ấm hơn → result = 0. O(n).

---

**Câu 18:** Implement stack bằng 2 queue. Operation push và pop?

- A. Cả hai O(n)
- B. Push O(1), pop O(n) — khi pop, di chuyển n-1 phần tử sang queue thứ 2, dequeue phần tử cuối
- C. Cả hai O(1)
- D. Push O(n), pop O(1)

> **Đáp án: B (hoặc cũng có variant D)**  
> **Giải thích:** Cách phổ biến: push O(1) chỉ enqueue vào q1; pop O(n) di chuyển n-1 phần tử sang q2, pop phần tử còn lại, swap q1↔q2. Variant ngược lại: push O(n) (luôn duy trì element mới ở front), pop O(1). Trade-off ngược.

---

**Câu 19:** Min Stack (stack hỗ trợ getMin() O(1)). Cách implement?

- A. Sort stack mỗi push
- B. Dùng auxiliary stack đồng bộ, mỗi entry lưu min của stack chính tại thời điểm push (hoặc lưu (val, min_so_far) trong main stack)
- C. Duyệt stack mỗi getMin → O(n)
- D. Dùng heap riêng

> **Đáp án: B**  
> **Giải thích:** Auxiliary stack: khi push x vào main, push `min(x, top of aux)` vào aux. Pop main → pop aux. getMin → top aux. Mỗi op O(1). Variant không có aux: lưu (val, min_so_far) trong main stack.

---

**Câu 20:** Trapping Rain Water bằng monotonic stack có complexity?

- A. O(1)
- B. O(n) — mỗi bar push và pop tối đa 1 lần
- C. O(n log n)
- D. O(n²)

> **Đáp án: B**  
> **Giải thích:** Monotonic stack giảm dần. Khi gặp bar cao hơn top → pop, tính water đọng giữa bar mới và bar dưới top (sau khi pop). Amortized O(n). Phương án two pointers cũng O(n) O(1) space.

---

**Câu 21:** Trong implementation `queue` bằng 2 stack với amortized O(1), tại sao "amortized" mà không phải "worst case"?

- A. Vì 1 operation có thể O(n) (lần đầu pop khi out rỗng, đổ toàn bộ từ in sang out)
- B. Vì có lỗi trong thuật toán
- C. Amortized = worst case
- D. Vì depends vào hash function

> **Đáp án: A**  
> **Giải thích:** 1 pop có thể tốn O(n) khi đổ stack. Nhưng mỗi phần tử di chuyển tối đa 2 lần (push vào in, sau đó di chuyển sang out). Tổng chi phí qua n op = O(n) → trung bình O(1)/op. Worst case 1 op vẫn O(n).

---

**Câu 22:** Evaluate Reverse Polish Notation (RPN, postfix expression) dùng stack vì?

- A. Toán hạng push vào stack; toán tử pop 2 toán hạng, tính, push kết quả → bản chất stack-based evaluation
- B. RPN luôn cần sort
- C. RPN không có toán tử
- D. Stack chỉ dùng cho infix

> **Đáp án: A**  
> **Giải thích:** RPN ("2 3 +" thay vì "2 + 3") đơn giản với stack: scan trái sang phải, gặp số push, gặp op pop 2 (chú ý thứ tự cho non-commutative như `-` và `/`), tính, push kết quả. Cuối stack có 1 phần tử = kết quả. Đây là cách máy thực thi expression (vd JVM stack-based VM).

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | A      | 13  | B      |
| 3   | A      | 14  | A      |
| 4   | B      | 15  | A      |
| 5   | C      | 16  | A      |
| 6   | D      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | A      | 19  | B      |
| 9   | B      | 20  | B      |
| 10  | A      | 21  | A      |
| 11  | C      | 22  | A      |
