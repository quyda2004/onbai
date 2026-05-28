# Stack, Queue, Deque & Monotonic Stack

---

## 1. Giải thích cho người mới hoàn toàn

### Stack — Chồng đĩa
Hãy tưởng tượng một chồng đĩa ăn cơm. Bạn chỉ có thể:
- **Đặt đĩa lên trên** (push)
- **Lấy đĩa từ trên xuống** (pop)

Cái nào vào sau thì ra trước — LIFO (Last In, First Out).

**Ví dụ đời thực:**
- Nút **Undo/Redo** trong Word: thao tác mới nhất được undo trước
- **Back button** trên trình duyệt: trang vừa xem được quay lại trước
- **Xếp bát đĩa trong bếp**: lấy từ trên xuống

### Queue — Hàng chờ
Hàng người đứng chờ ở quầy vé. Người đến trước được phục vụ trước — FIFO (First In, First Out).

**Ví dụ đời thực:**
- **Hàng chờ tại quầy ngân hàng**
- **Máy in**: file gửi trước được in trước
- **Call center**: cuộc gọi đến trước được trả lời trước

### Deque — Queue hai đầu
Deque (Double-Ended Queue) là hàng chờ có thể thêm/xóa từ cả hai đầu. Như một con tàu hỏa có cửa ở cả đầu và cuối.

### Monotonic Stack — Stack có thứ tự
Một Stack đặc biệt: các phần tử luôn được giữ theo thứ tự tăng dần (hoặc giảm dần). Khi phần tử mới vào phá vỡ thứ tự, ta loại phần tử cũ ra.

**Ví dụ đời thực:** Xếp hàng theo chiều cao — khi người cao hơn vào, những người thấp hơn đứng sau họ không còn "nhìn thấy được từ xa" nữa → bị pop ra.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Stack — Implementation Analysis

**Array-based Stack:**
- Push: `arr[top++] = val` — O(1) amortized (có thể resize)
- Pop: `return arr[--top]` — O(1)
- Cache-friendly: contiguous memory
- Fixed capacity hoặc dynamic array (amortized O(1) với doubling)

**Linked-list Stack:**
- Push: thêm node vào đầu — O(1) guaranteed
- Pop: xóa node đầu — O(1) guaranteed
- Cache-unfriendly: non-contiguous memory
- Overhead: pointer per node (8 bytes trên 64-bit)

**Call Stack trong máy tính:**
- Mỗi function call tạo một stack frame: local vars, params, return address
- Stack pointer (SP register) trỏ đến đỉnh
- Stack grows downward (địa chỉ giảm dần)
- Stack size thường 1-8 MB per thread

### Queue — Circular Array Implementation

Naive array queue: dequeue bằng cách shift tất cả phần tử → O(n). Không hiệu quả.

**Circular Array (Ring Buffer):**
```
[_, _, _, _, _]  capacity = 5
 ^head   ^tail

Enqueue: arr[tail] = val; tail = (tail + 1) % capacity
Dequeue: val = arr[head]; head = (head + 1) % capacity
```

- O(1) enqueue và dequeue guaranteed
- Full: `(tail + 1) % capacity == head`
- Empty: `head == tail`
- Python `collections.deque` uses doubly-linked list of fixed-size blocks (không phải circular array thuần, nhưng O(1) both ends)

### Monotonic Stack — Amortized Analysis

Pattern: "Next Greater Element" — tìm phần tử lớn hơn đầu tiên ở bên phải.

```
Array: [2, 1, 5, 3, 4]
       
Process 2: stack = [2]
Process 1: 1 < 2, stack = [2, 1]
Process 5: 5 > 1 → pop 1 (answer for 1 = 5)
           5 > 2 → pop 2 (answer for 2 = 5)
           stack = [5]
Process 3: 3 < 5, stack = [5, 3]
Process 4: 4 > 3 → pop 3 (answer for 3 = 4)
           4 < 5, stack = [5, 4]
```

**Amortized O(n):** Mỗi phần tử được push đúng 1 lần và pop đúng 1 lần → tổng push + pop = 2n → O(n) amortized dù có vòng lặp while bên trong.

### Monotonic Deque — Sliding Window Maximum

Duy trì deque chứa indices của các phần tử có thể là maximum trong window.

Invariant: deque front luôn là maximum của window hiện tại. Các phần tử trong deque theo thứ tự giảm dần (monotonic decreasing).

