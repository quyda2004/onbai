# Trắc nghiệm — Linked List

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Mỗi node trong singly linked list tối thiểu chứa:

- A. Chỉ dữ liệu
- B. Dữ liệu và 1 con trỏ tới node tiếp theo
- C. Dữ liệu, con trỏ tới node trước và sau
- D. Dữ liệu, con trỏ tới head và tail

> **Đáp án: B**  
> **Giải thích:** Singly linked list chỉ có `next`. Doubly mới có thêm `prev`. C mô tả DLL. A bỏ qua phần thiết yếu (liên kết). D không phải mô hình node chuẩn.

---

**Câu 2:** Time complexity của thao tác truy cập phần tử thứ k trong singly linked list là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: C**  
> **Giải thích:** Phải đi tuần tự từ head qua k node → O(k) trong worst case O(n). Không có random access như array.

---

**Câu 3:** Insert một node ở **head** của linked list (đã có head pointer) có complexity là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: A**  
> **Giải thích:** Chỉ cần tạo node mới, cho `new.next = head`, rồi `head = new`. Đúng 2 thao tác → O(1).

---

**Câu 4:** Ưu điểm chính của linked list so với array là?

- A. Truy cập index nhanh hơn
- B. Cache locality tốt hơn
- C. Insert/delete ở giữa hoặc đầu hiệu quả hơn khi đã có pointer
- D. Dùng ít memory hơn

> **Đáp án: C**  
> **Giải thích:** Array tốt hơn về cache, truy cập index, và thường về memory (vì LL có pointer overhead). LL thắng ở insert/delete khi đã có pointer tới node (O(1) thay vì O(n) cho array).

---

**Câu 5:** Doubly linked list khác singly linked list ở chỗ:

- A. Doubly có thêm con trỏ `prev` tới node liền trước
- B. Doubly chỉ chạy được trên hệ điều hành 64-bit
- C. Doubly không có node head
- D. Doubly luôn nhanh hơn singly

> **Đáp án: A**  
> **Giải thích:** DLL có 2 con trỏ (prev, next) cho phép đi 2 chiều. Tốn gấp đôi pointer nhưng đổi lại xóa node O(1) khi đã có pointer.

---

**Câu 6:** Trong linked list, node cuối cùng có đặc điểm gì?

- A. `next` trỏ về head
- B. `next` = null (trong singly thông thường)
- C. `next` trỏ về chính nó
- D. Không có `next`

> **Đáp án: B**  
> **Giải thích:** Singly LL thông thường: tail.next = null. Trong circular LL, tail.next = head. Mọi node đều có field `next`, chỉ là giá trị null/khác.

---

**Câu 7:** Dummy node (sentinel) trong linked list được dùng để:

- A. Tăng memory usage
- B. Đơn giản hóa code khi insert/delete ở head
- C. Lưu giá trị mặc định
- D. Đảm bảo thread-safety

> **Đáp án: B**  
> **Giải thích:** Dummy đứng trước head thật, biến mọi insert/delete (kể cả ở vị trí head) thành "insert/delete sau một node nào đó" → loại bỏ case đặc biệt cho head.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Reverse linked list iterative cần dùng bao nhiêu con trỏ phụ?

- A. 1
- B. 2
- C. 3 (prev, curr, next)
- D. n (n = độ dài list)

> **Đáp án: C**  
> **Giải thích:** Cần `prev` (node trước đã đảo), `curr` (đang xử lý), `next` (lưu next trước khi đè). Tổng 3 con trỏ, O(1) auxiliary space.

---

**Câu 9:** Floyd's cycle detection có complexity?

- A. O(1) time, O(n) space
- B. O(n) time, O(1) space
- C. O(n) time, O(n) space
- D. O(n²) time, O(1) space

> **Đáp án: B**  
> **Giải thích:** Slow đi 1, fast đi 2. Nếu có cycle độ dài L, fast bắt kịp slow trong vòng tối đa L bước sau khi cả hai vào cycle → O(n) tổng. Chỉ dùng 2 con trỏ → O(1) space.

---

**Câu 10:** Cho linked list `1 → 2 → 3 → 4 → 5 → 6`. Tìm middle bằng slow/fast khi fast đến cuối, slow chỉ vào node nào?

- A. Node 3
- B. Node 4
- C. Node 5
- D. Node 6

