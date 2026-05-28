# Ôn Thi Nhanh — 1.5 Ngày (từ 7h tối 28/05)

> **Cách dùng file này:**
> - Đọc **Key points** → nhớ nhanh điểm cốt lõi
> - Đọc **❓ Cần hiểu rõ** → tự trả lời; câu nào không trả lời được → mở tongquat/ly_thuyet tra ngay
> - Không cần đọc `ly_thuyet.md` chi tiết trừ khi bí

---

## Lịch học

```
  NGÀY 1 — BUỔI TỐI (7 PM – 11 PM)
  ──────────────────────────────────
  19:00  DSA Phần 1        (60 phút)
  20:00  DSA Phần 2        (60 phút)
  21:00  DSA Phần 3        (60 phút)
  22:00  OOP               (60 phút)

  NGÀY 2 — SÁNG & CHIỀU (8 AM – 5 PM)
  ──────────────────────────────────────────────────────
  08:00  OS                (60 phút)
  09:00  Network           (60 phút)
  10:00  System Design     (90 phút)
  11:30  ML Quick          (30 phút)
  12:00  NGHỈ TRƯA
  13:00  AI + Linux        (60 phút)
  14:00  Toán              (60 phút)
  15:00  Review yếu điểm  (120 phút)

  NGÀY 2 — BUỔI TỐI (5 PM – 11:30 PM)  ← NỬA NGÀY BỔ SUNG
  ──────────────────────────────────────────────────────
  17:00  NGHỈ                          (30 phút)
  17:30  Backend                       (60 phút)
  18:30  Database Advanced             (60 phút)
  19:30  API Advanced                  (60 phút)
  20:30  System Design Scenarios       (60 phút)
  21:30  Software Engineering          (120 phút)
           └ Concurrency & Async       (45 phút)
           └ Clean Code + Testing      (30 phút)
           └ Docker + CI/CD            (30 phút)
           └ Mock Test                 (15 phút)
```

---

# ═══════════════════════════════════════
# BUỔI TỐI
# ═══════════════════════════════════════

---

## 19:00 · DSA Phần 1 — Nền tảng & Kỹ thuật mảng · 60 phút
> [📖 Tongquat DSA](tongquat_dsa.md) | Chi tiết: [dsa/01_bigo](dsa/01_bigo/ly_thuyet.md) → [dsa/06_prefix_sum](dsa/06_prefix_sum/ly_thuyet.md)

---

### 📊 Big O · 10 phút

**Key points:**
- `O(log n)` = mỗi bước loại **nửa** không gian → Binary Search, BST balanced
- Đệ quy **2 nhánh** → `O(2ⁿ)`, không phải `O(n)`
- **Amortized O(1)**: dynamic array append — thỉnh thoảng O(n) nhưng trung bình O(1)
- Space complexity: nhớ tính **call stack** với đệ quy — O(h) với tree, O(n) với linear recursion

**❓ Cần hiểu rõ:**
- Big O đo lường cái gì? Tại sao bỏ hệ số và bậc thấp? (O(2n) → O(n))
- O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ) — cho ví dụ thực tế mỗi loại?
- Worst case, Average case, Best case — khi nào cần phân biệt?
- Amortized complexity là gì? Dynamic array resize tại sao O(1) amortized?
- Đệ quy với 1 nhánh (Fibonacci với memo) vs 2 nhánh (naive) — complexity khác nhau thế nào?

---

### 📋 Array & String · 10 phút

**Key points:**
- Array: access O(1) vì lưu liên tiếp trong bộ nhớ
- String immutable (Python/Java) → concat trong loop = O(n²), dùng `join` hoặc StringBuilder
- **Subarray** = liên tiếp (contiguous); **Subsequence** = không cần liên tiếp
- Off-by-one: `range(n-1)` hay `range(n)`? → vẽ ví dụ nhỏ trước khi code

**❓ Cần hiểu rõ:**
- Array lưu trong bộ nhớ thế nào? Tại sao access O(1) nhưng insert/delete O(n)?
- Dynamic array (Python list) khác static array thế nào? Resize xảy ra khi nào?
- Tại sao `"".join(list)` nhanh hơn `s += "..."` trong loop?
- In-place operation là gì? Swap 2 phần tử không cần biến temp trong Python viết thế nào?

---

### 🗺️ HashMap & HashSet · 10 phút

**Key points:**
- O(1) average, O(n) worst case (khi có nhiều collision)
- Key phải **hashable** — list không hash được, dùng **tuple**
- Pattern đếm tần suất: `Counter(arr)` hoặc `defaultdict(int)`
- Pattern two-pass: lần 1 build map, lần 2 dùng map

**❓ Cần hiểu rõ:**
- Hash function làm gì? Collision là gì? Có mấy cách xử lý collision?
- Load factor là gì? Ảnh hưởng thế nào đến performance? Khi nào HashMap rehash?
- HashSet khác HashMap thế nào? Khi nào dùng Set thay Map?
- Tại sao key phải hashable? Nếu dùng list làm key thì lỗi gì?

---

### 👉 Two Pointers · 15 phút

**Key points:**
- **Left/Right** (mảng đã sort): 2 Sum sorted, 3 Sum, Container with most water
- **Fast/Slow** (linked list): cycle detection (Floyd), tìm middle node
- **Merge** (2 sorted arrays): con trỏ mỗi mảng 1 cái
- Điều kiện áp dụng: thường cần mảng đã **sort** hoặc tính đơn điệu

**❓ Cần hiểu rõ:**
- Two Pointers cải thiện complexity từ O(n²) xuống O(n) — tại sao?
- Left/Right pointer: điều kiện dừng `while left < right` — tại sao `<` không phải `<=`?
- Floyd's Cycle Detection: fast đi 2 bước, slow đi 1 — tại sao chúng gặp nhau khi có cycle?
- Tìm middle node: slow pointer dừng ở đâu nếu n chẵn? n lẻ?

---

### 🪟 Sliding Window · 15 phút

**Key points:**
- **Fixed window**: sum/avg của k phần tử liên tiếp — trượt 1 phần tử mỗi bước
- **Variable window**: expand `right`, shrink `left` khi vi phạm điều kiện
- Template variable:
  ```
  left = 0
  for right in range(n):
      # thêm arr[right] vào window
      while window vi phạm điều kiện:
          # bỏ arr[left] ra, left += 1
      # cập nhật kết quả
  ```
- Bẫy: quên update state khi `left` tăng

**❓ Cần hiểu rõ:**
- Sliding Window khác Two Pointers thông thường thế nào?
- Khi nào expand (tăng right)? Khi nào shrink (tăng left)?
- Tại sao Sliding Window O(n) mặc dù có vòng while lồng nhau?
- Monotonic Deque dùng trong bài Sliding Window Maximum như thế nào?
- Khi nào dùng Sliding Window, khi nào dùng Prefix Sum?

---

### ➕ Prefix Sum · 10 phút

**Key points:**
- `prefix[i] = prefix[i-1] + arr[i-1]` (1-indexed)
- `sum(l→r) = prefix[r+1] - prefix[l]` → O(1) thay vì O(n) mỗi query
- **Pattern hay gặp**: đếm subarray có sum = k
  ```python
  count[prefix_sum - k] += count vào kết quả
  ```
- 2D prefix: `p[i][j] = p[i-1][j] + p[i][j-1] - p[i-1][j-1] + grid[i][j]`

**❓ Cần hiểu rõ:**
- Tại sao dùng prefix sum thay vì tính trực tiếp mỗi lần query?
- Công thức `sum(l→r) = prefix[r+1] - prefix[l]` — tại sao r+1 không phải r?
- Bài "subarray sum = k" dùng prefix + hashmap giải thế nào? Logic `count[prefix_sum - k]` là gì?
- Prefix Sum và Difference Array — khác nhau thế nào? Cái nào dùng khi nào?

---

## 20:00 · DSA Phần 2 — Đệ quy & Cấu trúc tuyến tính · 60 phút
> Chi tiết: [dsa/07_recursion](dsa/07_recursion/ly_thuyet.md) → [dsa/10_tree_bst](dsa/10_tree_bst/ly_thuyet.md)

---

### 🔄 Recursion · 15 phút

**Key points:**
- Mọi đệ quy = **base case** + **thu nhỏ bài toán về phía base case**
- Dùng **memoization** khi: cùng input gọi nhiều lần → overlapping subproblems
- **Tail recursion** = đệ quy ở cuối hàm → compiler tối ưu được stack (Python **không** tối ưu)
- Bẫy: quên base case → stack overflow; quên `return` từ nhánh đệ quy; quên restore state khi backtrack

**❓ Cần hiểu rõ:**
- Base case và recursive case là gì? Nếu thiếu base case thì sao?
- Memoization là gì? Khi nào nên dùng vs không cần dùng?
- Recurrence relation T(n) = 2T(n/2) + O(n) giải ra complexity bao nhiêu?
- Đệ quy vs Iteration — trade-off về performance và readability?
- Tại sao Python không tối ưu tail recursion? Giới hạn stack depth mặc định là bao nhiêu?

---

### 📚 Stack & Queue · 15 phút

**Key points:**
- **Stack** LIFO: undo/redo, call stack, DFS, parsing brackets
- **Queue** FIFO: BFS, task queue, scheduling
- **Monotonic Stack**: duy trì stack tăng/giảm dần → next greater element, histogram
  - Pop khi phần tử mới vi phạm tính đơn điệu
- **Deque**: O(1) ở cả 2 đầu → sliding window max
- `list.pop(0)` là O(n) → dùng `collections.deque` với `popleft()`

**❓ Cần hiểu rõ:**
- Stack và Queue — ứng dụng thực tế nào dùng Stack, nào dùng Queue?
- Monotonic Stack là gì? Giải bài "Next Greater Element" thế nào?
- Deque là gì? Khác Stack và Queue ở chỗ nào?
- Min Stack (getMin O(1)) cài đặt thế nào?
- Tại sao BFS dùng Queue, DFS dùng Stack?

---

### 🔗 Linked List · 15 phút

**Key points:**
- Insert/delete đầu/cuối O(1), ở giữa O(n) (cần duyệt để tìm vị trí)
- **Fast/Slow pointer**: cycle → Floyd's algorithm; middle → slow dừng ở mid
- **Reverse in-place**: 3 biến `prev=None, curr=head, next_node` — vẽ trước khi code
- **Dummy node**: tránh edge case khi xóa head hoặc insert đầu list
- Bẫy: lưu `next = curr.next` trước khi đứt link

**❓ Cần hiểu rõ:**
- Linked List khác Array thế nào? Khi nào dùng Linked List thay Array?
- Singly vs Doubly Linked List — khi nào cần Doubly?
- Tại sao cần dummy node? Cho ví dụ edge case nó giải quyết.
- Merge 2 sorted Linked Lists — complexity là bao nhiêu?
- Tìm node thứ k từ cuối — two pointers giải thế nào?

---

### 🌳 Tree & BST · 15 phút

**Key points:**
- **Inorder** BST = mảng đã sort → dùng để validate BST
- **Preorder** = serialize/reconstruct tree
- **Postorder** = tính từ lá lên (height, diameter, subtree sum)
- BST operations: O(log n) average, **O(n) worst** (suy biến thành linked list)
- **LCA**: nếu cả 2 node ở 2 phía root → root là LCA
- Bẫy: height vs depth; null check trước `.left/.right`

**❓ Cần hiểu rõ:**
- Root, Node, Leaf, Edge, Height, Depth — định nghĩa từng cái?
- BST property là gì? Validate BST bằng inorder thế nào?
- Khi nào BST bị degenerate? Giải pháp là gì (AVL, Red-Black)?
- LCA trong BST khác LCA trong Binary Tree thông thường thế nào?
- Level-order traversal (BFS trên tree) cài đặt thế nào?
- Tính height của tree bằng đệ quy — logic `1 + max(left, right)` từ đâu ra?

---

## 21:00 · DSA Phần 3 — Graph & Thuật toán nâng cao · 60 phút
> Chi tiết: [dsa/11_heap](dsa/11_heap/ly_thuyet.md) → [dsa/20_backtracking](dsa/20_backtracking/ly_thuyet.md)

---

### ⛰️ Heap · 10 phút

**Key points:**
- Python `heapq` = **min-heap** — max-heap dùng giá trị âm `-x`
- `heappush` O(log n), `heappop` O(log n), `heapify` **O(n)**
- **Top-K largest**: dùng min-heap size K → push, nếu size > K thì pop
- **K-way merge**: push `(value, list_idx, elem_idx)` vào heap

**❓ Cần hiểu rõ:**
- Heap là gì? Min-heap và Max-heap khác nhau thế nào?
- Heap property là gì? Heap có phải sorted array không?
- Heap cài bằng array: index parent/left child/right child tính thế nào?
- `heapify` tại sao O(n) chứ không phải O(n log n)?
- Top-K bằng min-heap size K — tại sao dùng min-heap không phải max-heap?
- Priority Queue là gì? Quan hệ với Heap?

---

### 🕸️ Graph + DFS/BFS · 15 phút

**Key points:**
- **Adjacency List** O(V+E) space — dùng cho sparse graph (thực tế)
- **Adjacency Matrix** O(V²) — check edge O(1), dùng khi dense graph
- **BFS** = shortest path (unweighted), level-order → dùng Queue
- **DFS** = tất cả paths, connected components, cycle detection → dùng Stack/recursion
- Add vào `visited` khi **push** vào queue, không phải khi pop
- **Union-Find**: detect cycle, connected components — path compression → O(α(n))

**❓ Cần hiểu rõ:**
- Directed vs Undirected, Weighted vs Unweighted — khi nào cần loại nào?
- Adjacency List vs Matrix — space và time trade-off?
- Tại sao phải add vào visited khi push, không phải khi pop?
- Union-Find hoạt động thế nào? Path compression là gì?
- Dijkstra dùng khi nào? Có dùng được với negative weight không? Tại sao?
- Bellman-Ford khác Dijkstra thế nào? Complexity?
- Topological Sort chỉ dùng được với loại graph nào? Tại sao?
- Kahn's Algorithm (in-degree) hoạt động thế nào? Detect cycle thế nào?

---

### 🔍 Binary Search · 10 phút