---

## 3. Định nghĩa chính xác

**Stack:** Abstract data type LIFO — Last In, First Out. Operations: push(x), pop(), peek()/top(), isEmpty(), size().

**Queue:** Abstract data type FIFO — First In, First Out. Operations: enqueue(x), dequeue(), front(), isEmpty(), size().

**Deque (Double-Ended Queue):** ADT cho phép push/pop ở cả hai đầu. Operations: appendleft(x), append(x), popleft(), pop(), peekleft(), peekright().

**Monotonic Stack:** Stack với invariant: phần tử từ bottom đến top luôn đơn điệu (tăng hoặc giảm). Khi push phần tử mới vi phạm invariant, pop các phần tử vi phạm trước.

**Monotonic Deque:** Deque với invariant đơn điệu, kết hợp window size control (loại phần tử out-of-window từ front).

---

## 4. Bảng Độ Phức Tạp Đầy Đủ

### Stack

| Thao tác | Array (dynamic) | Array (fixed) | Linked List |
|----------|-----------------|--------------|-------------|
| push | O(1) amortized* | O(1) | O(1) |
| pop | O(1) | O(1) | O(1) |
| peek/top | O(1) | O(1) | O(1) |
| isEmpty | O(1) | O(1) | O(1) |
| Space | O(n) | O(capacity) | O(n) + pointer overhead |

*Amortized: đôi khi O(n) khi resize, nhưng trung bình O(1) theo amortized analysis.

### Queue

| Thao tác | Naive Array | Circular Array | Linked List |
|----------|-------------|----------------|-------------|
| enqueue | O(1) amortized | O(1) | O(1) |
| dequeue | O(n) shift! | O(1) | O(1) |
| front | O(1) | O(1) | O(1) |
| isEmpty | O(1) | O(1) | O(1) |
| Space | O(n) | O(capacity) | O(n) + pointer overhead |

### Deque (Python collections.deque)

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| appendleft / append | O(1) | Both ends |
| popleft / pop | O(1) | Both ends |
| peek | O(1) | |
| random access arr[i] | O(n) | Không efficient, dùng list |
| Space | O(n) | |

### Monotonic Stack

| Thao tác | Worst Case | Amortized | Ghi chú |
|----------|-----------|-----------|---------|
| Process n elements | O(n) | O(1) per element | Mỗi element push 1 lần, pop 1 lần |
| Total (all n elements) | O(n) | O(n) | Không phải O(n²) |
| Space | O(n) | O(n) | Worst case stack chứa toàn bộ array |

**Điều kiện Worst case:** Input đã sorted → không có pop → stack chứa n elements.

### Monotonic Deque (Sliding Window Max)

| Thao tác | Complexity | Ghi chú |
|----------|-----------|---------|
| Process all elements | O(n) | Mỗi element vào/ra deque 1 lần |
| Space | O(k) | k = window size |

---

## 5. Code mẫu Python