> **Đáp án: B**  
> **Giải thích:** Với n chẵn (6), slow dừng tại node thứ n/2 + 1 = 4 (vì fast đi 2 bước/lần, dừng khi `fast` hoặc `fast.next` = null). Đây là quy ước "right-middle". Một số bài toán cần "left-middle" thì điều chỉnh điều kiện vòng `while`.

---

**Câu 11:** Xóa node trong singly linked list khi chỉ có pointer tới node đó (không có head, không có prev). Cách tối ưu (tạm chấp nhận "lừa" về vị trí logic):

- A. Không thể xóa được
- B. Phải traverse từ head
- C. Copy giá trị từ `node.next` vào `node`, rồi xóa `node.next` (chỉ áp dụng khi node không phải tail)
- D. Đặt `node = null`

> **Đáp án: C**  
> **Giải thích:** Trick LeetCode 237: thay vì xóa node thật, copy data của node sau vào node hiện tại, rồi unlink node sau. Hạn chế: không áp dụng khi node là tail. D không xóa được vì `node = null` chỉ đổi biến local, không tác động list.

---

**Câu 12:** Merge 2 sorted linked list (m và n phần tử) tối ưu có complexity?

- A. O(m × n)
- B. O((m + n) log(m + n))
- C. O(m + n) time, O(1) space (iterative)
- D. O(min(m, n))

> **Đáp án: C**  
> **Giải thích:** Mỗi vòng pick 1 node từ 2 list, tổng (m + n) vòng. Không cấp phát node mới — chỉ thay đổi con trỏ → O(1) space cho iterative. Recursive sẽ O(m + n) stack.

---

**Câu 13:** Merge K sorted linked lists tối ưu nhất bằng:

- A. Merge từng cặp tuần tự, O(N × K)
- B. Min heap chứa head của mỗi list, O(N log K) — N = tổng phần tử
- C. Concatenate rồi sort, O(N log N)
- D. Brute force pick min mỗi lượt, O(N × K)

> **Đáp án: B**  
> **Giải thích:** Heap chứa K head, pick min O(log K), push next O(log K) → mỗi node tốn O(log K) → tổng O(N log K). Divide-and-conquer merge cặp cũng cùng complexity O(N log K). Cách C tệ hơn vì không tận dụng tính đã sort.

---

**Câu 14:** Doubly linked list cho phép xóa node đã biết với complexity:

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: A**  
> **Giải thích:** Đã có `prev` sẵn → `node.prev.next = node.next` và `node.next.prev = node.prev` → 2 thao tác. Đây là lợi thế chính của DLL.

---

**Câu 15:** Tại sao LRU Cache thường implement bằng HashMap + Doubly Linked List?

- A. Vì hash map có sẵn LRU built-in
- B. HashMap cho O(1) lookup node theo key; DLL cho O(1) move-to-front và evict tail
- C. Vì singly LL không đủ memory
- D. Vì DLL có sẵn TTL

> **Đáp án: B**  
> **Giải thích:** LRU cần: (a) get key O(1) → HashMap; (b) move node lên front khi access O(1) → DLL (cần prev để unlink); (c) evict node cuối O(1) → DLL với tail pointer. Singly LL không làm được O(1) cho move-to-front.

---

**Câu 16:** Sort linked list O(n log n) với O(log n) auxiliary space dùng thuật toán nào?

- A. Quick Sort
- B. Merge Sort (top-down)
- C. Bubble Sort
- D. Counting Sort

> **Đáp án: B**  
> **Giải thích:** Merge sort phù hợp linked list: tìm middle (slow/fast), chia 2, merge sort 2 nửa, merge — O(n log n). Recursion depth = log n → O(log n) stack. Quick sort không hiệu quả trên LL vì không có random access. Bubble O(n²). Counting cần range nhỏ.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Sau khi Floyd's algorithm phát hiện cycle (slow gặp fast), reset slow về head và cho cả hai con trỏ đi 1 bước/lần. Tại sao chúng gặp nhau tại node bắt đầu cycle?

- A. Vì cycle luôn bắt đầu tại head
- B. Tổng quát: gọi a = khoảng cách head → cycle start, b = khoảng cách cycle start → meeting, c = phần còn lại của cycle. Khi gặp, fast đi 2(a+b), slow đi a+b. Hiệu = a+b là bội của L=b+c → a = c + k×L. Reset slow về head, cả 2 đi a bước cùng nhau → gặp tại cycle start
- C. Đó chỉ là một kết quả thực nghiệm, không có chứng minh
- D. Vì slow nhanh hơn fast sau reset