**Key points:**
- Template tránh infinite loop và overflow:
  ```python
  left, right = 0, n - 1
  while left <= right:
      mid = left + (right - left) // 2
      if condition: right = mid - 1
      else: left = mid + 1
  ```
- **Search space ẩn**: bài "tìm giá trị nhỏ nhất thỏa điều kiện" → binary search trên answer
- Bẫy: `(left + right) // 2` có thể overflow với integer lớn

**❓ Cần hiểu rõ:**
- Binary Search yêu cầu điều kiện gì với input?
- `while left <= right` vs `while left < right` — khi nào dùng cái nào?
- `right = mid - 1` và `left = mid + 1` — tại sao không phải `right = mid`?
- "Search space ẩn" là gì? Cho ví dụ bài toán cụ thể.
- Tìm leftmost/rightmost index thỏa điều kiện — cài đặt thế nào?

---

### ↕️ Sorting · 10 phút

**Key points:**

| Sort | Best | Average | Worst | Space | Stable? |
|------|------|---------|-------|-------|---------|
| Quick Sort | O(n log n) | O(n log n) | **O(n²)** | O(log n) | No |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | **O(n)** | **Yes** |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | **O(1)** | No |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |

- Python `sort()` = **TimSort** = O(n log n) worst, O(n) best (nearly sorted)

**❓ Cần hiểu rõ:**
- Quick Sort O(n²) worst case xảy ra khi nào? Cách tránh?
- Stable sort nghĩa là gì? Khi nào cần stable sort?
- Khi nào chọn Merge Sort, khi nào Quick Sort?
- Counting/Radix Sort điều kiện áp dụng là gì?
- In-place sort là gì? Sort nào in-place, sort nào không?

---

### 💰 Greedy · 10 phút

**Key points:**
- Đúng khi: **greedy choice property** + **optimal substructure**
- Interval scheduling: sort by **end time** → greedy chọn interval không overlap
- Jump game: track `max_reachable` index
- Bẫy: Greedy sai với coin change (coin set không standard)

**❓ Cần hiểu rõ:**
- Greedy Choice Property và Optimal Substructure là gì?
- Tại sao Greedy không phải lúc nào cũng đúng? Cho ví dụ sai.
- Exchange Argument dùng để chứng minh Greedy đúng thế nào?
- Greedy vs DP — khi nào dùng cái nào?

---

### 🧮 DP · 10 phút

**Key points:**
- Nhận biết: **optimal substructure** + **overlapping subproblems**
- **Top-down** (memo): dễ nghĩ, có overhead call, có thể stack overflow
- **Bottom-up** (tabulation): tốt hơn space, không stack overflow
- Dạng DP:
  ```
  1D:      dp[i] phụ thuộc dp[i-1], dp[i-2]   → Fibonacci, Climbing Stairs
  2D:      dp[i][j]                             → LCS, Edit Distance, Knapsack
  Interval: dp[i][j] = subproblem [i..j]        → Matrix Chain, Burst Balloons
  Bitmask: dp[mask][i]                          → TSP
  ```
- State reduction: Knapsack 2D → 1D bằng iterate **ngược**

**❓ Cần hiểu rõ:**
- Optimal Substructure và Overlapping Subproblems là gì?
- Top-down vs Bottom-up — trade-off? Khi nào dùng cái nào?
- State trong DP là gì? Làm sao xác định state?
- Thứ tự tính computation order quan trọng thế nào trong Bottom-up?
- Knapsack 0/1: 2D và optimize xuống 1D như thế nào? Tại sao iterate ngược?
- LCS, Edit Distance — DP state transition là gì?

---

### ↩️ Backtracking · 5 phút

**Key points:**
- Template:
  ```python
  def backtrack(state, choices):
      if is_solution(state):
          result.append(state[:])  # COPY!
          return
      for choice in choices:
          if is_valid(choice):
              state.append(choice)
              backtrack(state, next_choices)
              state.pop()          # UNDO
  ```
- **Pruning** = cắt nhánh sớm → tăng performance đáng kể
- Bẫy: quên `state[:]` → tất cả result trỏ cùng object; quên `pop()` → state nhiễm

**❓ Cần hiểu rõ:**
- Backtracking khác brute force thế nào?
- Tại sao phải `result.append(state[:])` chứ không phải `result.append(state)`?
- Pruning là gì? Cho ví dụ pruning cụ thể trong bài N-Queens.
- Backtracking vs DP — khi nào dùng cái nào?
- Permutation, Combination, Subset — điểm khác nhau trong cài đặt backtracking?

---

## 22:00 · OOP · 60 phút
> [📖 Tongquat OOP](tongquat_oop.md) | Chi tiết: [oop/ROADMAP.md](oop/ROADMAP.md)

---

### 🧱 Class & Object + 🔒 Encapsulation · 15 phút

**Key points:**
- `__init__` không "tạo" object — `__new__` mới tạo, `__init__` chỉ khởi tạo
- Instance variable vs Class variable: mutable default argument trong `__init__` → shared giữa instances
- `@property` + setter: encapsulation mà vẫn dùng cú pháp attribute
- Name mangling: `__var` → `_ClassName__var` — không phải truly private

**❓ Cần hiểu rõ:**
- Class và Object quan hệ thế nào? Instance variable vs Class variable khác gì?
- `__init__` vs `__new__` — làm gì khác nhau?
- Instance method, `@classmethod`, `@staticmethod` — khác nhau thế nào? Khi nào dùng cái nào?
- `self` trong Python là gì? Tại sao phải truyền?
- Mutable default argument `def __init__(self, data=[])` — lỗi gì xảy ra?
- `__str__` vs `__repr__` — dùng để làm gì?

---

### 👨‍👩‍👦 Inheritance · 15 phút

**Key points:**
- `super()` trong Python 3 dùng **MRO** (C3 linearization) — không đơn giản là gọi parent
- **MRO**: `ClassName.__mro__` để xem thứ tự tìm kiếm method
- Override `__str__`, `__repr__` — `__repr__` cho debug, `__str__` cho display
- **Diamond Problem**: Python giải quyết bằng C3 MRO

**❓ Cần hiểu rõ:**
- IS-A relationship là gì? Khi nào dùng Inheritance?
- `super()` hoạt động thế nào với MRO? Cho ví dụ multiple inheritance.
- C3 Linearization là gì? `W(Y, Z).__mro__` — kết quả là gì?
- Diamond Problem là gì? Python giải quyết thế nào?
- Method overriding vs Method overloading — khác nhau thế nào?
- "Composition over Inheritance" nghĩa là gì? Khi nào ưu tiên composition?

---

### 🎭 Polymorphism + 🎨 Abstraction · 15 phút

**Key points:**
- **Duck typing** (Python): không check type, check behavior — "if it walks like a duck"
- **Runtime polymorphism** = method overriding + virtual dispatch
- **Compile-time polymorphism** = overloading (Python không native, dùng `*args`)
- Abstract class: `from abc import ABC, abstractmethod` → không instantiate được

**❓ Cần hiểu rõ:**
- Polymorphism là gì? Tại sao hữu ích?
- Duck typing là gì? Python ưu tiên duck typing — tại sao?
- Virtual method (vtable) là gì? Java/C++ dispatch thế nào?
- Abstraction khác Encapsulation ở chỗ nào?
- `@abstractmethod` — nếu subclass không implement thì sao?

---

### 📜 Interface & Abstract Class · 15 phút

**Key points:**