```python
from collections import deque

# ============================================================
# 1. STACK — implementation và ứng dụng
# ============================================================

class Stack:
    """Stack implementation dùng Python list (dynamic array)."""
    
    def __init__(self):
        self._data = []
    
    def push(self, val):
        """O(1) amortized — list.append()."""
        self._data.append(val)
    
    def pop(self):
        """O(1) — list.pop() từ cuối."""
        if self.is_empty():
            raise IndexError("Pop from empty stack")
        return self._data.pop()
    
    def peek(self):
        """O(1) — xem đỉnh không xóa."""
        if self.is_empty():
            raise IndexError("Peek from empty stack")
        return self._data[-1]
    
    def is_empty(self):
        return len(self._data) == 0
    
    def size(self):
        return len(self._data)


# ============================================================
# Ứng dụng Stack 1: Valid Parentheses
# ============================================================

def is_valid_parentheses(s):
    """
    Kiểm tra chuỗi ngoặc có hợp lệ không.
    Stack: push opening brackets, pop khi gặp closing bracket.
    Time: O(n), Space: O(n)
    """
    stack = []
    matching = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in '({[':
            stack.append(char)
        elif char in ')}]':
            if not stack or stack[-1] != matching[char]:
                return False
            stack.pop()
    
    return len(stack) == 0


# ============================================================
# Ứng dụng Stack 2: Evaluate postfix expression
# ============================================================

def eval_postfix(expression):
    """
    Tính biểu thức postfix: "3 4 + 2 *" = (3+4)*2 = 14.
    Stack-based evaluation.
    Time: O(n), Space: O(n)
    """
    stack = []
    operators = {'+', '-', '*', '/'}
    
    for token in expression.split():
        if token not in operators:
            stack.append(int(token))
        else:
            b = stack.pop()  # second operand
            a = stack.pop()  # first operand
            if token == '+': stack.append(a + b)
            elif token == '-': stack.append(a - b)
            elif token == '*': stack.append(a * b)
            elif token == '/': stack.append(int(a / b))
    
    return stack[0]


# ============================================================
# 2. QUEUE — implementation
# ============================================================

class CircularQueue:
    """Queue implementation dùng circular array."""
    
    def __init__(self, capacity):
        self._capacity = capacity + 1  # +1 phân biệt full vs empty
        self._data = [None] * self._capacity
        self._head = 0
        self._tail = 0
    
    def enqueue(self, val):
        """O(1)."""
        if self.is_full():
            raise OverflowError("Queue is full")
        self._data[self._tail] = val
        self._tail = (self._tail + 1) % self._capacity
    
    def dequeue(self):
        """O(1)."""
        if self.is_empty():
            raise IndexError("Dequeue from empty queue")
        val = self._data[self._head]
        self._head = (self._head + 1) % self._capacity
        return val
    
    def front(self):
        """O(1)."""
        if self.is_empty():
            raise IndexError("Empty queue")
        return self._data[self._head]
    
    def is_empty(self):
        return self._head == self._tail
    
    def is_full(self):
        return (self._tail + 1) % self._capacity == self._head
    
    def size(self):
        return (self._tail - self._head) % self._capacity


# Python chuẩn: dùng collections.deque làm queue
queue = deque()
queue.append(1)      # enqueue → O(1)
queue.append(2)
queue.popleft()      # dequeue → O(1) ← QUAN TRỌNG, không dùng list.pop(0)!


# ============================================================
# 3. MONOTONIC STACK — Next Greater Element
# ============================================================

def next_greater_element(arr):
    """
    Tìm phần tử lớn hơn đầu tiên ở bên phải của mỗi phần tử.
    Nếu không có, trả về -1.
    
    Thuật toán: Monotonic decreasing stack (từ bottom lớn → top nhỏ)
    - Khi gặp phần tử lớn hơn top, pop (tìm được answer cho top)
    - Push phần tử hiện tại
    
    Time: O(n) amortized — mỗi element push/pop đúng 1 lần
    Space: O(n)
    """
    n = len(arr)
    result = [-1] * n
    stack = []  # lưu indices
    
    for i in range(n):
        # Pop tất cả phần tử nhỏ hơn arr[i] — arr[i] là answer của chúng
        while stack and arr[stack[-1]] < arr[i]:
            idx = stack.pop()
            result[idx] = arr[i]
        stack.append(i)
    
    # Phần tử còn trong stack: không có next greater → result = -1 (default)
    return result


# Demo
arr = [2, 1, 5, 3, 4]
print("Next greater:", next_greater_element(arr))
# [5, 5, -1, 4, -1]


# ============================================================
# 4. MONOTONIC STACK — Largest Rectangle in Histogram
# ============================================================

def largest_rectangle_histogram(heights):
    """
    LeetCode 84. Tìm diện tích hình chữ nhật lớn nhất trong histogram.
    
    Monotonic increasing stack: lưu indices theo thứ tự chiều cao tăng.
    Khi gặp thanh thấp hơn, pop các thanh cao hơn và tính diện tích.
    
    Time: O(n), Space: O(n)
    """
    stack = []   # monotonic increasing stack (lưu indices)
    max_area = 0
    heights = heights + [0]  # sentinel: trigger pop tất cả ở cuối
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            # Width: từ stack[-1]+1 đến i-1
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    
    return max_area


print("Largest rectangle:", largest_rectangle_histogram([2, 1, 5, 6, 2, 3]))
# 10


# ============================================================
# 5. MONOTONIC DEQUE — Sliding Window Maximum
# ============================================================

def sliding_window_maximum(arr, k):
    """
    LeetCode 239. Maximum trong mỗi sliding window size k.
    
    Monotonic decreasing deque: lưu indices, front = max của window.
    - Remove từ front nếu out of window (index < i-k+1)
    - Remove từ back nếu nhỏ hơn phần tử hiện tại (không thể là max)
    
    Time: O(n), Space: O(k)
    """
    if not arr or k == 0:
        return []
    
    dq = deque()     # lưu indices, decreasing values
    result = []
    
    for i in range(len(arr)):
        # Xóa phần tử ngoài window từ front
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Xóa phần tử nhỏ hơn từ back (họ không thể là max)
        while dq and arr[dq[-1]] < arr[i]:
            dq.pop()
        
        dq.append(i)
        
        # Bắt đầu thu kết quả khi window đủ size
        if i >= k - 1:
            result.append(arr[dq[0]])  # front là max
    
    return result


print("Sliding window max:", sliding_window_maximum([1,3,-1,-3,5,3,6,7], 3))
# [3, 3, 5, 5, 6, 7]


# ============================================================
# 6. BFS dùng Queue
# ============================================================

def bfs(graph, start):
    """
    BFS traversal dùng Queue.
    Queue đảm bảo xử lý theo level — FIFO.
    Time: O(V+E), Space: O(V)
    """
    visited = set([start])
    queue = deque([start])
    order = []
    
    while queue:
        node = queue.popleft()   # O(1) với deque
        order.append(node)
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return order


# ============================================================
# 7. ITERATIVE DFS dùng Stack — thay thế đệ quy
# ============================================================

def dfs_iterative(graph, start):
    """
    DFS iterative dùng explicit Stack.
    Tránh recursion limit của Python.
    Time: O(V+E), Space: O(V)
    """
    visited = set()
    stack = [start]
    order = []
    
    while stack:
        node = stack.pop()   # O(1)
        if node not in visited:
            visited.add(node)
            order.append(node)
            for neighbor in graph.get(node, []):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return order
```

