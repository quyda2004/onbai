# Linked List — Danh Sách Liên Kết

## 1. Giải thích cho người mới hoàn toàn

Tưởng tượng bạn đang chơi trò "săn kho báu". Mỗi tấm phiếu chỉ ghi 2 thứ:
- Một **mảnh thông tin** (con số, tên, vật phẩm...).
- **Địa chỉ** của tấm phiếu tiếp theo.

Bạn được phát tấm phiếu đầu tiên. Đọc xong nội dung, bạn nhìn địa chỉ ghi trên đó để đi đến tấm phiếu thứ hai. Cứ thế, bạn lần lượt đi qua các tấm phiếu — đến khi tấm phiếu cuối cùng ghi "hết, không còn nữa" (null).

Đó chính là **linked list**: một chuỗi các "node" (tấm phiếu), mỗi node chứa giá trị và một con trỏ tới node tiếp theo. Bạn **không thể nhảy thẳng đến tấm thứ 100** — phải đi tuần tự từ đầu.

### So sánh với mảng

- **Mảng (Array):** Giống dãy phòng khách sạn có số phòng liên tiếp. Muốn vào phòng 100 → đi thẳng tới, mất 1 bước.
- **Linked List:** Giống đường truy tìm kho báu. Muốn đến manh mối 100 → phải lần lượt qua 1, 2, 3, ..., 99 → mất 100 bước.

### Vì sao đôi khi linked list lại tốt hơn mảng?

Tưởng tượng bạn có một dãy phòng khách sạn xếp dọc. Bây giờ có khách mới muốn vào ở phòng số 5 và muốn **đẩy tất cả khách cũ ra phía sau 1 phòng**. Bạn phải dọn từng phòng → tốn rất nhiều công.

Với linked list, bạn chỉ cần **đổi địa chỉ** trên 2 tấm phiếu: tấm phiếu thứ 4 trỏ sang tấm mới, tấm mới trỏ sang tấm cũ thứ 5. Xong. Không cần dịch chuyển ai cả.

→ Linked list **chèn/xóa ở giữa rất nhanh** (O(1) nếu đã biết vị trí), nhưng **truy cập theo index chậm** (O(n)).

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Mô hình bộ nhớ

Khác với array (cấp phát liên tục, cache-friendly, prefetch tốt), linked list cấp phát **rời rạc** — mỗi node là một block heap riêng biệt, các node có thể nằm rải khắp memory. Điều này dẫn tới:
- **Cache miss cao**: mỗi lần truy cập node mới có thể là cache line khác → linked list trong thực tế chậm hơn array rất nhiều dù complexity lý thuyết giống nhau.
- **Memory overhead**: mỗi node thêm 1 (singly) hoặc 2 (doubly) con trỏ → với 8-byte pointer, tăng ~8–16 byte mỗi giá trị.
- **GC pressure** (managed languages): nhiều object nhỏ.

### Các biến thể chính

**Singly Linked List**: mỗi node có `data` và `next`.
- Ưu: tiết kiệm memory, đơn giản.
- Nhược: không đi ngược; xóa node yêu cầu biết node trước (hoặc trick "copy data từ next").

**Doubly Linked List**: mỗi node có `data`, `next`, `prev`.
- Ưu: đi ngược được; xóa O(1) khi đã có pointer tới node cần xóa.
- Nhược: tốn gấp đôi pointer; cập nhật phức tạp hơn.
- Dùng trong: LRU Cache, std::list (C++), java.util.LinkedList.

**Circular Linked List**: tail.next = head (thay vì null).
- Dùng trong: round-robin scheduler, Josephus problem, buffer xoay vòng.

**Skip List**: linked list nhiều tầng, mỗi tầng "skip" qua nhiều node → search/insert/delete O(log n) expected. Dùng làm thay thế balanced BST trong Redis (sorted set).

### Sentinel / Dummy node

Pattern phổ biến: tạo một `dummy` node trỏ vào head thực sự. Lợi ích:
- Tránh case đặc biệt khi insert/delete tại head.
- Code thống nhất, ít branching.
- LeetCode merge two sorted lists, remove nth from end thường viết với dummy.