| | Abstract Class | Interface |
|-|----------------|-----------|
| Quan hệ | IS-A (bản thiết kế) | CAN-DO (hợp đồng) |
| Multiple inherit | Không (Java/C#) | Có |
| State (fields) | Có | Không |
| Constructor | Có | Không (Java/C#) |
| Code cụ thể | Có | Có (default method Java 8+) |

- Interface = định nghĩa contract, không phụ thuộc implementation
- **Dependency Injection**: inject qua constructor, inject interface không phải concrete

**❓ Cần hiểu rõ:**
- Khi nào dùng Abstract Class, khi nào dùng Interface?
- Python không có `interface` keyword — mô phỏng bằng cách nào?
- Dependency Injection là gì? Tại sao inject interface thay vì concrete class?
- "Program to an interface, not an implementation" — nghĩa là gì thực tế?

---

### 🏗️ SOLID · 15 phút

**Key points — hỏi VI PHẠM, không chỉ định nghĩa:**

| Nguyên tắc | Vi phạm điển hình | Cách sửa |
|------------|------------------|---------|
| **S**RP | Class vừa xử lý DB vừa format JSON vừa gửi email | Tách class theo responsibility |
| **O**CP | `if type == "A"... elif type == "B"` để xử lý hành vi | Dùng polymorphism / Strategy |
| **L**SP | Subclass throw exception mới / yếu hóa precondition | Redesign hierarchy |
| **I**SP | Interface 10 method nhưng class chỉ implement 2 | Tách nhiều interface nhỏ |
| **D**IP | Class A tạo `new B()` bên trong (hardcode dependency) | Inject B qua constructor |

**❓ Cần hiểu rõ:**
- SRP: class có "nhiều hơn 1 lý do để thay đổi" nghĩa là gì?
- OCP: "open for extension, closed for modification" — ví dụ thực tế?
- LSP: "yếu hóa precondition" của subclass nghĩa là gì? Cho ví dụ vi phạm LSP.
- ISP: interface to xấu vì lý do gì?
- DIP: module high-level và low-level cùng depend on abstraction — cấu trúc thế nào?

---

### 🎯 Design Patterns · 15 phút

**Key points:**

| Pattern | Loại | Dùng khi |
|---------|------|---------|
| **Singleton** | Creational | Cần đúng 1 instance (config, logger) |
| **Factory Method** | Creational | Tạo object mà không biết exact class |
| **Builder** | Creational | Object phức tạp nhiều optional fields |
| **Observer** | Behavioral | Một thay đổi → notify nhiều objects |
| **Strategy** | Behavioral | Swap algorithm lúc runtime |
| **Decorator** | Structural | Thêm behavior không sửa class gốc |
| **Adapter** | Structural | 2 interface không tương thích |

**❓ Cần hiểu rõ:**
- Singleton: thread-safe Singleton cài thế nào? Double-checked locking là gì?
- Factory Method vs Abstract Factory — khác nhau thế nào?
- Builder: "telescoping constructor" là gì? Builder giải quyết thế nào?
- Observer: Subject và Observer là gì? Ví dụ thực tế nào dùng Observer pattern?
- Strategy: inject algorithm nghĩa là gì? Cho ví dụ thực tế.
- Decorator (OOP) vs Python `@decorator` — tại sao khác nhau?

---

# ═══════════════════════════════════════
# NGÀY 2
# ═══════════════════════════════════════

---

## 08:00 · OS · 60 phút
> [📖 Tongquat OS](tongquat_os.md) | Chi tiết: [os/ROADMAP.md](os/ROADMAP.md)

---

### ⚙️ Process & Thread · 20 phút

**Key points:**

| | Process | Thread |
|-|---------|--------|
| Memory | Riêng biệt (isolated) | Chia sẻ trong cùng process |
| Context switch | Nặng (lưu/restore toàn bộ PCB) | Nhẹ hơn |
| Giao tiếp | IPC (pipe, socket, shared mem) | Shared memory trực tiếp |
| Crash ảnh hưởng | Isolated | 1 thread crash → cả process |
| Tạo mới | `fork()` — tốn kém | `pthread_create()` — rẻ hơn |

- **PCB**: lưu registers, PC, stack pointer, open files của process
- `fork()` + `exec()`: fork nhân bản process → exec thay image mới
- Process states: new → ready → running → waiting → terminated

**❓ Cần hiểu rõ:**
- Process và Thread khác nhau cốt lõi ở điều gì?
- Context switch tốn kém vì cái gì phải lưu/restore?
- Tại sao crash 1 thread có thể kill cả process?
- `fork()` trả về gì cho parent, trả về gì cho child?
- Zombie process là gì? Orphan process là gì?
- Multi-threading vs Multi-processing — khi nào dùng cái nào?

---

### 🧠 Memory Management · 20 phút

**Key points:**
- **Virtual memory**: mỗi process thấy address space riêng → MMU translate virtual → physical
- **Page fault**: access page không có trong RAM → OS load từ disk (swap)
- **Thrashing**: page fault liên tục vì RAM quá nhỏ → CPU dành toàn thời gian swap
- **TLB**: cache của page table → giảm latency dịch địa chỉ
- **Memory leak**: heap không free → RAM dần cạn kiệt

**❓ Cần hiểu rõ:**
- Virtual memory là gì? Tại sao cần? Lợi ích so với physical memory trực tiếp?
- MMU làm gì? Page table là gì?
- TLB là gì? Nếu không có TLB thì chậm thế nào?
- Page fault xảy ra thế nào? OS xử lý ra sao?
- Thrashing là gì? Làm sao phòng tránh?
- Internal fragmentation vs External fragmentation — xuất hiện ở đâu?
- Heap vs Stack trong process memory model — khác nhau thế nào?

---

### 📅 CPU Scheduling · 10 phút

**Key points:**

| Algorithm | Preemptive? | Starvation? | Tốt khi |
|-----------|------------|-------------|---------|
| FCFS | Không | Không | Batch jobs |
| SJF/SRTF | Có/Không | Có (long jobs) | Min avg waiting time |
| Round Robin | Có | Không | Time-sharing, fairness |
| Priority | Cả hai | Có | Real-time |

- Aging: tăng priority theo thời gian chờ → giải quyết starvation
- Turnaround time = Completion − Arrival; Waiting time = Turnaround − Burst

**❓ Cần hiểu rõ:**
- Preemptive vs Non-preemptive scheduling — khác nhau thế nào?
- FCFS convoy effect là gì? Tại sao xảy ra?
- Tại sao SJF tối ưu average waiting time nhưng có vấn đề starvation?
- Round Robin quantum size ảnh hưởng thế nào? Quá nhỏ thì sao? Quá lớn thì sao?
- Turnaround time, Waiting time, Response time — công thức tính?

---

### 🔒 Deadlock · 10 phút

**Key points:**
- **4 điều kiện Coffman** (phải đồng thời đủ 4):
  1. Mutual Exclusion 2. Hold & Wait 3. No Preemption 4. Circular Wait
- Phá **bất kỳ 1** → không deadlock
- **Banker's Algorithm**: kiểm tra safe state trước khi cấp tài nguyên
- **Deadlock** ≠ **Livelock** (đang chạy nhưng không tiến triển) ≠ **Starvation**

**❓ Cần hiểu rõ:**
- Giải thích từng điều kiện Coffman bằng ví dụ thực tế.
- Phá điều kiện nào là thực tế nhất trong hệ thống thực?
- Deadlock Prevention, Avoidance, Detection — khác nhau thế nào?
- Banker's Algorithm làm gì? Safe state là gì?
- Deadlock vs Livelock vs Starvation — phân biệt rõ 3 cái này.

---

### 📁 File System + 🔄 Synchronization · 10 phút

**Key points:**
- **inode**: metadata (permission, size, pointer tới blocks) — **không chứa tên file**
- **Hard link** share inode; **Soft link** là shortcut có thể broken
- **Race condition**: kết quả phụ thuộc timing → luôn assume worst case
- **Mutex**: binary, có ownership — chỉ thread lock mới unlock được
- **Semaphore**: counter, không có ownership — producer-consumer
- **Monitor**: mutex + condition variable — Java `synchronized`
- **Spinlock**: busy-wait — tốt khi critical section rất ngắn

**❓ Cần hiểu rõ:**
- inode là gì? Tại sao không chứa tên file?
- Hard link vs Soft link — xóa file gốc thì hard link và soft link xử lý thế nào?
- Race condition là gì? Cho ví dụ `counter += 1` với 2 threads.
- Mutex vs Semaphore vs Monitor — bảng so sánh?
- Spinlock khi nào tốt, khi nào xấu?
- Condition variable `wait()` và `signal()` làm gì?
- Producer-Consumer problem giải bằng Semaphore thế nào?

---

## 09:00 · Network · 60 phút
> [📖 Tongquat Network](tongquat_network.md) | Chi tiết: [network/ROADMAP.md](network/ROADMAP.md)

---

### 🌐 OSI Model · 15 phút

**Key points:**
```
7. Application  → HTTP, FTP, SMTP, DNS
6. Presentation → TLS, SSL, JPEG (mã hóa, nén, format)
5. Session      → NetBIOS, RPC (quản lý phiên)
4. Transport    → TCP, UDP (port, end-to-end)
3. Network      → IP, Router (routing, IP address)
2. Data Link    → Ethernet, Switch (MAC address, frame)
1. Physical     → Cable, Hub, NIC (bit transmission)
```
Mnemonic (từ dưới lên): "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way"

**❓ Cần hiểu rõ:**
- OSI Model vs TCP/IP Model (4 tầng) — tầng nào gộp vào đâu?
- Encapsulation trong networking là gì? Mỗi tầng thêm gì?
- PDU từng tầng gọi là gì? (bit, frame, packet, segment, data)
- Router hoạt động ở tầng nào? Switch? Hub?
- Tầng nào dùng IP address? Tầng nào dùng MAC address?

---

### 🔗 TCP/IP · 25 phút

**Key points:**
- **3-way handshake**: SYN → SYN-ACK → ACK (establish connection)
- **4-way teardown**: FIN → ACK → FIN → ACK (close connection)
- **TCP vs UDP**:
  ```
  TCP: reliable, ordered, flow control, congestion control → HTTP, SSH, FTP
  UDP: fast, no guarantee → DNS, video stream, gaming
  ```
- **Sliding window**: gửi nhiều packet không cần ACK từng cái
- **TIME_WAIT**: đảm bảo ACK cuối đến được peer trước khi close
- TCP **không** đảm bảo message boundary → phải tự parse

**❓ Cần hiểu rõ:**
- Tại sao 3-way handshake cần 3 bước, không phải 2?
- Flow control và Congestion control khác nhau thế nào?
- TIME_WAIT state tồn tại bao lâu? Tại sao cần?
- TCP không đảm bảo message boundary — gây ra vấn đề gì thực tế?
- IPv4 vs IPv6 — điểm khác nhau chính?
- Subnet mask `/24` nghĩa là bao nhiêu hosts? Tính thế nào?

---

### 🌍 HTTP/HTTPS · 25 phút

**Key points:**
- **HTTP/1.1**: persistent connection, pipelining (nhưng head-of-line blocking)
- **HTTP/2**: multiplexing trên 1 TCP connection, header compression (HPACK)
- **HTTP/3**: chạy trên **QUIC** (UDP-based) → không còn HoL blocking
- **TLS handshake**: ClientHello → ServerHello + Certificate → Key Exchange → Finished
- Status codes:

| Code | Ý nghĩa |
|------|---------|
| 200 OK | Thành công |
| 201 Created | POST tạo resource mới |
| 301 Moved Permanently | Redirect vĩnh viễn |
| 302 Found | Redirect tạm thời |
| 304 Not Modified | Cache còn hợp lệ |
| 400 Bad Request | Request sai format |
| 401 Unauthorized | Chưa authenticate |
| 403 Forbidden | Đã auth, không có quyền |
| 404 Not Found | Resource không tồn tại |
| 429 Too Many Requests | Rate limit |
| 500 Internal Server Error | Server lỗi |
| 503 Service Unavailable | Server overload/down |

**❓ Cần hiểu rõ:**
- HTTP stateless nghĩa là gì? Ảnh hưởng thế nào đến thiết kế?
- HTTP methods: GET, POST, PUT, DELETE, PATCH — mỗi cái dùng khi nào?
- Idempotent method là gì? Method nào idempotent, method nào không?
- Head-of-line blocking là gì? HTTP/2 giải quyết thế nào? HTTP/3 giải quyết thế nào?
- QUIC là gì? Tại sao HTTP/3 dùng UDP không phải TCP?
- CORS là gì? Ai enforce? Tại sao không phải security mechanism thực sự?
- Cookie vs Session vs JWT — mỗi cái dùng để làm gì?

---

### 📡 DNS · 10 phút

**Key points:**
- **Recursive query**: resolver làm hết thay client
- **Iterative query**: client tự hỏi từng bước (Root → TLD → Authoritative)
- Record types: A=IPv4, AAAA=IPv6, CNAME=alias, MX=mail, NS=nameserver, TXT=verify
- **TTL**: thời gian cache — thấp = update nhanh, cao = ít load lên DNS server
- CNAME không dùng được ở apex domain (root domain)

**❓ Cần hiểu rõ:**
- DNS resolve từ đầu đến cuối diễn ra thế nào? (từ lúc gõ URL đến khi có IP)
- Recursive vs Iterative query — ai làm gì?
- Tại sao CNAME không dùng ở apex domain? Giải pháp là gì?
- DNS poisoning là gì? DNS over HTTPS (DoH) giải quyết gì?

---

### 🔌 Socket + 🛡️ Security · 10 phút

**Key points:**
- **Socket**: endpoint = `(IP, Port)`; TCP socket = `(src_ip, src_port, dst_ip, dst_port)`
- `epoll` O(1) vs `select` O(n) — cho multiplexing nhiều socket
- **Symmetric** (AES): nhanh, cần share key trước
- **Asymmetric** (RSA): chậm, dùng để exchange symmetric key
- TLS dùng cả 2: asymmetric exchange session key → symmetric encrypt data
- Tấn công: MITM, CSRF (forged request), XSS (inject script), SQL injection

**❓ Cần hiểu rõ:**
- Blocking vs Non-blocking socket — khác nhau thế nào?
- `select`, `poll`, `epoll` — tại sao epoll tốt hơn?
- TLS dùng cả 2 loại encryption — tại sao không dùng asymmetric cho toàn bộ?
- CSRF attack là gì? CSRF token phòng thủ thế nào?
- XSS là gì? SQL injection là gì? Phòng chống thế nào?
- Hashing (bcrypt, SHA) khác encryption thế nào?

---

## 10:00 · System Design · 90 phút
> [📖 REVIEW chi tiết](REVIEW_1_5_DAYS.md) | [system-design/ROADMAP.md](system-design/ROADMAP.md)

---

### 📈 Scalability · 10 phút

**Key points:**
- **Vertical** (scale up): đơn giản, nhưng giới hạn hardware + single point of failure
- **Horizontal** (scale out): cần **stateless** service — session ở Redis/DB, không ở RAM local
- **Bottleneck**: CPU-bound vs I/O-bound vs Memory-bound → giải pháp khác nhau

**❓ Cần hiểu rõ:**
- Vertical vs Horizontal scaling — trade-off cụ thể?
- Tại sao horizontal scaling yêu cầu stateless?
- CPU-bound, I/O-bound, Memory-bound — làm sao phân biệt? Giải pháp mỗi loại?

---

### ⚖️ Load Balancing · 10 phút

**Key points:**
- Round Robin, Weighted RR, Least Connections, IP Hash (sticky), Random
- **L4** (TCP level): nhanh, không hiểu HTTP
- **L7** (HTTP level): smart routing, SSL termination, content-based routing
- Sticky session → **external session store** (Redis) — không lưu ở LB

**❓ Cần hiểu rõ:**
- L4 vs L7 Load Balancer — trade-off? Khi nào dùng cái nào?
- IP Hash (sticky session) — vấn đề gì nếu server chết?
- Health check trong LB hoạt động thế nào?
- Active-Active vs Active-Passive HA cho chính LB?

---

### ⚡ Caching · 15 phút

**Key points:**
- **Cache-aside** (lazy loading): app đọc cache trước → miss → đọc DB → write cache
- **Write-through**: write cache VÀ DB cùng lúc → consistent, write latency cao
- **Write-behind** (write-back): write cache trước, async DB → fast, risk mất data
- Eviction: LRU (phổ biến), LFU (access không đều), TTL
- **Cache stampede**: nhiều request cùng miss cùng lúc → DB quá tải → mutex lock hoặc probabilistic early expiration
- **CDN**: geographically distributed cache cho static assets

**❓ Cần hiểu rõ:**
- Cache hit rate tốt là bao nhiêu? Cache miss gây ra vấn đề gì?
- Cache-aside, Write-through, Write-behind — consistency/performance trade-off?
- Read-through cache là gì?
- LRU cache cài đặt O(1) get + put bằng HashMap + Doubly Linked List thế nào?
- Cache stampede là gì? Cách phòng?
- Redis vs Memcached — điểm khác nhau chính?

---

### 🗄️ Database · 20 phút

**Key points:**
- **SQL**: ACID, schema cứng, JOIN tốt, vertical scale chủ yếu
- **NoSQL**: BASE, schema flexible, horizontal scale, nhiều loại (document/kv/graph/column)
- **Index**: B-tree (range query), Hash (exact match O(1)), Composite (thứ tự cột quan trọng!)
- **Sharding**: horizontal partition data — consistent hashing tránh re-shard khi thêm node
- **Replication**: Master-Slave (read scale) vs Master-Master (write scale, risk conflict)
- **N+1 Problem**: N queries cho N rows → dùng JOIN hoặc eager loading
- **Connection pool**: reuse connection thay vì tạo mới mỗi request

**❓ Cần hiểu rõ:**
- ACID — giải thích từng chữ với ví dụ?
- Transaction, Commit, Rollback là gì?
- B-tree index và Hash index — khi nào dùng cái nào?
- Composite index: thứ tự cột có quan trọng không? Tại sao?
- Consistent hashing là gì? Giải quyết vấn đề gì khi sharding?
- Normalization là gì? Khi nào nên denormalize?
- N+1 problem là gì? Cho ví dụ cụ thể.

---

### 📨 Message Queue · 15 phút

**Key points:**
- **Kafka**: log-based, retain message (replay), high throughput, consumer pull
- **RabbitMQ**: traditional queue, message ack/nack, routing rules, consumer push
- **At-least-once** + **idempotency** = practical choice (exactly-once rất khó)
- **Dead Letter Queue**: message failed nhiều lần → debug
- Bẫy: consumer phải xử lý xong rồi mới ACK, không ACK ngay khi nhận

**❓ Cần hiểu rõ:**
- Tại sao dùng Message Queue thay vì gọi trực tiếp (sync call)?
- At-least-once, At-most-once, Exactly-once — khác nhau thế nào? Cái nào khó nhất?
- Idempotency trong MQ là gì? Tại sao cần?
- Kafka consumer group là gì?
- DLQ là gì? Khi nào message vào DLQ?

---

### 🔌 API Design · 10 phút

**Key points:**
- **REST**: stateless, resource-based URL, HTTP methods có nghĩa
  - GET = safe + idempotent; PUT/DELETE = idempotent; POST = không
- **GraphQL**: client chọn field cần → tránh over-fetch/under-fetch
- **gRPC**: binary (protobuf), bidirectional streaming, internal service-to-service
- **Rate limiting**: Token Bucket (burst OK) vs Leaky Bucket (smooth output)
- **Versioning**: URL `/v1/`, Header, Subdomain

**❓ Cần hiểu rõ:**
- REST 6 constraints là gì?
- Idempotent method là gì? GET safe là gì?
- REST vs GraphQL vs gRPC — khi nào dùng cái nào?
- Over-fetching và Under-fetching là gì?
- Token Bucket vs Leaky Bucket — hoạt động thế nào?
- Idempotency key trong API là gì?

---

### 🧩 Microservices · 10 phút

**Key points:**
- **Circuit Breaker** states: Closed → Open (error rate cao) → Half-Open (thử lại)
- **Saga pattern**: Choreography (event-driven) vs Orchestration (central coordinator)
- **API Gateway**: single entry — auth, rate limit, routing, SSL termination
- **Service Discovery**: Consul/Eureka — service register, client lookup

**❓ Cần hiểu rõ:**
- Microservices vs Monolith — ưu nhược điểm?
- Circuit Breaker 3 states hoạt động thế nào? Tại sao cần?
- Saga Choreography vs Orchestration — trade-off?
- Distributed transaction problem là gì?
- Service mesh là gì? (Istio, Linkerd)

---

### ⚖️ CAP + Consistency · 10 phút

**Key points:**
- **CAP**: khi network partition → chọn CP (MongoDB, ZK) hoặc AP (Cassandra, DynamoDB)
- **ACID** (SQL) vs **BASE** (NoSQL: Basically Available, Soft state, Eventual consistency)
- Isolation levels (yếu → mạnh):
  ```
  Read Uncommitted → Read Committed → Repeatable Read → Serializable
  ```
- **Quorum**: W + R > N → strong consistency (N=3, W=2, R=2 phổ biến)
- **Eventual consistency**: system sẽ nhất quán *cuối cùng* — OK cho social feed, DNS

**❓ Cần hiểu rõ:**
- CAP theorem: tại sao không thể có cả 3 cùng lúc khi có network partition?
- Dirty read, Non-repeatable read, Phantom read là gì? Isolation level nào ngăn cái gì?
- Quorum công thức W + R > N — ví dụ cụ thể N=3, W=2, R=2 hoạt động thế nào?
- Strong consistency vs Eventual consistency — trade-off?

---

## 11:30 · ML Quick · 30 phút
> [📖 Tongquat ML](tongquat_ml.md)

---

### 🤖 Supervised / Unsupervised / Reinforcement · 10 phút

**Key points:**
- **Supervised**: có label → Regression (Linear, Logistic), Classification (SVM, KNN, Tree, Forest)
- **Unsupervised**: tìm structure → K-Means, DBSCAN, PCA
- **Reinforcement**: maximize reward → Q-Learning, PPO
- **Regression**: predict số liên tục; **Classification**: predict class rời rạc

**❓ Cần hiểu rõ:**
- Supervised vs Unsupervised vs Reinforcement — khi nào dùng cái nào?
- Linear Regression: cost function là gì? Gradient Descent tối ưu cái gì?
- Logistic Regression: tại sao cần Sigmoid? Output là gì (bao nhiêu → class 1)?
- Decision Tree: Gini impurity và Information Gain là gì? Split chọn thế nào?
- Random Forest: Bagging là gì? Khác Decision Tree thế nào?
- SVM: hyperplane, support vectors, kernel trick là gì?
- KNN: hoạt động thế nào? Chọn K như thế nào?
- K-Means: thuật toán hoạt động thế nào? Chọn K bằng Elbow method là gì?
- PCA là gì? Dùng để làm gì?

---

### 📉 Bias-Variance & Regularization · 10 phút

**Key points:**
```
Total Error = Bias² + Variance + Irreducible Noise

High Bias   → Underfitting → model quá đơn giản
  Fix: tăng complexity, thêm features, giảm regularization

High Variance → Overfitting → model quá phức tạp
  Fix: thêm data, tăng regularization, dropout, early stopping
```

**❓ Cần hiểu rõ:**
- Bias là gì? Variance là gì? Trade-off là gì?
- Nhận biết underfitting/overfitting qua train/validation loss thế nào?
- L1 (Lasso) vs L2 (Ridge) regularization — cơ chế khác nhau thế nào? L1 tạo sparse model — tại sao?
- Dropout: cơ chế lúc train và lúc inference khác nhau thế nào?
- Cross-validation và K-fold là gì?

---

### 📊 Metrics · 5 phút

**Key points:**

| Metric | Công thức | Khi dùng |
|--------|-----------|---------|
| Accuracy | (TP+TN)/Total | Balanced dataset |
| Precision | TP/(TP+FP) | FP costly (spam filter) |
| Recall | TP/(TP+FN) | FN costly (cancer detection) |
| F1-Score | 2·P·R/(P+R) | Imbalanced dataset |
| ROC-AUC | Area under ROC | So sánh models |
| RMSE | √(Σ(y-ŷ)²/n) | Regression |

**❓ Cần hiểu rõ:**
- Tại sao Accuracy không đủ khi imbalanced dataset?
- Precision và Recall — khi nào ưu tiên cái nào? Cho ví dụ spam vs cancer.
- F1-score là harmonic mean của P và R — tại sao harmonic không phải arithmetic?
- ROC curve là gì? AUC = 1.0 vs AUC = 0.5 nghĩa là gì?

---

### 🧠 Neural Network · 5 phút

**Key points:**
- Activation: **ReLU** (hidden), **Sigmoid** (binary output), **Softmax** (multiclass)
- `w = w - lr * gradient` → SGD, Adam (adaptive learning rate)
- **Backprop** = chain rule từ output ngược về input
- CNN → image; RNN/LSTM → sequence; Transformer → NLP hiện đại
- Vanishing gradient: Sigmoid bão hòa → gradient ≈ 0 → ReLU giải quyết

**❓ Cần hiểu rõ:**
- Tại sao cần activation function? Không có thì thế nào?
- Vanishing gradient problem là gì? ReLU giải quyết thế nào? Dying ReLU là gì?
- Backpropagation: chain rule áp dụng thế nào?
- SGD vs Mini-batch GD vs Adam — trade-off?
- CNN: convolution layer và pooling layer làm gì?
- LSTM giải quyết vấn đề gì của RNN?
- Transformer: self-attention mechanism hoạt động thế nào? Query/Key/Value là gì?
- Transfer learning là gì? Fine-tuning là gì?

---

## 13:00 · AI + Linux · 60 phút
> [📖 Tongquat AI](tongquat_ai.md) | [📖 Tongquat Linux](tongquat_linux.md)

---

### 🤖 AI Quick · 20 phút

**Key points:**
- **Search**: BFS (optimal, unweighted), DFS (all paths), **A*** (heuristic guided), Greedy Best-First
- **NLP pipeline**: tokenize → remove stopwords → vectorize (TF-IDF/Word2Vec) → model
- **Transformer**: self-attention → "query chú ý word nào khác quan trọng nhất" → positional encoding
- **LLM**: next-token prediction; fine-tuning với instruction data; RAG = retrieval + generation

**❓ Cần hiểu rõ:**
- Uninformed search (BFS/DFS) vs Informed search (A*, Greedy Best-First) — khác nhau thế nào?
- A* algorithm là gì? Admissible heuristic là gì?
- TF-IDF: TF là gì, IDF là gì? Tại sao cần IDF?
- Word2Vec khác one-hot encoding thế nào? Word embedding là gì?
- BERT vs GPT — encoder vs decoder approach?
- LLM: pre-training và fine-tuning khác nhau thế nào?
- RAG là gì? Giải quyết vấn đề gì của LLM?
- Hallucination trong LLM là gì?
- Prompt engineering: zero-shot, few-shot, chain-of-thought là gì?

---

### 🐧 Linux Quick · 40 phút

**Key points:**

**Cấu trúc thư mục:**
```
/          — root
/home      — home directories của users
/etc       — config files
/var       — variable data (logs, cache)
/tmp       — temporary files
/usr       — user programs
/bin       — essential binaries
```

**Navigation:**
```bash
ls -la                   # list với permissions + hidden files
find . -name "*.py"      # tìm file theo pattern
find . -type f -size +1M # tìm file > 1MB
pwd                      # print working dir
cd -                     # quay lại dir trước
```

**Text Processing:**
```bash
grep -r "pattern" .          # search recursive
grep -i "pattern" file       # case-insensitive
grep -v "pattern" file       # exclude matching lines
awk '{print $1, $NF}' file   # lấy cột 1 và cột cuối
sed 's/old/new/g' file       # replace all
wc -l / -w / -c file         # đếm lines/words/chars
sort file | uniq -c | sort -rn  # đếm tần suất, sort descending
cut -d',' -f1,3 file         # lấy cột 1 và 3 (delimiter ,)
```

**Process Management:**
```bash
ps aux | grep name       # tìm process theo tên
ps aux | awk '{print $2}' # lấy PIDs
kill PID                 # SIGTERM (graceful)
kill -9 PID              # SIGKILL (force)
pkill -f "process_name"  # kill by name pattern
top / htop               # realtime monitor
nohup cmd > out.log 2>&1 & # chạy background, redirect output
jobs / fg %1 / bg %1     # quản lý background jobs
```

**Permissions:**
```bash
chmod 755 file     # rwxr-xr-x
chmod +x file      # thêm execute permission
chown user:group file
umask 022          # default: files=644, dirs=755
# rwx = 4+2+1 = 7; rw- = 4+2 = 6; r-x = 4+1 = 5
```

**Pipeline & I/O:**
```bash
cmd1 | cmd2          # pipe stdout → stdin
> file               # redirect stdout, overwrite
>> file              # redirect stdout, append
2> error.log         # redirect stderr
2>&1                 # redirect stderr → stdout
cmd &                # run in background
```

**Networking:**
```bash
curl -X GET/POST url
curl -H "Authorization: Bearer token" url
ssh user@host -p port
scp local user@host:/remote/path
netstat -tulnp / ss -tulnp   # xem open ports
ping host
```

**❓ Cần hiểu rõ:**
- `ls -la` — mỗi cột trong output nghĩa là gì?
- Absolute path vs relative path — khác nhau thế nào?
- `grep -r`, `-i`, `-v`, `-n`, `-l` — mỗi flag làm gì?
- `awk '{print $1}'` — $1, $NF, $0 nghĩa là gì?
- `sed 's/old/new/g'` — `g` flag nghĩa là gì? Không có `g` thì sao?
- `chmod 755` — tính từng chữ số như thế nào? rwx = ?
- `umask` là gì? Ảnh hưởng thế nào đến permission file mới tạo?
- `kill` vs `kill -9` — SIGTERM vs SIGKILL khác nhau thế nào?
- `2>&1` — redirect stderr vào stdout, tại sao viết thế?
- Daemon process là gì? Cron job setup thế nào?
- Exit code $? là gì? 0 nghĩa là gì?

---

## 14:00 · Toán (Rời Rạc + Tổ Hợp + Tư Duy) · 60 phút
> [📖 Rời Rạc](tongquat_roi_rac.md) | [📖 Tổ Hợp](tongquat_to_hop.md) | [📖 Tư Duy](tongquat_toan_tu_duy.md)

---

### 📐 Toán Rời Rạc · 20 phút

**Key points — Graph Theory:**
- **Handshaking Lemma**: Σdeg(v) = 2|E| → số đỉnh degree lẻ luôn **chẵn**
- **Euler Circuit**: mọi đỉnh degree **chẵn** + connected
- **Euler Path**: đúng **0 hoặc 2** đỉnh degree lẻ
- **Tree**: |E| = |V| - 1; Kₙ = n(n-1)/2 cạnh; Bipartite = không có odd cycle
- Chromatic number χ: bipartite = 2; complete Kₙ = n

**Key points — Modular Arithmetic:**
```
a ≡ b (mod m)  ↔  m | (a-b)
(a + b) mod m = ((a mod m) + (b mod m)) mod m
Fermat (p nguyên tố, gcd(a,p)=1): a^(p-1) ≡ 1 (mod p)
→ Tính a^b mod p với b lớn: a^(b mod (p-1)) mod p
```

**Key points — Relations:**
- Equivalence: reflexive + symmetric + transitive → tạo equivalence classes
- Partial order: reflexive + antisymmetric + transitive

**❓ Cần hiểu rõ:**
- Handshaking Lemma — chứng minh thế nào? Ứng dụng gì?
- Euler Circuit vs Euler Path — điều kiện chính xác? Hamiltonian khác thế nào?
- Bipartite graph: định nghĩa? Tại sao không có odd cycle?
- Planar graph là gì? Euler's formula `V - E + F = 2` là gì?
- Fermat's Little Theorem — ứng dụng tính `2^1000000 mod 7` thế nào?
- Chinese Remainder Theorem là gì? Dùng khi nào?
- DFA vs NFA — power tương đương không? Convert thế nào?

---

### 🎲 Toán Tổ Hợp · 20 phút

**Key points:**

| Bài toán | Công thức | Ví dụ |
|----------|-----------|-------|
| Sắp xếp n vật khác nhau | n! | 3! = 6 |
| Chọn k từ n, **có thứ tự, không lặp** | P(n,k) = n!/(n-k)! | P(5,2)=20 |
| Chọn k từ n, **không thứ tự, không lặp** | C(n,k) = n!/(k!(n-k)!) | C(5,2)=10 |
| Chọn k từ n, **có thứ tự, có lặp** | n^k | 3^2=9 |
| Chọn k từ n, **không thứ tự, có lặp** | C(n+k-1, k) | C(4+2,2)=15 |
| Sắp xếp có phần tử lặp | n!/(a₁!·a₂!·...) | "AABB": 4!/2!2!=6 |

- **Pascal**: C(n,k) = C(n-1,k-1) + C(n-1,k); tổng hàng n = 2ⁿ
- **Inclusion-Exclusion**: |A∪B| = |A|+|B|-|A∩B|
- **Catalan**: Cₙ = C(2n,n)/(n+1) → BST với n keys, đường không cắt đường chéo

**❓ Cần hiểu rõ:**
- Quy tắc nhân vs Quy tắc cộng — khi nào dùng cái nào?
- Hoán vị có lặp vs Tổ hợp có lặp — cho ví dụ phân biệt.
- Stars and Bars: phân phối n đồng vào k hộp (có thể rỗng) — công thức?
- Catalan number xuất hiện trong bài toán nào? (đường lưới, BST, ngoặc)
- Inclusion-Exclusion với 3 tập — công thức đầy đủ?
- Sắp xếp "MISSISSIPPI" — áp dụng công thức nào?

---

### 🧠 Toán Tư Duy · 20 phút

**Key points — Logic:**
- P→Q chỉ FALSE khi P=True, Q=False
- **Contrapositive** ¬Q→¬P ≡ P→Q (tương đương)
- **Converse** Q→P ≠ P→Q
- **Modus Ponens**: (P→Q, P) → Q
- **Modus Tollens**: (P→Q, ¬Q) → ¬P

**Key points — Proof techniques:**
- **Induction**: Base case P(1) + Inductive step [P(k) → P(k+1)]
- **Contradiction**: Giả sử ¬P → dẫn đến mâu thuẫn → P đúng

**Key points — Bit manipulation:**

| Phép tính | Code | Ứng dụng |
|-----------|------|---------|
| Check bit i | `n & (1 << i)` | |
| Set bit i | `n \| (1 << i)` | |
| Clear bit i | `n & ~(1 << i)` | |
| Toggle bit i | `n ^ (1 << i)` | |
| Check chẵn/lẻ | `n & 1` | |
| Xóa rightmost 1-bit | `n & (n-1)` | đếm số bit 1 |
| Enumerate subsets | bitmask `0..2ⁿ-1` | |
| a XOR a = 0 | `a ^ a` | tìm số xuất hiện 1 lần |

**❓ Cần hiểu rõ:**
- Bảng chân trị của P→Q, P↔Q — điền đầy đủ?
- Contrapositive vs Converse vs Inverse — cái nào tương đương P→Q?
- Proof by Induction: strong induction là gì? Khi nào cần?
- Pigeonhole Principle phát biểu gì? Cho ví dụ ứng dụng.
- `n & (n-1)` xóa rightmost 1-bit — tại sao?
- XOR trick: tìm số xuất hiện 1 lần trong array có các số xuất hiện 2 lần — giải thế nào?
- Bitmask để enumerate tất cả subsets của n phần tử — viết code thế nào?

---

## 15:00 · Review Điểm Yếu · 120 phút

```
Thứ tự ưu tiên:
1. Làm qua phần "❓ Cần hiểu rõ" của các topic chưa trả lời được
2. Xem lại complexity table bên dưới — nhớ chưa? Có nhầm không?
3. Ôn lại các "Bảng so sánh A vs B" ở cuối file
4. Mở tongquat tương ứng đọc "Câu hỏi tự test nhanh" nếu còn thời gian
```

---

## ═══════════════════════════════════════
## QUICK REFERENCE
## ═══════════════════════════════════════

### Complexity bắt buộc nhớ

```
  Cấu trúc dữ liệu        Access    Search    Insert    Delete    Space
  ──────────────────────────────────────────────────────────────────────
  Array                    O(1)      O(n)      O(n)      O(n)      O(n)
  Linked List              O(n)      O(n)      O(1)*     O(1)*     O(n)
  HashMap                  —         O(1)**    O(1)**    O(1)**    O(n)
  Stack / Queue            —         O(n)      O(1)      O(1)      O(n)
  BST (balanced)           —         O(log n)  O(log n)  O(log n)  O(n)
  Heap                     O(1)***   O(n)      O(log n)  O(log n)  O(n)
  Trie                     O(L)      O(L)      O(L)      O(L)      O(n·L)

  * với known pointer   ** average case   *** chỉ min/max

  Thuật toán              Best        Average      Worst       Space
  ─────────────────────────────────────────────────────────────────────
  Quick Sort              O(n log n)  O(n log n)   O(n²)       O(log n)
  Merge Sort              O(n log n)  O(n log n)   O(n log n)  O(n)
  Heap Sort               O(n log n)  O(n log n)   O(n log n)  O(1)
  Binary Search           O(1)        O(log n)     O(log n)    O(1)
  BFS / DFS               —           O(V+E)       O(V+E)      O(V)
  Dijkstra                —           O((V+E)logV) —           O(V)
  Topological Sort        —           O(V+E)       O(V+E)      O(V)
```

---

### Ports cần nhớ

```
  22=SSH   25=SMTP   53=DNS   80=HTTP   443=HTTPS
  3306=MySQL   5432=PostgreSQL   6379=Redis   27017=MongoDB
```

---

### HTTP Status hay bị hỏi

```
  200 OK              201 Created         204 No Content
  301 Moved Perm.     302 Found (temp)    304 Not Modified
  400 Bad Request     401 Unauthorized    403 Forbidden     404 Not Found
  429 Rate Limit      500 Server Error    503 Unavailable
```

---

### Bảng so sánh A vs B — hay bị hỏi nhất

**DSA:**
- Array vs Linked List — access O(1) vs O(n), insert đầu O(n) vs O(1)
- Stack vs Queue — LIFO vs FIFO, DFS vs BFS
- BFS vs DFS — shortest path vs all paths
- Greedy vs DP — greedy choice property vs overlapping subproblems
- Quick Sort vs Merge Sort — in-place/unstable/O(n²) worst vs O(n) space/stable
- HashMap vs BST — O(1) avg vs O(log n) ordered

**OOP:**
- Abstract Class vs Interface — IS-A/state/code vs CAN-DO/no-state/contract
- Composition vs Inheritance — has-a/loose coupling vs is-a/tight coupling
- Override vs Overload — runtime/same signature vs compile-time/different params
- `@classmethod` vs `@staticmethod` — cls param vs no class access

**OS:**
- Process vs Thread — isolated memory vs shared memory
- Mutex vs Semaphore — binary/owner vs counter/no-owner
- Deadlock vs Livelock vs Starvation — blocked vs busy-no-progress vs never-scheduled
- Hard link vs Soft link — share inode vs path pointer

**Network:**
- TCP vs UDP — reliable/ordered vs fast/no-guarantee
- HTTP/1.1 vs HTTP/2 vs HTTP/3 — HoL blocking vs multiplexing vs QUIC
- 401 vs 403 — chưa authenticate vs đã auth, không có quyền
- Cookie vs Session vs JWT — server-stored vs client token

**System Design:**
- SQL vs NoSQL — ACID/schema vs BASE/flexible
- Cache-aside vs Write-through — lazy/stale risk vs consistent/slow write
- Kafka vs RabbitMQ — log-based/replay vs traditional/routing
- REST vs GraphQL vs gRPC — resource-based vs client-driven vs binary/streaming
- Vertical vs Horizontal scaling — hardware limit/SPOF vs stateless required
- CP vs AP (CAP) — consistency vs availability when partition

**ML:**
- Supervised vs Unsupervised — có label vs không label
- Precision vs Recall — FP vs FN costly
- L1 vs L2 regularization — sparse/absolute vs smooth/squared
- CNN vs RNN vs Transformer — image vs sequence vs attention-based

**Backend:**
- Session vs JWT — server-side state vs stateless token
- OAuth 2.0 vs OpenID Connect — authorization vs authentication
- Monolith vs Microservices vs Serverless
- Sync vs Async communication

**Database:**
- INNER JOIN vs LEFT JOIN vs FULL JOIN
- WHERE vs HAVING — trước vs sau GROUP BY
- Index vs No index — fast read vs fast write
- Covering index vs Partial index

**API:**
- REST vs GraphQL vs gRPC vs WebSocket — khi nào dùng cái nào
- Pagination: Offset vs Cursor-based
- API Key vs Bearer Token vs OAuth 2.0
- Rate limiting: Token Bucket vs Leaky Bucket vs Sliding Window

---

# ═══════════════════════════════════════
# BUỔI TỐI NGÀY 2 — Nửa ngày bổ sung
# ═══════════════════════════════════════

---

## 17:30 · Backend · 60 phút

---

### 🌐 HTTP Request Lifecycle · 10 phút

**Key points:**
```
Browser gõ URL → DNS resolve → TCP connect (3-way handshake)
→ TLS handshake (HTTPS) → HTTP request gửi lên server
→ Server xử lý: middleware → auth → business logic → DB query
→ DB trả kết quả → server tạo response → gửi về browser
→ Browser render HTML/CSS/JS
```

**❓ Cần hiểu rõ:**
- "What happens when you type google.com?" — liệt kê đủ các bước?
- Web server (Nginx) và App server (Gunicorn/Node) — phân công như thế nào?
- Middleware là gì? Request pipeline hoạt động thế nào?
- Synchronous vs Asynchronous processing — khác nhau ở đâu?

---

### 🔐 Authentication & Authorization · 25 phút

**Key points:**
- **Authentication** = "Mày là ai?" → xác minh danh tính
- **Authorization** = "Mày được làm gì?" → kiểm tra quyền

**Session-based auth:**
```
Login → Server tạo session_id → lưu vào store (Redis/DB)
→ Gửi session_id về client qua Cookie
→ Request sau: client gửi cookie → server lookup session
```

**JWT (JSON Web Token):**
```
Format: header.payload.signature  (Base64 encoded)
header:    {"alg": "HS256", "typ": "JWT"}
payload:   {"user_id": 1, "exp": 1234567890, "role": "admin"}
signature: HMAC-SHA256(header + "." + payload, secret)

Login → server ký JWT → gửi về client (localStorage / Cookie)
→ Request sau: client gửi trong Authorization: Bearer <token>
→ Server verify signature — KHÔNG cần lookup DB → stateless
```

**JWT vs Session:**

| | Session | JWT |
|-|---------|-----|
| State | Server-side | Stateless (client-side) |
| Revoke | Dễ (xóa session) | Khó (cần blacklist) |
| Scale | Cần sticky session hoặc shared store | Scale dễ hơn |
| Size | Nhỏ (chỉ ID) | Lớn hơn (chứa payload) |

**OAuth 2.0:**
```
Authorization framework — cho phép app A truy cập data của user ở app B
KHÔNG phải authentication!

Flows:
- Authorization Code: cho web app (secure, có server-side)
- Client Credentials: service-to-service (không có user)
- Implicit: SPA (deprecated, không dùng nữa)
- Device Code: TV, CLI tool

Roles: Resource Owner, Client, Authorization Server, Resource Server
```

**OpenID Connect (OIDC):**
- Lớp authentication chạy trên OAuth 2.0
- Trả về **ID Token** (JWT) chứa thông tin user
- OAuth 2.0 cho authorization; OIDC cho authentication

**❓ Cần hiểu rõ:**
- Authentication vs Authorization — phân biệt rõ ràng với ví dụ?
- JWT signature verify thế nào? Nếu secret bị lộ thì sao?
- Tại sao JWT khó revoke? Giải pháp nào?
- OAuth 2.0 Authorization Code flow diễn ra thế nào? (từng bước)
- OIDC vs OAuth 2.0 — cái nào dùng để "Login with Google"?
- Refresh token là gì? Access token vs Refresh token khác nhau thế nào?
- Tại sao không nên lưu JWT trong localStorage? Cookie HttpOnly là gì?

---

### 🛡️ Security Practices · 15 phút

**Key points:**
- **Input validation**: validate ở server, không chỉ ở client
- **SQL Injection**: dùng prepared statement / parameterized query
- **XSS**: escape output, Content-Security-Policy header
- **CSRF**: CSRF token hoặc SameSite cookie attribute
- **HTTPS**: always; HSTS header để force HTTPS
- **Secrets management**: env variables, không hardcode trong code
- **Password hashing**: bcrypt/Argon2, không dùng MD5/SHA1 cho password

**OWASP Top 10 tóm tắt:**

| # | Tên | Ví dụ |
|---|-----|-------|
| 1 | Broken Access Control | Truy cập resource của user khác bằng cách đổi ID |
| 2 | Cryptographic Failures | Lưu password plain-text, dùng HTTP |
| 3 | Injection | SQL injection, Command injection |
| 4 | Insecure Design | Thiếu rate limiting, không throttle login |
| 5 | Security Misconfiguration | Default credentials, stack trace lộ ra |
| 6 | Vulnerable Components | Dùng thư viện cũ có CVE |
| 7 | Auth Failures | Brute force, weak session |
| 8 | Integrity Failures | Deserialization không an toàn |
| 9 | Logging Failures | Không log security events |
| 10 | SSRF | Server bị trick gọi internal service |

**Key points — Logging & Monitoring:**
- **Structured logging**: log dạng JSON, có traceId, userId, timestamp
- **Distributed tracing**: theo dõi request qua nhiều service (Jaeger, Zipkin)
- **Metrics**: latency (p50/p95/p99), error rate, throughput
- **Alerting**: set threshold → alert khi vượt ngưỡng

**❓ Cần hiểu rõ:**
- Prepared statement ngăn SQL injection thế nào?
- bcrypt khác SHA256 thế nào khi hash password? Tại sao bcrypt tốt hơn?
- HSTS là gì? Tại sao cần ngoài HTTPS?
- Tại sao không log sensitive data (password, token) trong logs?
- p95 latency là gì? Tại sao quan trọng hơn average?

---

### ⚙️ Backend Patterns · 10 phút

**Key points:**
- **Environment variables**: config theo môi trường (dev/staging/prod) — không hardcode
- **Health check endpoint**: `GET /health` → LB dùng để route traffic
- **Graceful shutdown**: finish in-flight requests trước khi tắt
- **Idempotency**: cùng request gọi nhiều lần → cùng kết quả (dùng idempotency key)
- **Retry with exponential backoff**: retry 1s → 2s → 4s → 8s... + jitter để tránh thundering herd
- **Timeout**: mọi external call (DB, API) phải có timeout — không để hang vô hạn

**❓ Cần hiểu rõ:**
- 12-factor app là gì? Những factor nào quan trọng nhất?
- Graceful shutdown tại sao cần? Không có thì sao?
- Exponential backoff với jitter là gì? Tại sao cần jitter?
- Circuit breaker vs Retry — khi nào dùng cái nào?
- Feature flag là gì? Dùng để làm gì?

---

## 18:30 · Database Advanced · 60 phút

---

### 🔍 SQL Nâng cao · 25 phút

**Key points — JOINs:**
```sql
-- INNER JOIN: chỉ rows có match ở cả 2 bảng
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN: tất cả rows bên trái + match bên phải (NULL nếu không match)
SELECT u.name, o.total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
-- → Lấy tất cả users, kể cả user chưa có order nào

-- FULL OUTER JOIN: tất cả rows từ cả 2 bảng
-- RIGHT JOIN: ngược với LEFT JOIN (ít dùng)
```

**Key points — GROUP BY & HAVING:**
```sql
-- WHERE: lọc trước khi group (filter rows)
-- HAVING: lọc sau khi group (filter groups)
SELECT user_id, COUNT(*) as order_count
FROM orders
WHERE status = 'completed'       -- lọc trước group
GROUP BY user_id
HAVING COUNT(*) > 5;             -- lọc sau group
```

**Key points — Window Functions:**
```sql
-- ROW_NUMBER(): đánh số thứ tự trong partition
-- RANK(): rank có gap khi tie
-- DENSE_RANK(): rank không có gap
-- LAG(col, n): giá trị n rows trước
-- LEAD(col, n): giá trị n rows sau

SELECT name, salary,
  RANK() OVER (PARTITION BY dept ORDER BY salary DESC) as rank
FROM employees;
```

**Key points — CTE vs Subquery:**
```sql
-- CTE (Common Table Expression) — dễ đọc hơn, có thể recursive
WITH high_value_users AS (
  SELECT user_id FROM orders GROUP BY user_id HAVING SUM(total) > 1000
)
SELECT u.name FROM users u JOIN high_value_users h ON u.id = h.user_id;
```

**Key points — EXPLAIN:**
```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 5;
-- Xem: Seq Scan (chậm, không dùng index) vs Index Scan (nhanh)
-- Rows, Cost, Actual time
```

**❓ Cần hiểu rõ:**
- INNER JOIN vs LEFT JOIN vs FULL JOIN — kết quả khác nhau thế nào?
- WHERE vs HAVING — tại sao không thể dùng WHERE sau GROUP BY?
- Window function khác GROUP BY thế nào? (GROUP BY collapse rows, window function không)
- CTE vs Subquery — khi nào dùng CTE?
- `EXPLAIN` output đọc thế nào? Seq Scan khi nào? Index Scan khi nào?
- N+1 problem trong SQL là gì? Viết query tránh N+1 thế nào?

---

### 📇 Indexing Strategy · 15 phút

**Key points:**
```
Khi nào tạo index:
✅ Column hay xuất hiện trong WHERE, JOIN ON, ORDER BY
✅ Foreign key columns
✅ Column có high cardinality (nhiều giá trị unique)

Khi nào KHÔNG tạo index:
❌ Table nhỏ (< vài nghìn rows) — full scan nhanh hơn
❌ Column có low cardinality (gender, status) — ít selective
❌ Table bị write nhiều — index làm chậm INSERT/UPDATE/DELETE
```

**Types của Index:**

| Loại | Dùng khi | Ví dụ |
|------|---------|-------|
| B-tree (default) | Range query, ORDER BY | `WHERE age > 18` |
| Hash | Exact match only | `WHERE email = ?` |
| Composite | Multi-column filter | `WHERE status='A' AND date > ?` |
| Covering | Query chỉ cần index columns | Tránh table lookup |
| Partial | Index subset của rows | `WHERE deleted_at IS NULL` |
| Full-text | Text search | `WHERE MATCH(content)` |

**Composite index rule:**
```sql
-- Index (a, b, c) có thể dùng cho:
-- WHERE a = ?                    ✅
-- WHERE a = ? AND b = ?          ✅
-- WHERE a = ? AND b = ? AND c = ?✅
-- WHERE b = ?                    ❌ (phải bắt đầu từ leftmost column)
-- WHERE a = ? AND c = ?          ✅ chỉ dùng phần a
```

**❓ Cần hiểu rõ:**
- Index hoạt động thế nào bên trong (B-tree structure)?
- Tại sao index làm chậm INSERT/UPDATE/DELETE?
- Covering index là gì? Lợi ích gì?
- Composite index "leftmost prefix rule" là gì?
- Index selectivity là gì? Tại sao index trên `gender` không hiệu quả?
- Khi nào query optimizer KHÔNG dùng index dù đã tạo?

---

### 🔄 Transactions & Locking · 10 phút

**Key points:**
```
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT; -- hoặc ROLLBACK nếu có lỗi
```

- **Optimistic locking**: không lock khi read, check version khi write → ít conflict thì dùng
- **Pessimistic locking**: lock ngay khi read (`SELECT ... FOR UPDATE`) → nhiều conflict thì dùng
- **Deadlock trong DB**: 2 transaction chờ nhau → DB tự detect và rollback 1 cái

**Isolation levels vs vấn đề:**

| Level | Dirty Read | Non-repeatable Read | Phantom Read |
|-------|-----------|---------------------|--------------|
| Read Uncommitted | ✅ có | ✅ có | ✅ có |
| Read Committed | ❌ không | ✅ có | ✅ có |
| Repeatable Read | ❌ không | ❌ không | ✅ có |
| Serializable | ❌ không | ❌ không | ❌ không |

**❓ Cần hiểu rõ:**
- Dirty read, Non-repeatable read, Phantom read — định nghĩa từng cái với ví dụ?
- Optimistic vs Pessimistic locking — khi nào dùng cái nào?
- `SELECT ... FOR UPDATE` làm gì?
- Deadlock trong DB xảy ra thế nào? DB xử lý thế nào?
- Tại sao nên giữ transaction ngắn nhất có thể?

---

### 🗃️ Redis & NoSQL Patterns · 10 phút

**Key points — Redis data structures:**

| Structure | Commands | Use case |
|-----------|----------|---------|
| String | GET, SET, INCR, EXPIRE | Cache, counter, rate limit |
| Hash | HGET, HSET, HMGET | User session, object cache |
| List | LPUSH, RPOP, LRANGE | Queue, recent items |
| Set | SADD, SMEMBERS, SISMEMBER | Unique visitors, tags |
| Sorted Set | ZADD, ZRANGE, ZRANGEBYSCORE | Leaderboard, timeline |

**Key points — Redis patterns:**
```
Cache: SET key value EX 3600  (expire in 1 hour)
Rate limiting: INCR user:123:hits → nếu > limit: block
                EXPIRE user:123:hits 60  (reset sau 60s)
Session store: HSET session:abc user_id 1 expires 1234567890
Pub/Sub: PUBLISH channel message → SUBSCRIBE channel nhận
Leaderboard: ZADD scores 100 "alice" → ZREVRANK scores "alice"
```

**Key points — NoSQL chọn loại nào:**

| Loại | Database | Dùng khi |
|------|----------|---------|
| Document | MongoDB | Flexible schema, nested data, content management |
| Key-Value | Redis, DynamoDB | Cache, session, simple lookup |
| Column-family | Cassandra | Write-heavy, time-series, IoT |
| Graph | Neo4j | Social network, recommendation, fraud detection |

**❓ Cần hiểu rõ:**
- Redis Sorted Set dùng cho leaderboard thế nào? Complexity của ZADD, ZRANGE?
- Redis Pub/Sub vs Message Queue (Kafka/RabbitMQ) — khác nhau thế nào?
- MongoDB embedding vs referencing — khi nào dùng cái nào?
- Cassandra partition key và clustering key là gì? Tại sao quan trọng?
- Khi nào dùng Redis thay Memcached?

---

## 19:30 · API Advanced · 60 phút

---

### 📐 REST API Best Practices · 20 phút

**Key points — URL Design:**
```
✅ Tốt:
  GET    /users              → list users
  GET    /users/123          → get user 123
  POST   /users              → create user
  PUT    /users/123          → update toàn bộ user 123
  PATCH  /users/123          → partial update user 123
  DELETE /users/123          → delete user 123
  GET    /users/123/orders   → orders của user 123

❌ Xấu:
  GET  /getUsers
  POST /createUser
  GET  /users/getById?id=123
```

**Key points — Pagination:**
```
Offset-based (simple nhưng có vấn đề):
  GET /users?page=2&limit=10
  → OFFSET 10 LIMIT 10 trong SQL
  Vấn đề: record mới insert → data shift → skip/duplicate

Cursor-based (tốt hơn cho data thay đổi):
  GET /users?cursor=eyJ1c2VyX2lkIjoxMDB9&limit=10
  cursor = base64(last_seen_id)
  → WHERE id > last_seen_id LIMIT 10
  Tốt cho: infinite scroll, realtime feeds
```

**Key points — Response format:**
```json
{
  "data": { ... },        // actual payload
  "meta": {               // pagination, counts
    "total": 100,
    "page": 2,
    "limit": 10
  },
  "error": null           // null nếu thành công
}

Lỗi:
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is invalid",
    "details": [{"field": "email", "message": "..."}]
  }
}
```

**❓ Cần hiểu rõ:**
- Tại sao dùng noun không phải verb trong URL?
- PUT vs PATCH — khác nhau thế nào? Khi nào dùng cái nào?
- Offset pagination vấn đề gì? Cursor-based giải quyết thế nào?
- Consistent error response format tại sao quan trọng?
- HATEOAS là gì? REST level 3 là gì?

---

### 🔑 API Authentication & Security · 15 phút

**Key points — Authentication methods:**

| Method | Dùng khi | Cách hoạt động |
|--------|---------|---------------|
| API Key | Public API, server-to-server | Header: `X-API-Key: abc123` |
| Bearer Token (JWT) | User-facing API | Header: `Authorization: Bearer <token>` |
| OAuth 2.0 | 3rd party access | Flow phức tạp hơn |
| Basic Auth | Internal tools | `Authorization: Basic base64(user:pass)` |
| mTLS | Service-to-service | Client cert + Server cert |

**Key points — Rate Limiting implementation:**
```
Token Bucket (recommended):
  Mỗi user có bucket với max B tokens
  Tokens được thêm vào với rate R tokens/second
  Mỗi request consume 1 token
  Nếu bucket trống → 429 Too Many Requests
  Cho phép burst (dùng hết bucket cùng lúc)

Sliding Window Log:
  Lưu timestamp của mỗi request
  Xóa timestamps cũ hơn window
  Nếu count > limit → reject
  Tốn bộ nhớ nhưng chính xác

Redis implementation (Token Bucket đơn giản):
  INCR user:123:requests → if > 100: return 429
  EXPIRE user:123:requests 60  (reset sau 60 giây)
```

**❓ Cần hiểu rõ:**
- Tại sao không dùng Basic Auth cho production API?
- API Key khác Bearer Token thế nào? Khi nào dùng cái nào?
- Rate limit headers cần trả về gì? (X-RateLimit-Limit, X-RateLimit-Remaining, Retry-After)
- Tại sao Rate Limiting nên ở API Gateway, không phải từng service?

---

### 🔄 Real-time & Advanced Patterns · 15 phút

**Key points — WebSocket vs alternatives:**

| | WebSocket | Server-Sent Events (SSE) | Long Polling | HTTP/2 Push |
|-|-----------|--------------------------|-------------|-------------|
| Direction | Bidirectional | Server → Client only | Bidirectional | Server → Client |
| Protocol | WS (upgrade từ HTTP) | HTTP | HTTP | HTTP/2 |
| Dùng khi | Chat, gaming, collab | Notifications, feed | Legacy fallback | Realtime updates |

**Key points — GraphQL:**
```
Schema:
  type User { id: ID!, name: String!, posts: [Post!]! }
  type Post { id: ID!, title: String!, author: User! }

Query (đọc):
  query { user(id: "1") { name, posts { title } } }
  → Chỉ trả về name và post titles, không có fields thừa

Mutation (write):
  mutation { createPost(title: "Hello") { id, title } }

Subscription (realtime):
  subscription { messageAdded { content, author { name } } }

Vấn đề N+1 trong GraphQL:
  Query user.posts → cho mỗi post lại query author → N+1
  Giải pháp: DataLoader (batch + cache queries trong 1 tick)
```

**Key points — API Documentation:**
```yaml
# OpenAPI 3.0 (Swagger)
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found
```

**❓ Cần hiểu rõ:**
- WebSocket connection lifecycle: upgrade từ HTTP thế nào?
- SSE vs WebSocket — khi nào dùng SSE thay WebSocket?
- GraphQL N+1 problem — DataLoader giải quyết thế nào?
- OpenAPI specification dùng để làm gì? Swagger UI là gì?
- gRPC streaming (unary, server-streaming, client-streaming, bidirectional)?

---

### 🔖 API Versioning & Testing · 10 phút

**Key points — Versioning:**
```
URL versioning:          /api/v1/users  (phổ biến nhất, dễ nhìn)
Header versioning:       Accept: application/vnd.api+json;version=1
Query param:             /users?version=1
Subdomain:               v1.api.example.com
```

**Key points — API Testing:**
```
Unit test: test logic của handler function (mock DB)
Integration test: test API endpoint với real DB (test container)
Contract test: đảm bảo provider và consumer đồng ý về API spec (Pact)
E2E test: test flow đầy đủ (Postman/Newman, Playwright)
Load test: test performance dưới tải (k6, JMeter, Locust)
```

**❓ Cần hiểu rõ:**
- Tại sao URL versioning phổ biến nhất dù vi phạm REST principles?
- Contract testing là gì? Khác integration testing thế nào?
- Mock vs Stub vs Fake trong testing — phân biệt?
- Load test vs Stress test vs Soak test — khác nhau thế nào?

---

## 20:30 · System Design — Tình huống thực tế · 60 phút

> Với mỗi bài, học thuộc: **Requirements → Estimation → Core components → Scale → Edge cases**

---

### 🔗 Design URL Shortener (TinyURL) · 10 phút

**Functional requirements:**
- Tạo short URL từ long URL
- Redirect short URL → long URL
- Custom alias (tùy chọn), expiry

**Estimation:**
- 100M URLs/day write → ~1200 writes/s
- Read:Write ratio = 10:1 → 12000 reads/s
- Storage: 100M * 500 bytes = 50GB/day

**Core design:**
```
1. Encode: dùng Base62 (a-zA-Z0-9) → 6 ký tự = 62^6 ≈ 57 tỷ URLs
   Cách tạo ID: counter (simple, predictable) hoặc UUID (random, unpredictable)

2. Storage: SQL hoặc KV store (Redis for cache + DB for persistence)
   Table: short_url | long_url | created_at | expires_at | user_id

3. Flow:
   POST /shorten → generate ID → store (short, long) → return short URL
   GET /{shortCode} → lookup → 301/302 redirect

4. Cache: Redis cache cho hot URLs (top 20% URLs = 80% traffic)
```

**Scale:**
- Read-heavy: CDN + Redis cache ở edge
- Write: multiple servers + DB replication
- Custom domain: DNS wildcard record

**❓ Cần hiểu rõ:**
- 301 vs 302 redirect — URL shortener nên dùng cái nào? Trade-off?
- Counter-based ID dễ bị enumerate — giải pháp?
- Nếu user gửi cùng 1 long URL 2 lần → tạo 2 short URL hay return cùng 1?

---

### 🐦 Design Social Feed (Twitter-like) · 15 phút

**Core challenge:** Fanout — khi Alice post tweet, phân phối đến N followers

**Fan-out on Write (Push):**
```
Khi Alice post:
  → lookup tất cả followers
  → write tweet vào timeline feed của từng follower
  
READ: chỉ cần đọc feed của user → rất nhanh
WRITE: chậm nếu user có nhiều followers (celebrity với 10M followers)
```

**Fan-out on Read (Pull):**
```
Khi Bob đọc feed:
  → lookup tất cả people Bob follows
  → fetch recent tweets từ mỗi người
  → merge + sort
  
WRITE: nhanh
READ: chậm nếu follow nhiều người
```

**Hybrid approach (Twitter dùng):**
```
- User thường (< X followers): Fan-out on Write
- Celebrity (> X followers): Fan-out on Read
- Khi read: merge pre-computed feed + fetch celebrity tweets
```

**Storage:**
- Tweets: Cassandra (write-heavy, time-series)
- User graph (follow): Graph DB hoặc Redis Set
- Timeline cache: Redis List (store tweet_ids)
- Media: S3/CDN

**❓ Cần hiểu rõ:**
- Tại sao Celebrity không dùng fan-out on write?
- Timeline chỉ lưu tweet_ids không lưu tweet content — tại sao?
- Trending topics detect thế nào? (sliding window counter)
- Search tweets: Elasticsearch — tại sao không dùng SQL?

---

### 💬 Design Chat App (WhatsApp-like) · 10 phút

**Core challenge:** Realtime message delivery

**Architecture:**
```
WebSocket Server (stateful):
  - Mỗi user connect WebSocket tới 1 server
  - Server giữ map: user_id → WebSocket connection

Message flow:
  Alice gửi message cho Bob:
  1. Alice → WebSocket server A
  2. Server A lookup: Bob đang connect server B
  3. Server A → Message Queue → Server B
  4. Server B → Bob (nếu online)
  5. Nếu Bob offline: lưu message vào DB, push notification

Service Discovery: Redis/ZooKeeper lưu user_id → server_id mapping
```

**Storage:**
- Messages: Cassandra — partition by conversation_id, sort by timestamp
- User metadata: MySQL/PostgreSQL
- Media: S3 với pre-signed URL

**Online Presence:**
```
Heartbeat: client ping server mỗi 5-10s → update Redis TTL
Nếu TTL expire → user offline
```

**❓ Cần hiểu rõ:**
- Tại sao cần Message Queue giữa WebSocket servers?
- End-to-end encryption: key exchange thế nào? Server có đọc được không?
- Group chat fan-out → tối ưu thế nào cho group lớn (1000+ members)?
- Read receipts (seen/delivered) implement thế nào?

---

### ⏱️ Design Rate Limiter · 10 phút

**Requirements:**
- Limit: 100 requests/user/minute
- Distributed (nhiều servers)
- Low latency overhead

**Algorithms:**

| Algorithm | Pros | Cons |
|-----------|------|------|
| Fixed Window | Simple | Burst ở boundary (100 req cuối phút 1 + 100 req đầu phút 2 = 200 req/2s) |
| Sliding Window Log | Accurate | Memory: lưu timestamp mỗi request |
| Sliding Window Counter | Balance | Approximate |
| Token Bucket | Bursty traffic OK | Complex |
| Leaky Bucket | Smooth output | No burst |

**Distributed implementation với Redis:**
```python
# Token Bucket với Redis
def is_allowed(user_id):
    key = f"rate:{user_id}"
    pipe = redis.pipeline()
    now = time.time()
    pipe.zadd(key, {now: now})           # Sliding Window Log
    pipe.zremrangebyscore(key, 0, now - 60)  # remove old
    pipe.zcard(key)                       # count in window
    pipe.expire(key, 60)
    _, _, count, _ = pipe.execute()
    return count <= 100
```

**Where to implement:**
- API Gateway: centralized, consistent
- Middleware: per-service flexibility
- Không implement ở client: bypassable

**❓ Cần hiểu rõ:**
- Fixed window boundary problem là gì? Cho ví dụ cụ thể?
- Token Bucket vs Leaky Bucket — khi nào dùng cái nào?
- Distributed rate limiter: tại sao Redis Lua script tốt hơn multiple commands?
- Rate limit per IP vs per User vs per API key — khi nào dùng cái nào?

---

### 📺 Design Video Streaming (YouTube-like) · 15 phút

**Core challenge:** Store, process, deliver video efficiently

**Upload flow:**
```
User upload → Chunked upload → Object Storage (S3)
→ Message Queue → Video Processing Workers:
  - Transcode to multiple formats (360p, 720p, 1080p, 4K)
  - Extract thumbnail
  - Generate subtitles (ML)
→ Store metadata in DB
→ Notify user: video ready
→ CDN pre-warm popular videos
```

**Streaming flow:**
```
User click video:
1. Client → API server → get video metadata + CDN URL
2. Client → CDN: request video chunk
3. Adaptive bitrate streaming (HLS/DASH):
   - Client monitor bandwidth
   - Request higher quality khi bandwidth tốt
   - Request lower quality khi bandwidth kém
4. CDN edge server serve chunks gần nhất
```

**Storage:**
- Video files: Object storage (S3) + CDN
- Metadata: SQL (video_id, title, user_id, view_count)
- Comments: Cassandra
- View count: Redis counter → async batch write to DB

**Scale:**
- Hot videos: CDN cache
- Long tail videos: on-demand transcode khi được request
- Geographic: CDN PoPs gần users

**❓ Cần hiểu rõ:**
- Tại sao cần transcode nhiều resolution? Adaptive bitrate streaming là gì?
- Chunked upload giải quyết vấn đề gì so với upload 1 file lớn?
- View count tại sao dùng Redis thay vì trực tiếp UPDATE DB?
- CDN edge vs CDN origin — khi nào CDN miss, request về origin?
- Tại sao video comment không dùng SQL mà dùng Cassandra?

---

## 21:30 · Mock Test · 30 phút

> **Luật**: không xem đáp án trước. Tự trả lời rồi mới check.

---

### 🧪 Phần 1 — Output Code (5 phút)

**Câu 1:**
```python
class Base:
    x = 0
    def __init__(self):
        Base.x += 1
        self.id = Base.x

a = Base()
b = Base()
c = Base()
print(Base.x, a.id, b.id, c.id)
```
> **Đáp án:** `3 1 2 3`

**Câu 2:**
```python
def foo(n, memo={}):
    if n <= 1: return n
    if n in memo: return memo[n]
    memo[n] = foo(n-1, memo) + foo(n-2, memo)
    return memo[n]

print(foo(5))
print(len(foo.__defaults__[0]))  # số items trong memo
```
> **Đáp án:** `5` rồi `4` (memo chứa {2:1, 3:2, 4:3, 5:5})

**Câu 3:**
```python
from collections import deque

stack = []
queue = deque()
for x in [3, 1, 4, 1, 5]:
    stack.append(x)
    queue.append(x)

print(stack.pop(), queue.popleft())
```
> **Đáp án:** `5 3` (stack: LIFO lấy 5; queue: FIFO lấy 3)

---

### 🧪 Phần 2 — Câu hỏi nhanh (10 phút)

**Q: Gõ `www.google.com` → liệt kê 6 bước chính xảy ra:**
> DNS resolve → TCP 3-way handshake → TLS handshake → HTTP GET request → Server xử lý → Response + browser render

**Q: Database có 1 triệu users. Query `WHERE email = ?` không dùng index thì O(?) — có index thì O(?)**
> Không index: O(n) = O(1,000,000); Có index B-tree: O(log n) ≈ O(20)

**Q: JWT payload lưu `{"user_id": 1, "role": "admin"}` — user có thể sửa payload không?**
> Có thể sửa (Base64 là reversible), nhưng signature sẽ invalid → server reject → KHÔNG được. Nhưng phải verify signature!

**Q: POST `/users` trả 200 OK hay 201 Created?**
> 201 Created — resource mới được tạo thành công

**Q: `SELECT * FROM users WHERE status = 'active' ORDER BY created_at DESC LIMIT 10` — index nên đặt ở cột nào?**
> Composite index: `(status, created_at DESC)` — filter status trước, sort created_at sau

**Q: Redis dùng để làm gì khi implement rate limiting?**
> INCR counter per user → check > limit → reject; EXPIRE để reset sau time window

**Q: Circuit Breaker ở trạng thái Open — request đến thì xử lý thế nào?**
> Fail fast ngay lập tức — return error mà không gọi downstream service

**Q: Tại sao Kafka tốt hơn RabbitMQ cho log aggregation?**
> Kafka retain messages (replay), high throughput, sequential disk write, consumer pull (không push)

---

### 🧪 Phần 3 — System Design nhanh (15 phút)

**Scenario 1:** Website bạn đột ngột tăng từ 1000 user/ngày lên 1 triệu user/ngày. Server bắt đầu chậm. Bạn làm gì? (Liệt kê theo thứ tự ưu tiên)

> 1. **Caching**: thêm Redis cache cho DB queries phổ biến → giảm DB load
> 2. **CDN**: static assets (CSS, JS, images) → offload web server
> 3. **Horizontal scaling**: thêm app servers + Load Balancer
> 4. **Database**: read replicas cho read-heavy queries
> 5. **Queue**: heavy tasks (email, image resize) → async via MQ
> 6. **Profiling**: tìm bottleneck thực sự (N+1 queries, slow queries)

**Scenario 2:** API của bạn đột ngột trả 500 errors. Bạn debug thế nào?

> 1. Check **logs** ngay → tìm error message/stack trace
> 2. Check **metrics**: error rate tăng từ khi nào? Deploy gần nhất?
> 3. Check **downstream deps**: DB connection OK? 3rd party API timeout?
> 4. Check **resource**: CPU, RAM, disk usage?
> 5. Rollback nếu sau deploy gần nhất
> 6. Fix → hotfix deploy → monitor

**Scenario 3:** User report "đôi khi mua hàng bị trừ tiền 2 lần". Nguyên nhân có thể là gì?

> 1. **Không idempotent**: user click submit 2 lần → 2 transactions
>    → Fix: idempotency key, disable button sau click
> 2. **Race condition**: 2 requests cùng lúc → check balance cùng lúc → cả 2 pass
>    → Fix: DB transaction + SELECT FOR UPDATE hoặc optimistic locking
> 3. **Network retry**: client timeout → retry → server đã xử lý lần 1 rồi
>    → Fix: idempotency key per request

**Scenario 4:** Thiết kế một hệ thống notification (push/email/SMS) khi user đặt hàng thành công:

```
Order Service → publish "order.created" event → Message Queue (Kafka)
                                                    ↓
                               ┌────────────────────┼────────────────────┐
                               ↓                    ↓                    ↓
                     Email Service          Push Service          SMS Service
                     (sendgrid)             (FCM/APNS)            (Twilio)
                     retry nếu fail         retry nếu fail        retry nếu fail
                               ↓                    ↓                    ↓
                          DLQ nếu fail         DLQ nếu fail         DLQ nếu fail
```

> Key points: decoupled (thêm channel mới không cần sửa Order Service), retry per channel, DLQ cho failed notifications, at-least-once delivery + idempotency key

---

# ═══════════════════════════════════════
# 21:30 · Software Engineering · 120 phút
# ═══════════════════════════════════════

---

## ⚡ Concurrency & Async · 45 phút

---

### Concurrency vs Parallelism

**Key points:**
```
Concurrency  = nhiều task đang "in progress" cùng lúc (có thể xen kẽ trên 1 CPU)
Parallelism  = nhiều task thực sự chạy đồng thời trên nhiều CPU cores

Ví dụ:
  Concurrency:  1 đầu bếp, 2 món → chặt thịt rồi đợi nước sôi, trong lúc đó xào rau
  Parallelism:  2 đầu bếp, 2 món → mỗi người nấu 1 món đồng thời
```

- **Thread**: unit of execution, share memory → cần đồng bộ
- **Process**: isolated memory → giao tiếp qua IPC (pipe, socket, shared mem)
- **Coroutine** (async): cooperative multitasking, 1 thread, yield control tự nguyện

**❓ Cần hiểu rõ:**
- Concurrency vs Parallelism — cho ví dụ thực tế phân biệt 2 khái niệm?
- I/O-bound task vs CPU-bound task — dùng thread/process/async cho loại nào?
- Green thread (goroutine, coroutine) vs OS thread — khác nhau thế nào?

---

### Threading & Race Conditions

**Key points — Race Condition:**
```python
# NGUY HIỂM: race condition
counter = 0

def increment():
    global counter
    # 3 operations: read → add 1 → write
    # Nếu 2 threads cùng read counter=5 → cả 2 write 6 → mất 1 lần tăng!
    counter += 1

# AN TOÀN: dùng lock
import threading
lock = threading.Lock()
counter = 0

def safe_increment():
    global counter
    with lock:
        counter += 1
```

**Key points — Python threading:**
```python
import threading

# Thread
t = threading.Thread(target=worker_fn, args=(arg1,))
t.start()
t.join()  # đợi thread finish

# Thread Pool (tốt hơn tạo thread thủ công)
from concurrent.futures import ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=4) as executor:
    futures = [executor.submit(task, arg) for arg in args]
    results = [f.result() for f in futures]

# Lock, RLock, Semaphore, Event, Condition
lock = threading.Lock()
with lock:          # auto acquire + release, exception-safe
    # critical section

sem = threading.Semaphore(5)  # max 5 concurrent access
with sem:
    # access shared resource
```

**Key points — Python GIL:**
```
GIL (Global Interpreter Lock): CPython chỉ cho 1 thread chạy Python bytecode tại 1 thời điểm

→ Thread KHÔNG giúp CPU-bound tasks (tính toán nặng) — GIL block
→ Thread VẪN giúp I/O-bound tasks (network, file) — khi thread chờ I/O thì release GIL

Fix CPU-bound với multiprocessing:
  from concurrent.futures import ProcessPoolExecutor
  → Mỗi process có GIL riêng → chạy song song thực sự
```

**❓ Cần hiểu rõ:**
- Race condition trong `counter += 1` xảy ra thế nào ở cấp độ CPU instructions?
- `with lock:` vs `lock.acquire()` + `lock.release()` — tại sao dùng context manager?
- Python GIL là gì? Tại sao tồn tại? Thread có ích gì với GIL?
- Thread pool vs tạo thread thủ công — tại sao thread pool tốt hơn?
- Deadlock trong code Python xảy ra khi nào? Cách phòng?
- `threading.Event` và `threading.Condition` dùng để làm gì?

---

### Async/Await & Event Loop

**Key points:**
```python
import asyncio

# Coroutine — hàm async, trả về coroutine object
async def fetch_data(url):
    await asyncio.sleep(1)   # yield control (non-blocking)
    return f"data from {url}"

# Event Loop: scheduler chạy coroutines
# Khi 1 coroutine await, event loop chạy coroutine khác

# Concurrent I/O (chạy song song trong 1 thread)
async def main():
    # Sequential: 3 giây
    # r1 = await fetch_data("url1")
    # r2 = await fetch_data("url2")
    # r3 = await fetch_data("url3")

    # Concurrent: ~1 giây
    r1, r2, r3 = await asyncio.gather(
        fetch_data("url1"),
        fetch_data("url2"),
        fetch_data("url3"),
    )

asyncio.run(main())
```

**Key points — Khi nào dùng gì:**

| Loại task | Tool phù hợp | Lý do |
|-----------|-------------|-------|
| I/O-bound (network, file, DB) | `asyncio` hoặc `threading` | Chờ I/O → release CPU |
| CPU-bound (tính toán nặng) | `multiprocessing` | Bypass GIL |
| Mixed | `asyncio` + `ProcessPoolExecutor` | Run CPU tasks in process pool |

**Key points — Producer-Consumer pattern:**
```python
import asyncio

async def producer(queue):
    for i in range(10):
        await queue.put(i)
        await asyncio.sleep(0.1)
    await queue.put(None)  # sentinel

async def consumer(queue):
    while True:
        item = await queue.get()
        if item is None: break
        print(f"Processing {item}")

async def main():
    queue = asyncio.Queue(maxsize=5)
    await asyncio.gather(producer(queue), consumer(queue))
```

**Key points — Common async pitfalls:**
```python
# ❌ Blocking call trong async context → block event loop!
async def bad():
    time.sleep(1)         # blocks event loop
    requests.get(url)     # blocking I/O

# ✅ Đúng
async def good():
    await asyncio.sleep(1)
    async with aiohttp.ClientSession() as s:
        r = await s.get(url)
```

**❓ Cần hiểu rõ:**
- Event loop là gì? Cơ chế hoạt động?
- `await` làm gì? Yield control nghĩa là gì?
- `asyncio.gather` vs `asyncio.wait` — khác nhau thế nào?
- Blocking call trong async context gây ra vấn đề gì?
- Async vs Threading cho I/O-bound — cái nào tốt hơn? Trade-off?
- `asyncio.Queue` vs `queue.Queue` — khi nào dùng cái nào?

---

### Java Concurrency (nếu cần)

**Key points:**
```java
// synchronized method
public synchronized void increment() {
    counter++;
}

// synchronized block (finer-grained)
synchronized(this) {
    counter++;
}

// java.util.concurrent
ExecutorService pool = Executors.newFixedThreadPool(4);
Future<Integer> future = pool.submit(() -> heavyComputation());
Integer result = future.get();  // block until done
pool.shutdown();

// CompletableFuture (async chaining)
CompletableFuture.supplyAsync(() -> fetchUser(id))
    .thenApply(user -> enrichUser(user))
    .thenAccept(user -> System.out.println(user));

// ReentrantLock (vs synchronized)
Lock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();  // PHẢI unlock trong finally!
}
```

**Key points — Java thread-safe collections:**
```
ConcurrentHashMap      → HashMap thread-safe, segment locking
CopyOnWriteArrayList   → read nhiều/write ít
BlockingQueue          → producer-consumer (ArrayBlockingQueue, LinkedBlockingQueue)
AtomicInteger          → atomic increment không cần lock
volatile               → visibility guarantee (không phải atomicity!)
```

**❓ Cần hiểu rõ:**
- `synchronized` vs `ReentrantLock` — khi nào dùng cái nào?
- `volatile` đảm bảo gì? KHÔNG đảm bảo gì?
- `CompletableFuture` vs `Future` — điểm khác nhau chính?
- `ConcurrentHashMap` thread-safe thế nào? `Collections.synchronizedMap` khác gì?
- ThreadLocal là gì? Dùng khi nào?

---

## 🧹 Clean Code + Testing · 30 phút

---

### Clean Code Principles

**Key points — Naming:**
```python
# ❌ Xấu
def calc(x, y, z):
    return x * y * (1 - z)

# ✅ Tốt — tên nói lên intention
def calculate_discounted_price(price, quantity, discount_rate):
    return price * quantity * (1 - discount_rate)

# ❌
d = 86400  # seconds in a day
# ✅
SECONDS_PER_DAY = 86400
```

**Key points — Functions:**
```
- 1 function = 1 việc (Single Responsibility)
- Tên function = verb + noun: getUserById(), sendEmail()
- Arguments ≤ 3 (nếu nhiều hơn → dùng object/dict)
- Không có side effects ẩn
- Fail fast: validate input đầu hàm, return sớm
```

**Key points — DRY, KISS, YAGNI:**
- **DRY** (Don't Repeat Yourself): logic trùng lặp → tách thành function/class
- **KISS** (Keep It Simple): code đơn giản > code "thông minh"
- **YAGNI** (You Ain't Gonna Need It): không code feature "phòng hờ"

**Key points — Code smells hay gặp:**

| Smell | Ví dụ | Fix |
|-------|-------|-----|
| Long method | Hàm 200 dòng | Tách thành nhiều hàm nhỏ |
| Magic number | `if status == 3` | Dùng const: `COMPLETED = 3` |
| God class | Class làm 10 thứ | Tách theo SRP |
| Deep nesting | if if if if | Early return / flatten |
| Long parameter list | `def f(a,b,c,d,e,f)` | Dùng object |

**❓ Cần hiểu rõ:**
- DRY vs WET (Write Everything Twice) — khi nào DRY làm hại?
- Early return pattern là gì? Tại sao tốt hơn deep nesting?
- Code comment khi nào cần, khi nào không cần?
- Refactoring là gì? Khác với rewriting thế nào?

---

### Testing

**Key points — Test pyramid:**
```
         /E2E\          ← ít nhất, chậm, cost cao (Selenium, Playwright)
        /──────\
       / Integ. \       ← middle (test với real DB, real services)
      /──────────\
     /  Unit Tests \    ← nhiều nhất, nhanh, cheap (mock dependencies)
    /______________\
```

**Key points — Unit testing:**
```python
import pytest
from unittest.mock import Mock, patch, MagicMock

# Test đơn giản
def add(a, b): return a + b

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

# Test với mock (giả lập dependency)
def get_user(db, user_id):
    return db.query(f"SELECT * FROM users WHERE id = {user_id}")

def test_get_user():
    mock_db = Mock()
    mock_db.query.return_value = {"id": 1, "name": "Alice"}
    
    result = get_user(mock_db, 1)
    
    assert result["name"] == "Alice"
    mock_db.query.assert_called_once()  # verify mock was called

# Parametrize test
@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
])
def test_add_params(a, b, expected):
    assert add(a, b) == expected
```

**Key points — Test doubles:**

| Type | Mô tả | Khi dùng |
|------|-------|---------|
| **Mock** | Verify interactions (was method called?) | Kiểm tra behavior |
| **Stub** | Return canned response | Kiểm tra state |
| **Fake** | Working implementation (in-memory DB) | Integration-like tests |
| **Spy** | Real object + verify calls | Khi không muốn replace |

**Key points — TDD (Test-Driven Development):**
```
Red → Green → Refactor

1. Red:    Viết test FAIL trước (function chưa tồn tại)
2. Green:  Viết code minimal để test PASS
3. Refactor: Cleanup code, tests vẫn pass

Benefits: testable design, confidence to refactor, living documentation
```

**❓ Cần hiểu rõ:**
- Unit test vs Integration test vs E2E test — scope và purpose từng loại?
- Mock vs Stub vs Fake — phân biệt bằng ví dụ cụ thể?
- TDD "Red → Green → Refactor" — tại sao viết test trước?
- Test coverage 100% có đảm bảo không có bug không?
- Flaky test là gì? Nguyên nhân và cách fix?

---

## 🐳 Docker + CI/CD · 30 phút

---

### Docker & Containers

**Key points — Container vs VM:**
```
VM:         Guest OS + App + Binaries (nặng, GB, boot minutes)
Container:  App + Binaries only, share Host OS kernel (nhẹ, MB, boot seconds)

Docker: build, ship, run containers
Image:  read-only template (class)
Container: running instance of image (object)
```

**Key points — Dockerfile:**
```dockerfile
# Multi-stage build (giữ image nhỏ)
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY . .
EXPOSE 8000

# Chạy với non-root user (security)
RUN useradd -m appuser
USER appuser

CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Key points — Docker Compose:**
```yaml
# docker-compose.yml
services:
  app:
    build: .
    ports: ["8000:8000"]
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on: [db, redis]

  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=pass
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  postgres_data:
```

**Key points — Best practices:**
```
✅ 1 process per container (separation of concerns)
✅ .dockerignore để loại bỏ node_modules, .git, ...
✅ Layer caching: COPY requirements.txt → RUN pip install → COPY . .
   (thay đổi code không re-install dependencies)
✅ Non-root user
✅ Multi-stage build để giảm image size
✅ Health check trong Dockerfile: HEALTHCHECK CMD curl http://localhost:8000/health
```

**❓ Cần hiểu rõ:**
- Container vs VM — trade-off về isolation và overhead?
- Docker image layer là gì? Tại sao layer caching quan trọng?
- `COPY requirements.txt` trước `COPY . .` — tại sao thứ tự này?
- Docker volume vs bind mount — khác nhau thế nào?
- Kubernetes là gì? Container orchestration làm gì?
- Docker network: bridge, host, overlay — khi nào dùng cái nào?

---

### CI/CD Pipeline

**Key points — CI (Continuous Integration):**
```
Developer push code → CI pipeline tự động chạy:
  1. Lint/Format check (flake8, black, eslint)
  2. Unit tests
  3. Integration tests
  4. Security scan (Snyk, Trivy)
  5. Build Docker image
  6. Push to registry

Nếu bất kỳ step nào fail → block merge PR
```

**Key points — CD (Continuous Delivery/Deployment):**
```
CD = tự động deploy lên môi trường sau khi CI pass

Delivery:   deploy tự động lên staging, manual approval để lên prod
Deployment: deploy tự động lên prod (không cần manual)
```

**Key points — Deployment Strategies:**

| Strategy | Mô tả | Downtime? | Rollback? |
|----------|-------|-----------|----------|
| **Recreate** | Tắt cũ, bật mới | Có | Chậm |
| **Rolling** | Update từng instance | Không | Được |
| **Blue-Green** | 2 môi trường, switch traffic | Không | Nhanh (switch lại) |
| **Canary** | Dần dần tăng % traffic mới | Không | Nhanh |
| **Feature Flag** | Code mới nhưng tắt feature | Không | Tắt flag |

```
Blue-Green:
  Blue (prod đang chạy v1)
  Green (deploy v2 mới)
  → Test Green OK → switch LB → Blue trở thành standby
  → Rollback: switch LB lại Blue

Canary:
  Deploy v2 → 5% traffic → monitor metrics → 20% → 50% → 100%
  → Nếu error rate tăng → rollback ngay
```

**Key points — GitHub Actions example:**
```yaml
name: CI/CD Pipeline
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with: {python-version: '3.11'}
      - run: pip install -r requirements.txt
      - run: pytest --cov=app tests/
      - run: flake8 app/

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build & push Docker image
        run: |
          docker build -t myapp:${{ github.sha }} .
          docker push registry/myapp:${{ github.sha }}
      - name: Deploy to staging
        run: kubectl set image deployment/myapp app=registry/myapp:${{ github.sha }}
```

**Key points — Git workflow:**
```
Trunk-based development (recommended):
  main branch là source of truth
  Short-lived feature branches (1-2 ngày)
  Feature flags để hide incomplete features
  Merge thường xuyên → ít conflict

GitFlow:
  main, develop, feature/*, release/*, hotfix/*
  Phức tạp hơn, phù hợp release cycle cứng
```

**❓ Cần hiểu rõ:**
- CI vs CD (Delivery vs Deployment) — phân biệt?
- Blue-Green vs Canary — khi nào dùng cái nào?
- Rollback strategy trong Kubernetes thế nào? (`kubectl rollout undo`)
- Feature flag và Blue-Green deployment khác nhau thế nào?
- Trunk-based vs GitFlow — trade-off?
- Infrastructure as Code (IaC) là gì? Terraform dùng để làm gì?

---

## 🎯 Mock Test — Software (15 phút)

> Tự trả lời rồi mới xem đáp án.

---

**Q1:** Đoạn code dưới đây thread-safe không? Nếu không, fix thế nào?
```python
class Counter:
    def __init__(self):
        self.count = 0
    def increment(self):
        self.count += 1  # 2 threads cùng gọi increment() đồng thời
```
> **Đáp án:** Không thread-safe. `self.count += 1` không atomic (read-modify-write). Fix: dùng `threading.Lock()` hoặc `threading.atomic` hoặc `threading.local`.

---

**Q2:** Python GIL ảnh hưởng gì đến việc chọn threading vs multiprocessing?
> **Đáp án:** GIL block threads khi chạy Python bytecode → threads không parallel cho CPU-bound. I/O-bound: thread OK (release GIL khi chờ I/O). CPU-bound: phải dùng multiprocessing để bypass GIL.

---

**Q3:** `async def` function nếu gọi `time.sleep(5)` bên trong thì xảy ra gì?
> **Đáp án:** Block toàn bộ event loop trong 5 giây — không có coroutine nào khác chạy được. Phải dùng `await asyncio.sleep(5)` thay thế.

---

**Q4:** Trong Docker, tại sao layer order quan trọng?
```dockerfile
# Option A:         vs     # Option B:
COPY . .                   COPY requirements.txt .
COPY requirements.txt .    RUN pip install -r requirements.txt
RUN pip install ...        COPY . .
```
> **Đáp án:** Option B tốt hơn. Docker cache layer. Nếu chỉ thay đổi source code (không thay requirements.txt), Option B sẽ dùng cached layer "pip install" → build nhanh hơn nhiều. Option A phải re-install mọi lần thay đổi code.

---

**Q5:** Blue-Green deployment — traffic đang ở Blue (v1), bạn muốn deploy v2. Các bước?
> **Đáp án:**
> 1. Deploy v2 lên Green environment
> 2. Smoke test Green (health check, basic functionality)
> 3. Switch Load Balancer: 100% traffic → Green
> 4. Monitor metrics (error rate, latency) trong 10-15 phút
> 5. Nếu OK: Blue trở thành standby
> 6. Nếu bad: switch LB lại Blue → rollback hoàn tất trong seconds

---

**Q6:** Unit test vs Integration test — cho ví dụ test function `create_user(db, email, password)`:
> **Unit test:** Mock `db`, kiểm tra hàm call đúng methods, return đúng kết quả, validate email format, hash password
> **Integration test:** Dùng test database thật (hoặc in-memory), gọi `create_user` thực sự, verify user được insert vào DB, verify password được hash