---

## 6. Khi nào dùng / Không dùng

### Stack — Dùng khi:
- **Expression parsing:** ngoặc, infix → postfix, calculator
- **DFS iterative:** tránh recursion, traversal tree/graph
- **Undo/Redo history:** thao tác gần nhất được undo trước
- **Backtracking:** lưu trạng thái khi explore path
- **Function call simulation:** interpreter, virtual machine
- **Monotonic Stack:** next greater/smaller element, histogram problems

### Queue — Dùng khi:
- **BFS:** level-order traversal, shortest path (unweighted)
- **Task scheduling:** FIFO processing order quan trọng
- **Producer-Consumer pattern:** buffer giữa producer và consumer
- **Cache eviction:** FIFO cache (đơn giản nhất)
- **Stream processing:** xử lý data theo thứ tự đến

### Deque — Dùng khi:
- **Sliding window problems:** thêm/xóa cả hai đầu
- **Palindrome check:** so sánh từ hai đầu
- **Work stealing scheduler:** steal từ back, process từ front
- **Browser history:** forward/back cần cả 2 đầu

### Không dùng:
- Stack khi cần random access → dùng List/Array
- Queue khi cần priority → dùng Heap (Priority Queue)
- Circular queue khi size động → dùng deque
- Monotonic stack khi cần range max với dynamic updates → dùng Segment Tree

---

## 7. So sánh với các cấu trúc liên quan

| Cấu trúc | Order | Access | Insert/Delete | Use case |
|----------|-------|--------|---------------|----------|
| Stack | LIFO | Top only O(1) | Top only O(1) | DFS, undo, parsing |
| Queue | FIFO | Front only O(1) | Front/Back O(1) | BFS, scheduling |
| Deque | Both ends | Both ends O(1) | Both ends O(1) | Sliding window, palindrome |
| Priority Queue | By priority | Max/Min O(1) | O(log n) | Dijkstra, scheduling by priority |
| List/Array | None | Random O(1) | End O(1), middle O(n) | General purpose |
| Linked List | None | Sequential O(n) | Head O(1), tail O(1)* | Dynamic insert/delete |

*Với tail pointer.

### Array-based vs Linked-list Stack

| Tiêu chí | Array-based | Linked-list |
|----------|-------------|-------------|
| Memory layout | Contiguous (cache-friendly) | Non-contiguous |
| Push worst case | O(n) resize (rare) | O(1) always |
| Push amortized | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Memory overhead | Low (no pointers) | High (pointer per node) |
| Preferred in practice | Yes (Python list) | Less common |