### Các pattern thuật toán then chốt

1. **Two pointers slow/fast**: tìm middle, detect cycle, find cycle start, palindrome.
2. **Reverse linked list**: iterative (3 con trỏ prev/curr/next) hoặc recursive.
3. **Merge K sorted lists**: dùng min-heap O(N log K).
4. **In-place modification**: reorder list, swap pairs, rotate.
5. **Cloning với random pointer**: hashmap mapping cũ → mới, hoặc trick interleaving.

### Trade-off vs Dynamic Array

| Khía cạnh | Linked List | Dynamic Array |
|-----------|-------------|---------------|
| Random access | O(n) | O(1) |
| Insert/delete đầu/giữa | O(1) nếu có pointer | O(n) (phải dịch) |
| Append cuối | O(1) (nếu có tail pointer) | O(1) amortized |
| Cache locality | Tệ | Rất tốt |
| Memory overhead/element | Cao (pointer) | Thấp |
| Phù hợp khi | Cần insert/delete giữa thường xuyên, không cần random access | Cần random access nhanh, ít insert/delete giữa |

Trong thực tế (mạnh nhờ cache), array thường thắng linked list ngay cả ở các benchmark "lý thuyết linked list thắng" với n < ~10^6. Linked list được dùng nhiều khi:
- Là cấu phần của data structure khác (LRU Cache hash + DLL, adjacency list cho graph, hash map chaining).
- Yêu cầu reference-stable (pointer tới node giữ nguyên khi insert/delete chỗ khác — array thì resize phá vỡ).

---

## 3. Định nghĩa chính xác

**Linked List**: Cấu trúc dữ liệu tuyến tính trong đó các phần tử (gọi là **node**) được lưu trữ tại các vùng nhớ rời rạc và liên kết với nhau qua **con trỏ** (reference). Mỗi node tối thiểu chứa: (1) dữ liệu, (2) con trỏ đến node tiếp theo.

**Node**: Đơn vị cơ bản của linked list, chứa giá trị và một hoặc nhiều con trỏ.

**Head**: Node đầu tiên của list. Toàn bộ list được tham chiếu qua head.

**Tail**: Node cuối cùng, có `next = null` (trong singly/doubly) hoặc trỏ về head (trong circular).

**Dummy / Sentinel node**: Node giả không chứa dữ liệu hợp lệ, đặt trước head để đơn giản hóa thao tác chèn/xóa tại head.

---

## 4. Bảng Độ phức tạp đầy đủ

### Singly Linked List

| Thao tác | Best Case | Average Case | Worst Case | Auxiliary Space | Ghi chú |
|----------|-----------|--------------|------------|-----------------|---------|
| Access (chỉ số k) | O(1) | O(n) | O(n) | O(1) | Best khi k=0 |
| Search giá trị | O(1) | O(n) | O(n) | O(1) | Best khi ở head |
| Insert ở head | O(1) | O(1) | O(1) | O(1) | |
| Insert ở tail (có tail ptr) | O(1) | O(1) | O(1) | O(1) | Không có tail ptr: O(n) |
| Insert sau node đã biết | O(1) | O(1) | O(1) | O(1) | |
| Insert tại chỉ số k | O(1) | O(n) | O(n) | O(1) | Phải traverse trước |
| Delete head | O(1) | O(1) | O(1) | O(1) | |
| Delete tail | O(n) | O(n) | O(n) | O(1) | Phải tìm prev của tail |
| Delete node đã biết (có prev) | O(1) | O(1) | O(1) | O(1) | |
| Delete by value | O(1) | O(n) | O(n) | O(1) | Phải search |
| Reverse | O(n) | O(n) | O(n) | O(1) iter / O(n) recur | |

### Doubly Linked List

| Thao tác | Best Case | Average | Worst | Auxiliary Space | Ghi chú |
|----------|-----------|---------|-------|-----------------|---------|
| Access | O(1) | O(n) | O(n) | O(1) | Có thể đi từ tail nếu k > n/2 → tối ưu O(n/2) vẫn O(n) |
| Insert ở head/tail | O(1) | O(1) | O(1) | O(1) | |
| Delete node đã biết | O(1) | O(1) | O(1) | O(1) | Không cần biết prev (đã có sẵn) |
| Reverse | O(n) | O(n) | O(n) | O(1) | Đổi next ↔ prev |