> **Đáp án: B**  
> **Giải thích:** Đây là chứng minh chuẩn của Floyd's algorithm. Key: hiệu khoảng cách 2 con trỏ tại meeting là bội của L, từ đó suy ra a ≡ c (mod L).

---

**Câu 18:** Phân tích đoạn code sau, nó làm gì?

```python
def f(head):
    if not head or not head.next:
        return head
    new_head = f(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

- A. Đếm node
- B. Reverse linked list (recursive)
- C. Tìm middle node
- D. Detect cycle

> **Đáp án: B**  
> **Giải thích:** Đệ quy reverse: đệ quy đến cuối, lấy new_head. Sau đó node ngay sau head sẽ quay lại trỏ về head (`head.next.next = head`), head trở thành cuối (`head.next = None`). Recursion stack O(n).

---

**Câu 19:** Copy linked list with random pointer (mỗi node có thêm `random` trỏ ngẫu nhiên). Phương án O(1) extra space sử dụng kỹ thuật nào?

- A. Hash map old → new (O(n) space)
- B. Interleaving: chèn copy của mỗi node ngay sau node gốc, sau đó set random từ next, cuối cùng tách 2 list
- C. Recursion với memo
- D. Không thể đạt O(1) space

> **Đáp án: B**  
> **Giải thích:** Bước 1: với mỗi node X, tạo X' chèn ngay sau X → list interleaved. Bước 2: X'.random = X.random.next (vì copy của Y nằm ngay sau Y). Bước 3: tách thành 2 list. Không cần hashmap → O(1) extra space (không tính output).

---

**Câu 20:** Palindrome linked list với O(n) time, O(1) space dùng cách nào?

- A. Đẩy hết vào stack rồi so sánh
- B. Convert sang array, kiểm tra two pointers
- C. Tìm middle, reverse nửa sau in-place, so sánh nửa đầu với nửa sau (đã reverse), cuối cùng phục hồi list nếu cần
- D. Hash từng cặp đối xứng

> **Đáp án: C**  
> **Giải thích:** A và B cần O(n) extra space. C: slow/fast tìm middle O(n), reverse nửa sau O(n), so sánh O(n) → tổng O(n) time, O(1) space. Lưu ý cần xử lý số node lẻ/chẵn cẩn thận.

---

**Câu 21:** Remove Nth from end (one pass). Vì sao đặt `fast` đi trước `slow` đúng N+1 bước (với dummy node)?

- A. Vì N+1 bằng chỉ số bắt đầu
- B. Để khi fast đến null, slow ở node ngay TRƯỚC node cần xóa → có thể thực hiện `slow.next = slow.next.next`
- C. Đó là quy ước, có thể dùng N
- D. Để xử lý edge case list rỗng

> **Đáp án: B**  
> **Giải thích:** Sau khi fast đi trước N+1, khoảng cách fast↔slow là N+1. Khi fast đến null, slow cách cuối list N+1 → tức là TRƯỚC node thứ N từ cuối. Có dummy nên kể cả N = độ dài list vẫn xử lý được node đầu.

---

**Câu 22:** Trong bài "Reorder List" (L0 → L1 → ... → Ln → đổi thành L0 → Ln → L1 → Ln-1 → ...), kỹ thuật nào tối ưu O(n) time, O(1) space?

- A. Đẩy vào array rồi build lại
- B. Tìm middle → reverse nửa sau → merge 2 nửa xen kẽ
- C. Recursion swap từng cặp
- D. Sort lại

> **Đáp án: B**  
> **Giải thích:** Đây là pattern cổ điển kết hợp 3 kỹ thuật cơ bản: slow/fast (find middle), reverse, merge. Tất cả đều O(n) time và O(1) space. A đúng nhưng O(n) space.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | C      |
| 2   | C      | 13  | B      |
| 3   | A      | 14  | A      |
| 4   | C      | 15  | B      |
| 5   | A      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | C      | 19  | B      |
| 9   | B      | 20  | C      |
| 10  | B      | 21  | B      |
| 11  | C      | 22  | B      |