---

## 8. Common Pitfalls

### Pitfall 1: Dùng list.pop(0) cho Queue — O(n)!
```python
# SAI: O(n) vì phải shift tất cả elements
queue = []
queue.append(1)
queue.pop(0)  # O(n)! 

# ĐÚNG: dùng collections.deque
from collections import deque
queue = deque()
queue.append(1)
queue.popleft()  # O(1)
```

### Pitfall 2: Không kiểm tra empty trước khi pop
```python
# SAI: IndexError nếu stack empty
def process(stack):
    val = stack.pop()  # crash nếu empty!

# ĐÚNG:
def process(stack):
    if not stack:
        return None
    return stack.pop()
```

### Pitfall 3: Monotonic Stack — nhầm tăng vs giảm
```python
# Next GREATER element: dùng monotonic DECREASING stack
# (pop khi current > top → current là answer của top)

# Next SMALLER element: dùng monotonic INCREASING stack
# (pop khi current < top → current là answer của top)
```

### Pitfall 4: Monotonic Deque — quên remove out-of-window elements
```python
# SAI: quên cleanup phần tử ngoài window
while dq and arr[dq[-1]] < arr[i]:
    dq.pop()
dq.append(i)
# → front có thể là index ngoài window!

# ĐÚNG: luôn remove from front TRƯỚC
while dq and dq[0] < i - k + 1:
    dq.popleft()  # remove out-of-window
while dq and arr[dq[-1]] < arr[i]:
    dq.pop()      # maintain monotonic
dq.append(i)
```

### Pitfall 5: Circular Queue — phân biệt full vs empty
```python
# Khi head == tail: empty HOẶC full — phải phân biệt!
# Giải pháp 1: size counter riêng
# Giải pháp 2: capacity = actual_size + 1, không bao giờ fill hết
# Giải pháp 3: boolean flag is_full
```

### Pitfall 6: Histogram — sentinel ở cuối
```python
# Nếu không có sentinel, phần tử cuối stack không được process
heights = [2, 1, 5, 6, 2, 3]
# Thêm 0 vào cuối để trigger pop tất cả:
heights = heights + [0]  # sentinel
```

---

## 9. Câu hỏi phỏng vấn hay gặp

**Q1. Implement Stack chỉ dùng Queue, và ngược lại.**
→ Stack từ 2 queues: push vào Q1, pop bằng cách chuyển n-1 phần tử sang Q2. Queue từ 2 stacks: enqueue vào S1, dequeue bằng cách chuyển toàn bộ S1 sang S2 (amortized O(1)).

**Q2. LeetCode 20 — Valid Parentheses.**
→ Stack: push opening, pop khi gặp closing, kiểm tra matching.

**Q3. LeetCode 84 — Largest Rectangle in Histogram.**
→ Monotonic increasing stack. Khi gặp thanh thấp hơn, pop và tính diện tích.

**Q4. LeetCode 239 — Sliding Window Maximum.**
→ Monotonic decreasing deque. O(n) total.

**Q5. Tại sao Monotonic Stack là O(n) dù có while loop bên trong?**
→ Amortized: mỗi element được push đúng 1 lần và pop đúng 1 lần → tổng operations = 2n → O(n) amortized.

**Q6. LeetCode 155 — Min Stack.**
→ Dùng 2 stacks: stack chính và stack_min. Stack_min push khi phần tử mới ≤ min hiện tại.

**Q7. Khi nào dùng Stack thay vì đệ quy cho DFS?**
→ Khi cần tránh stack overflow (deep graph), khi cần kiểm soát order xử lý, hoặc khi cần iterative để debug dễ hơn.

**Q8. BFS vs DFS — cấu trúc dữ liệu nào cho mỗi loại?**
→ BFS → Queue (FIFO, đảm bảo level-order). DFS → Stack (LIFO, đi sâu trước). Đệ quy DFS dùng call stack.

**Q9. LeetCode 42 — Trapping Rain Water.**
→ Monotonic Stack: pop khi gặp thanh cao hơn, tính water được trap giữa các thanh.

**Q10. Deque trong Python (`collections.deque`) được implement thế nào?**
→ Doubly-linked list của fixed-size blocks (không phải single linked list). Cân bằng giữa cache locality và O(1) operations ở cả 2 đầu.