### Operations trên thuật toán quan trọng

| Bài toán | Time | Space |
|----------|------|-------|
| Reverse linked list | O(n) | O(1) |
| Detect cycle (Floyd) | O(n) | O(1) |
| Find middle (slow/fast) | O(n) | O(1) |
| Merge 2 sorted lists | O(m+n) | O(1) iter / O(m+n) recur |
| Merge K sorted lists (heap) | O(N log K) | O(K) |
| Sort linked list (merge sort) | O(n log n) | O(log n) stack |
| Palindrome check | O(n) | O(1) (in-place reverse half) |
| Copy list with random pointer | O(n) | O(1) interleaving / O(n) hashmap |
| Remove Nth from end | O(n) | O(1) |
| Reorder list | O(n) | O(1) |

---

## 5. Code mẫu

### Định nghĩa Node và List cơ bản (singly)

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def from_list(arr):
    """Helper: tạo linked list từ Python list."""
    dummy = ListNode()
    curr = dummy
    for x in arr:
        curr.next = ListNode(x)
        curr = curr.next
    return dummy.next

def to_list(head):
    """Helper: dump linked list ra list để in."""
    out = []
    while head:
        out.append(head.val)
        head = head.next
    return out
```

### Reverse linked list (iterative)

```python
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        nxt = curr.next   # lưu next trước khi đè
        curr.next = prev  # đảo chiều
        prev = curr
        curr = nxt
    return prev  # head mới

# Test
head = from_list([1, 2, 3, 4, 5])
print(to_list(reverse_list(head)))  # [5, 4, 3, 2, 1]
```

### Reverse linked list (recursive)

```python
def reverse_list_rec(head):
    if not head or not head.next:
        return head
    new_head = reverse_list_rec(head.next)
    head.next.next = head  # node sau quay lại trỏ về head
    head.next = None       # head trở thành đuôi mới
    return new_head
```

### Detect cycle (Floyd's algorithm)

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False

def cycle_start(head):
    """Trả về node bắt đầu chu trình, hoặc None nếu không có."""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            # tìm node bắt đầu
            ptr = head
            while ptr is not slow:
                ptr = ptr.next
                slow = slow.next
            return ptr
    return None
```

### Find middle node

```python
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # với 5 node trả về node 3, với 6 node trả về node 4
```

### Merge two sorted lists

```python
def merge_two(l1, l2):
    dummy = ListNode()
    tail = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            tail.next = l1
            l1 = l1.next
        else:
            tail.next = l2
            l2 = l2.next
        tail = tail.next
    tail.next = l1 or l2  # gắn phần còn lại
    return dummy.next
```

### Remove Nth node from end (one pass)

```python
def remove_nth_from_end(head, n):
    dummy = ListNode(0, head)
    slow = fast = dummy
    for _ in range(n + 1):
        fast = fast.next
    while fast:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next
```

### Doubly Linked List Node

```python
class DLLNode:
    def __init__(self, val=0):
        self.val = val
        self.prev = None
        self.next = None

def remove_node(node):
    """Xóa node trong DLL không cần biết head, O(1)."""
    if node.prev:
        node.prev.next = node.next
    if node.next:
        node.next.prev = node.prev
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Cần insert/delete tại đầu hoặc giữa thường xuyên, đặc biệt khi đã có pointer tới node đó.
- Không biết trước kích thước, cấp phát động từng phần.
- Xây dựng các cấu trúc dữ liệu phức tạp khác: queue, stack, hash map (chaining), graph (adjacency list), LRU cache (hash + DLL).
- Cần reference stability (pointer tới node luôn hợp lệ kể cả khi insert/delete chỗ khác).
- Implement undo/redo (DLL của các state).

**Không dùng khi:**
- Cần random access nhanh theo index → array/dynamic array tốt hơn.
- Yêu cầu cache locality cao, dữ liệu nhỏ → array thắng vì hardware prefetching.
- Memory hạn chế và mỗi phần tử nhỏ → pointer overhead có thể gấp đôi data.
- Cần binary search trực tiếp → linked list không hỗ trợ O(log n) search (skip list mới hỗ trợ).

---

## 7. So sánh với các cấu trúc liên quan

| Tiêu chí | Array | Singly LL | Doubly LL | Dynamic Array |
|----------|-------|-----------|-----------|---------------|
| Random access | O(1) | O(n) | O(n) | O(1) |
| Insert head | O(n) | O(1) | O(1) | O(n) |
| Insert tail | O(1) nếu còn chỗ | O(1)* | O(1) | O(1) amortized |
| Delete known node | O(n) | O(n) nếu chỉ có ptr | O(1) | O(n) |
| Memory/element | Thấp | +1 ptr | +2 ptr | Thấp |
| Cache friendly | Rất | Tệ | Tệ | Rất |

*Có tail pointer.

| | Linked List | Stack | Queue |
|-|-------------|-------|-------|
| Mục đích | Tổng quát | LIFO | FIFO |
| Bản chất | CTDL cơ sở | Có thể implement bằng LL hoặc array | Tương tự |

| | Linked List | Skip List |
|-|-------------|-----------|
| Search | O(n) | O(log n) expected |
| Memory overhead | Thấp | Cao hơn (multi-level pointer) |
| Implement | Đơn giản | Phức tạp hơn |
| Use case | General | Redis sorted set, alt cho BBST |

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Quên kiểm tra null** trước khi dereference `node.next` → null pointer exception. Pattern: `while node and node.next:`.

2. **Mất head pointer** khi reverse hoặc khi đi qua list bằng `head = head.next` → không còn cách lấy lại list. Dùng biến `curr` và giữ `head` riêng.

3. **Không lưu `next` trước khi đè** trong reverse: `curr.next = prev` rồi `curr = curr.next` → đi sai vì `curr.next` đã bị đổi. Phải lưu `nxt = curr.next` trước.

4. **Memory leak (trong C/C++)**: xóa node không free → leak. Trong Python/Java có GC tự xử lý nhưng vẫn cần ngắt reference để GC làm việc.

5. **Off-by-one trong "Remove Nth from end"**: phải dùng dummy + đi fast trước n+1 bước (không phải n) để slow dừng ở node trước cần xóa.

6. **Detect cycle viết sai**: kiểm tra `slow == fast` ở đầu vòng (trước khi tiến) → khi cả hai bằng nhau ngay từ head → false positive. Phải check sau khi tiến.

7. **Sửa DLL không cập nhật cả 2 chiều** → bị "leak" mất 1 chiều liên kết.

8. **Recursive reverse với linked list dài**: Python recursion limit (~1000) → stack overflow với list lớn → dùng iterative.

9. **Compare bằng `==` vs `is`**: so sánh value vs identity. Với node, thường dùng `is` để check cùng object (Floyd's algorithm).

10. **Insert/delete in-place trong khi đang iterate** mà không cập nhật con trỏ traverse → vòng lặp vô hạn hoặc skip node.

---

## 9. Câu hỏi phỏng vấn hay gặp

- Reverse linked list iterative và recursive — viết code, so sánh space complexity.
- Detect cycle: Floyd vs hash set — so sánh time/space.
- Tìm node bắt đầu cycle — chứng minh tại sao reset 1 con trỏ về head, đi cùng tốc độ thì gặp nhau ở start cycle.
- Tìm middle node với 1 pass.
- Merge K sorted linked lists — phương án dùng heap, phương án divide-and-conquer merge từng cặp.
- Copy list with random pointer — phương án hashmap O(n) space, phương án interleaving O(1).
- LRU Cache — implement với hash map + doubly linked list (O(1) cho cả get và put).
- Sort linked list O(n log n) — viết merge sort cho linked list, tại sao không dùng quick sort.
- Palindrome linked list — phương án O(n) time O(1) space (reverse nửa sau, so sánh).
- Khi nào dùng DLL thay vì SLL?
- Linked list vs array: phân tích khi nào thực sự nên dùng linked list trong production.
- Implement queue bằng 2 stack, stack bằng linked list, v.v.
