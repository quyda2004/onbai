# Ôn tập toàn bộ trong 1.5 ngày

> Dành cho người **đã có nền** — không giải thích từ đầu, chỉ nhắc lại điểm cốt lõi,
> bẫy hay gặp, và complexity cần nhớ.
> Mỗi block = 1 khoảng thời gian cụ thể. Stick to the schedule.

---

## Lịch tổng quan

```
  ┌─────────────────────────────────────────────────────────────────────┐
  │  NGÀY 1  (~ 12 giờ học)                                             │
  ├───────────┬─────────────────────────────────────────────────────────┤
  │ 08:00-10:30│ DSA — Nền tảng & Kỹ thuật mảng (6 topic)              │
  │ 10:30-12:30│ DSA — Đệ quy + Cấu trúc tuyến tính (3 topic)          │
  │ 12:30-13:30│ NGHỈ TRƯA                                              │
  │ 13:30-15:30│ DSA — Tree / Heap / Trie (3 topic)                     │
  │ 15:30-17:30│ DSA — Graph + Tìm kiếm & Sắp xếp (4 topic)            │
  │ 17:30-18:00│ NGHỈ                                                   │
  │ 18:00-19:30│ DSA — Thuật toán nâng cao (3 topic)                    │
  │ 19:30-21:00│ OOP (8 topic)                                          │
  │ 21:00-22:30│ OS (6 topic)                                           │
  ├───────────┴─────────────────────────────────────────────────────────┤
  │  NGÀY 2  (~ 6 giờ học — buổi sáng)                                 │
  ├───────────┬─────────────────────────────────────────────────────────┤
  │ 08:00-10:00│ Network (6 topic)                                      │
  │ 10:00-13:00│ System Design (8 topic)                                │
  │ 13:00-13:30│ Điểm yếu / câu hỏi còn mơ                             │
  └───────────┴─────────────────────────────────────────────────────────┘
```

---

---

# NGÀY 1

---

## 08:00 – 10:30 · DSA: Nền tảng & Kỹ thuật mảng

### 📊 Big O Notation · 20 phút
> [📖 Chi tiết](dsa/01_bigo/ly_thuyet.md)

- `O(log n)` = mỗi bước loại đi **nửa** không gian → Binary Search, BST balanced
- `O(n log n)` = sort tốt nhất có thể với comparison-based
- **Amortized O(1)**: dynamic array append — thỉnh thoảng O(n) nhưng trung bình O(1)
- Bẫy: đệ quy có **2 nhánh** → thường O(2ⁿ), không phải O(n)
- Space complexity: nhớ tính **call stack** với đệ quy — O(h) với tree, O(n) với linear recursion

---

### 📋 Array & String · 20 phút
> [📖 Chi tiết](dsa/02_array_string/ly_thuyet.md)

- In-place swap dùng XOR hoặc tuple unpack — không cần biến temp
- String immutable (Python/Java) → concat trong loop = O(n²), dùng `join` hoặc StringBuilder
- Subarray vs Subsequence: subarray = liên tiếp, subsequence = không cần liên tiếp
- Bẫy off-by-one: `range(n-1)` hay `range(n)`? Vẽ ví dụ nhỏ trước

---

### 🗺️ HashMap / HashSet · 20 phút
> [📖 Chi tiết](dsa/03_hashmap_hashset/ly_thuyet.md)

- Dùng khi cần tra cứu O(1) — đổi **time** lấy **space**
- Pattern đếm tần suất: `Counter(arr)` hoặc `defaultdict(int)`
- Pattern two-pass: lần 1 build map, lần 2 dùng map
- Bẫy: key phải **hashable** — list không hash được, dùng tuple
- Collision worst case O(n), nhưng average O(1) với load factor tốt

---

### 👉 Two Pointers · 25 phút
> [📖 Chi tiết](dsa/04_two_pointers/ly_thuyet.md)

- **Left/Right** (mảng đã sort): 2 Sum sorted, 3 Sum, container with most water
- **Fast/Slow** (linked list): cycle detection, tìm middle node
- **Sliding** (không phải sliding window): merge 2 sorted arrays
- Điều kiện áp dụng: thường cần **mảng đã sort** hoặc cấu trúc có tính đơn điệu
- Bẫy: quên điều kiện `left < right` → infinite loop

---

### 🪟 Sliding Window · 25 phút
> [📖 Chi tiết](dsa/05_sliding_window/ly_thuyet.md)

- **Fixed window**: sum/average của k phần tử liên tiếp
- **Variable window**: mở rộng `right`, thu hẹp `left` khi vi phạm điều kiện
- Template variable window:
  ```
  left = 0
  for right in range(n):
      # thêm arr[right] vào window
      while window vi phạm:
          # bỏ arr[left] ra
          left += 1
      # cập nhật kết quả
  ```
- Bẫy: quên update state khi `left` tăng

---

### ➕ Prefix Sum · 20 phút
> [📖 Chi tiết](dsa/06_prefix_sum/ly_thuyet.md)

- `prefix[i] = prefix[i-1] + arr[i-1]` → sum từ l đến r = `prefix[r+1] - prefix[l]`
- **Biến thể hay gặp**: đếm subarray có sum = k → dùng `prefix + hashmap`
  - `count[prefix_sum - k]` tăng thêm 1 mỗi khi gặp
- 2D prefix sum: `p[i][j] = p[i-1][j] + p[i][j-1] - p[i-1][j-1] + grid[i][j]`

---

## 10:30 – 12:30 · DSA: Đệ quy + Cấu trúc tuyến tính

### 🔄 Recursion · 30 phút
> [📖 Chi tiết](dsa/07_recursion/ly_thuyet.md)

- Mọi đệ quy = **base case** + **thu nhỏ bài toán về phía base case**
- Khi nào dùng memo: nếu cùng 1 input gọi nhiều lần → overlap subproblems
- **Tail recursion** = đệ quy cuối hàm → compiler tối ưu được stack (Python không tối ưu)
- Bẫy phổ biến:
  - Quên base case → stack overflow
  - Return giá trị từ nhánh đệ quy nhưng quên return ở trên
  - Modify biến shared mà không restore → backtracking sai

---

### 📚 Stack & Queue · 25 phút
> [📖 Chi tiết](dsa/08_stack_queue/ly_thuyet.md)

- **Monotonic Stack**: duy trì stack tăng dần hoặc giảm dần → next greater element, histogram
  - Khi nào pop: khi phần tử mới vi phạm tính đơn điệu của stack
- **Deque**: O(1) ở cả 2 đầu → sliding window maximum (monotonic deque)
- **BFS dùng Queue**, **DFS/backtracking dùng Stack** (hoặc call stack)
- Bẫy: `queue.pop(0)` là O(n) — dùng `collections.deque` với `popleft()`

---

### 🔗 Linked List · 25 phút
> [📖 Chi tiết](dsa/09_linked_list/ly_thuyet.md)

- **Fast/Slow pointer**: cycle → Floyd's algorithm; middle → slow dừng ở mid
- **Reverse**: 3 biến `prev, curr, next` — vẽ trước khi code
- **Dummy node**: tránh edge case khi xoá head hoặc insert đầu list
- Bẫy:
  - Quên update `prev` trước khi move `curr`
  - `next = curr.next` phải lưu trước khi đứt link

---

## 13:30 – 15:30 · DSA: Tree / Heap / Trie

### 🌳 Tree & BST · 40 phút
> [📖 Chi tiết](dsa/10_tree_bst/ly_thuyet.md)

- **Traversal**:
  - Inorder BST = mảng đã sort → dùng để validate BST
  - Preorder = serialize/reconstruct tree
  - Postorder = tính toán từ lá lên (height, diameter)
- **DFS trên tree** = đệ quy với return value có nghĩa
- Pattern hay gặp: "trả về 2 giá trị từ đệ quy" — (is_valid, value)
- BST operations: O(log n) average, O(n) worst (suy biến)
- **LCA**: tìm lowest common ancestor — nếu cả 2 node ở 2 phía của root → root là LCA
- Bẫy: height vs depth, null check trước khi truy cập `.left/.right`

---

### ⛰️ Heap · 25 phút
> [📖 Chi tiết](dsa/11_heap/ly_thuyet.md)

- Python `heapq` là **min-heap** — max-heap dùng giá trị âm
- `heappush` O(log n), `heappop` O(log n), `heapify` O(n)
- **Top-K largest**: dùng min-heap size K → sau khi push nếu size > K thì pop
- **K-way merge**: push (value, list_index, element_index) vào heap
- Bẫy: `heapify` modify in-place — không cần gán lại

---

### 🔤 Trie · 25 phút
> [📖 Chi tiết](dsa/13_trie/ly_thuyet.md)

- Node = `dict` hoặc `array[26]` + flag `is_end`
- Insert/Search/StartsWith đều O(L) với L = độ dài word
- Dùng khi: autocomplete, spell check, IP routing, word search trong grid
- **Compressed Trie** (Radix Tree): gộp node có 1 con — tiết kiệm space
- Bẫy: quên set `is_end = True` khi insert

---

## 15:30 – 17:30 · DSA: Graph + Tìm kiếm & Sắp xếp

### 🕸️ Graph · 30 phút
> [📖 Chi tiết](dsa/12_graph/ly_thuyet.md)

- **Adjacency List** O(V+E) space — dùng cho sparse graph (thực tế)
- **Adjacency Matrix** O(V²) space — dùng khi cần check edge O(1)
- Directed vs Undirected: undirected = mỗi edge thêm vào 2 chiều
- **Weighted graph**: Dijkstra O((V+E) log V), Bellman-Ford O(VE) cho negative weight
- **Union-Find**: detect cycle, connected components — `find` + `union` với path compression

---

### 🌊 DFS / BFS · 30 phút
> [📖 Chi tiết](dsa/16_dfs_bfs/ly_thuyet.md)

- **BFS** = shortest path (unweighted), level-order, minimum steps
- **DFS** = explore all paths, connected components, cycle detection
- BFS template:
  ```
  queue = deque([start])
  visited = {start}
  while queue:
      node = queue.popleft()
      for neighbor in graph[node]:
          if neighbor not in visited:
              visited.add(neighbor)
              queue.append(neighbor)
  ```
- Bẫy: quên `visited` set → infinite loop với cycle; add vào visited **khi push**, không phải khi pop

---

### 🔍 Binary Search · 25 phút
> [📖 Chi tiết](dsa/14_binary_search/ly_thuyet.md)

- Template tránh infinite loop:
  ```python
  left, right = 0, n - 1
  while left <= right:        # <= quan trọng
      mid = left + (right - left) // 2
      if condition: right = mid - 1
      else: left = mid + 1
  ```
- **Search space ẩn**: bài toán "tìm giá trị nhỏ nhất thoả điều kiện" → binary search trên answer
- Bẫy: `left + right // 2` khác `left + (right - left) // 2` → overflow với integer lớn

---

### ↕️ Sorting · 20 phút

- **Quick Sort**: O(n log n) average, O(n²) worst (pivot bad) — in-place, cache-friendly
- **Merge Sort**: O(n log n) guaranteed, O(n) space — stable, dùng cho linked list
- **Heap Sort**: O(n log n) guaranteed, O(1) space — không stable
- **Counting/Radix Sort**: O(n) với điều kiện — khi range nhỏ hoặc cần stable
- Python `sort()` = TimSort = O(n log n) worst, O(n) best (nearly sorted)

---

### 📐 Topological Sort · 15 phút
> [📖 Chi tiết](dsa/17_topological_sort/ly_thuyet.md)

- Chỉ áp dụng với **DAG** (Directed Acyclic Graph)
- **Kahn's** (BFS): đếm in-degree, push node có in-degree = 0, giảm in-degree khi pop
- **DFS-based**: DFS xong push vào stack, reverse stack = topological order
- Detect cycle: nếu kết quả Kahn's có ít hơn V node → có cycle
- Bài thực tế: course schedule, build system dependencies

---

## 18:00 – 19:30 · DSA: Thuật toán nâng cao

### 💰 Greedy · 25 phút
> [📖 Chi tiết](dsa/18_greedy/ly_thuyet.md)

- Greedy đúng khi có **greedy choice property** + **optimal substructure**
- Cách chứng minh: "exchange argument" — giả sử optimal không dùng greedy choice → swap → vẫn optimal
- Pattern hay gặp:
  - Interval scheduling: sort by **end time**, chọn interval không overlap
  - Jump game: track max reachable index
  - Huffman encoding: always merge 2 smallest
- Bẫy: Greedy không đúng với coin change (nếu coin set không standard)

---

### 🧮 Dynamic Programming · 40 phút
> [📖 Chi tiết](dsa/19_dynamic_programming/ly_thuyet.md)

- Nhận biết DP: **optimal substructure** + **overlapping subproblems**
- **Top-down** (memo): dễ viết, dễ nghĩ, có overhead function call
- **Bottom-up** (tabulation): tốt hơn về space, không có stack overflow
- Các dạng DP phổ biến:
  ```
  1D DP:     dp[i] phụ thuộc dp[i-1], dp[i-2]  →  Fibonacci, Climbing Stairs
  2D DP:     dp[i][j]                            →  LCS, Edit Distance, Knapsack
  Interval:  dp[i][j] = subproblem [i..j]        →  Matrix Chain, Burst Balloons
  Bitmask:   dp[mask][i]                         →  TSP, Hamiltonian Path
  ```
- **State reduction**: Knapsack 2D → 1D bằng cách iterate ngược
- Bẫy: thứ tự tính toán phải đảm bảo subproblem đã có khi cần

---

### ↩️ Backtracking · 25 phút
> [📖 Chi tiết](dsa/20_backtracking/ly_thuyet.md)

- Template:
  ```python
  def backtrack(state, choices):
      if is_solution(state):
          result.append(state[:])  # copy!
          return
      for choice in choices:
          if is_valid(choice):
              state.append(choice)
              backtrack(state, next_choices)
              state.pop()          # UNDO
  ```
- **Pruning** = cắt nhánh sớm → tăng performance đáng kể
- Bẫy: quên `state[:]` khi append → tất cả result trỏ cùng object
- Bẫy: quên undo (pop) → state bị nhiễm từ nhánh trước

---

## 19:30 – 21:00 · OOP
> [📖 Roadmap chi tiết](oop/ROADMAP.md)

### 🧱 Class & Object + 🔒 Encapsulation · 15 phút
> [📖](oop/01_class_object/ly_thuyet.md) · [📖](oop/02_encapsulation/ly_thuyet.md)

- `__init__` không "tạo" object — `__new__` mới tạo, `__init__` chỉ init
- `@property` + setter: encapsulation mà vẫn dùng cú pháp attribute
- Name mangling: `__var` → `_ClassName__var` — không phải truly private
- Bẫy: mutable default argument trong `__init__` → shared giữa các instance

---

### 👨‍👩‍👦 Inheritance · 15 phút
> [📖](oop/03_inheritance/ly_thuyet.md)

- `super()` trong Python 3 dùng **MRO** (C3 linearization) — không phải chỉ gọi parent
- **MRO** quan trọng khi multiple inheritance: `ClassName.__mro__`
- Override `__str__` và `__repr__` — `__repr__` cho debug, `__str__` cho display
- Bẫy: gọi `super().__init__()` khi có multiple inheritance — dùng cooperative inheritance

---

### 🎭 Polymorphism + 🎨 Abstraction · 15 phút
> [📖](oop/04_polymorphism/ly_thuyet.md) · [📖](oop/05_abstraction/ly_thuyet.md)

- **Duck typing** (Python): không check type, check behavior — "if it walks like a duck"
- **Runtime polymorphism** = method overriding + virtual dispatch
- **Compile-time polymorphism** = method overloading (Python không có native, dùng `*args`)
- Abstract class: `from abc import ABC, abstractmethod` — không instantiate được

---

### 📜 Interface & Abstract Class · 15 phút
> [📖](oop/06_interface_abstract/ly_thuyet.md)

- Interface = **hợp đồng** (CAN-DO) — class có thể implement nhiều interface
- Abstract class = **bản thiết kế** (IS-A) — có thể có concrete method, single inheritance
- Khi nào dùng cái nào:
  - Nhiều class không liên quan cần cùng hành vi → Interface
  - Muốn share code giữa các subclass liên quan → Abstract class
- **Dependency Injection**: inject qua constructor, inject interface không phải concrete class

---

### 🏗️ SOLID · 20 phút
> [📖](oop/07_solid_principles/ly_thuyet.md)

- **S**: class có nhiều hơn 1 lý do để thay đổi → vi phạm SRP
- **O**: thêm feature bằng cách **thêm class mới**, không sửa class cũ
- **L**: subclass không được **ném exception mới** hoặc **yếu hoá precondition**
- **I**: interface to = nhiều method = client phải implement method không cần
- **D**: module high-level không import module low-level — cả 2 import **abstraction**
- Bẫy phỏng vấn: hỏi ví dụ vi phạm, không chỉ hỏi định nghĩa

---

### 🎯 Design Patterns · 20 phút
> [📖](oop/08_design_patterns/ly_thuyet.md)

- **Singleton**: thread-safe cần double-checked locking hoặc metaclass
- **Factory Method**: trả về interface, không trả về concrete class
- **Observer**: Subject giữ list observers, `notify()` gọi `update()` của tất cả
- **Strategy**: inject algorithm qua constructor — swap được lúc runtime
- **Decorator**: wrap object cùng interface — Python `@decorator` khác với OOP Decorator pattern
- **Builder**: tránh "telescoping constructor" — method chaining, trả về `self`

---

## 21:00 – 22:30 · OS
> [📖 Roadmap chi tiết](os/ROADMAP.md)

### ⚙️ Process & Thread · 20 phút
> [📖](os/01_process_thread/ly_thuyet.md)

- Process = isolated memory space; Thread = shared memory trong cùng process
- **Context switch**: lưu/restore registers + PC + stack pointer — tốn kém
- **PCB** (Process Control Block): lưu toàn bộ state của process
- Thread nhẹ hơn process vì share address space — nhưng lỗi 1 thread có thể kill cả process
- Bẫy phỏng vấn: "tại sao fork() + exec() thay vì tạo process trực tiếp?"

---

### 🧠 Memory Management · 20 phút
> [📖](os/02_memory_management/ly_thuyet.md)

- **Virtual memory**: mỗi process thấy không gian địa chỉ riêng — MMU translate virtual → physical
- **Page fault**: access page không có trong RAM → OS load từ disk (swap)
- **Thrashing**: page fault liên tục vì RAM quá nhỏ, CPU dành toàn thời gian swap
- **Memory leak**: heap không free → dùng hết RAM dần
- TLB (Translation Lookaside Buffer): cache của page table → giảm latency dịch địa chỉ

---

### 📅 Scheduling · 15 phút
> [📖](os/03_scheduling/ly_thuyet.md)

- **FCFS**: simple nhưng convoy effect — 1 process dài block tất cả
- **SJF**: optimal average waiting time nhưng starvation với process dài
- **Round Robin**: fair, response time tốt — quantum size quan trọng (quá nhỏ → context switch overhead)
- **Priority Scheduling**: aging giải quyết starvation
- **Multilevel Queue**: foreground (interactive) vs background (batch) — khác priority

---

### 🔒 Deadlock · 15 phút
> [📖](os/04_deadlock/ly_thuyet.md)

- **4 điều kiện Coffman**: Mutual Exclusion + Hold & Wait + No Preemption + Circular Wait
- Phá **bất kỳ 1** điều kiện → không deadlock
- **Banker's Algorithm**: kiểm tra safe state trước khi cấp tài nguyên
- **Deadlock vs Livelock**: livelock = process đang chạy nhưng không tiến triển (nhường nhịn nhau mãi)
- **Deadlock vs Starvation**: starvation = process không bao giờ được tài nguyên dù không có deadlock

---

### 📁 File System + 🔄 Synchronization · 20 phút
> [📖](os/05_file_system/ly_thuyet.md) · [📖](os/06_synchronization/ly_thuyet.md)

- **inode**: metadata của file (permission, size, pointer tới blocks) — không chứa tên file
- **Hard link** vs **Soft link**: hard link share inode, soft link là shortcut có thể broken
- **Race condition**: kết quả phụ thuộc vào timing — luôn assume worst case ordering
- **Mutex**: binary semaphore có ownership — chỉ thread lock mới unlock được
- **Semaphore**: counter — `wait()` giảm, `signal()` tăng — không có ownership
- **Monitor**: mutex + condition variable — Java `synchronized`, Python `with lock:`
- **Spinlock**: busy-wait — tốt khi critical section rất ngắn, xấu khi dài

---

---

# NGÀY 2

---

## 08:00 – 10:00 · Network
> [📖 Roadmap chi tiết](network/ROADMAP.md)

### 🌐 OSI Model · 15 phút
> [📖](network/01_osi_model/ly_thuyet.md)

- 7 tầng: **P**hysical → **D**ata Link → **N**etwork → **T**ransport → **S**ession → **P**resentation → **A**pplication
- Mnemonic: "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way"
- TCP/IP model = 4 tầng (gộp): Network Access | Internet | Transport | Application
- Tầng quan trọng nhất phỏng vấn: **3** (IP, routing), **4** (TCP/UDP), **7** (HTTP, DNS, SMTP)
- **PDU** theo tầng: bit → frame → packet → segment → data

---

### 🔗 TCP / IP · 25 phút
> [📖](network/02_tcp_ip/ly_thuyet.md)

- **3-way handshake**: SYN → SYN-ACK → ACK (establish)
- **4-way teardown**: FIN → ACK → FIN → ACK (close)
- **TCP vs UDP**:
  ```
  TCP: reliable, ordered, flow control, congestion control — HTTP, SSH, FTP
  UDP: fast, no guarantee — DNS, video stream, gaming
  ```
- **Sliding window**: cho phép gửi nhiều packet mà không cần ACK từng cái
- **TIME_WAIT**: tại sao cần? Đảm bảo ACK cuối cùng đến được peer
- Bẫy: TCP **không** đảm bảo message boundary — có thể nhận nhiều lần, phải tự parse

---

### 🌍 HTTP / HTTPS · 25 phút
> [📖](network/03_http_https/ly_thuyet.md)

- **HTTP/1.1**: persistent connection, pipelining (nhưng head-of-line blocking)
- **HTTP/2**: multiplexing trên 1 TCP connection, header compression (HPACK), server push
- **HTTP/3**: chạy trên **QUIC** (UDP-based) — không còn head-of-line blocking ở transport
- **TLS handshake**: ClientHello → ServerHello + Certificate → Key Exchange → Finished
- Status codes hay hỏi: 301 vs 302, 401 vs 403, 429, 503
- **CORS**: browser enforce, server set header — không phải security mechanism thực sự

---

### 📡 DNS · 20 phút
> [📖](network/04_dns/ly_thuyet.md)

- **Recursive vs Iterative query**: resolver làm hết thay client (recursive) vs client tự hỏi từng bước
- **TTL**: cache duration — thấp = update nhanh, cao = ít load lên DNS server
- **Record types**: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail), NS (nameserver), TXT (verify)
- **DNS over HTTPS (DoH)**: mã hoá DNS query — prevent eavesdropping
- Bẫy: CNAME không dùng được ở apex domain (root domain) — dùng ALIAS hoặc ANAME

---

### 🔌 Socket + 🛡️ Security · 35 phút
> [📖](network/05_socket/ly_thuyet.md) · [📖](network/06_security/ly_thuyet.md)

- **Socket**: endpoint = (IP, Port) — TCP socket = (src_ip, src_port, dst_ip, dst_port)
- **Blocking vs Non-blocking**: blocking `accept()` đợi mãi → cần thread hoặc async I/O
- **Select/Poll/Epoll**: multiplexing nhiều socket — epoll O(1) vs select O(n)
- **Symmetric encryption**: AES — nhanh, cần share key trước
- **Asymmetric encryption**: RSA — chậm, dùng để exchange symmetric key
- **TLS dùng cả 2**: asymmetric để exchange session key, symmetric để encrypt data
- **Common attacks**: MITM (giả server), CSRF (forged request), XSS (inject script), SQL injection

---

## 10:00 – 13:00 · System Design
> [📖 Roadmap chi tiết](system-design/ROADMAP.md)

### 📈 Scalability · 20 phút
> [📖](system-design/01_scalability/ly_thuyet.md)

- **Vertical** (scale up): giới hạn bởi hardware tối đa, single point of failure
- **Horizontal** (scale out): cần stateless service — session phải store ở Redis/DB, không ở RAM
- **Bottleneck analysis**: CPU bound vs I/O bound vs Memory bound → giải pháp khác nhau
- **Stateless**: request bất kỳ có thể xử lý bởi server bất kỳ → prereq cho horizontal scale

---

### ⚖️ Load Balancing · 20 phút
> [📖](system-design/02_load_balancing/ly_thuyet.md)

- **Algorithms**: Round Robin, Weighted RR, Least Connections, IP Hash (sticky session), Random
- **L4 vs L7**: L4 = TCP level (nhanh, không hiểu HTTP), L7 = HTTP level (smart routing, SSL termination)
- **Health check**: LB tự loại server chết ra khỏi pool
- **Active-Passive vs Active-Active**: HA setup cho LB chính nó
- Bẫy: sticky session = stateful → một server chết là mất session → dùng external session store

---

### ⚡ Caching · 25 phút
> [📖](system-design/03_caching/ly_thuyet.md)

- **Cache-aside** (lazy loading): app đọc cache trước, miss thì đọc DB rồi write cache
- **Write-through**: write vào cache VÀ DB cùng lúc — consistent nhưng write latency cao
- **Write-behind** (write-back): write cache trước, async write DB — fast nhưng risk mất data
- **Eviction policies**: LRU (phổ biến nhất), LFU (cho access pattern không đều), TTL
- **Cache stampede** (thundering herd): nhiều request cùng miss cache cùng lúc → DB quá tải → dùng mutex lock hoặc probabilistic early expiration
- CDN = geographically distributed cache cho static assets

---

### 🗄️ Database · 30 phút
> [📖](system-design/04_database/ly_thuyet.md)

- **SQL vs NoSQL**:
  ```
  SQL:   ACID, schema cứng, join tốt, vertical scale chủ yếu
  NoSQL: BASE, schema flexible, horizontal scale, nhiều loại (document/kv/graph/column)
  ```
- **Index**: B-tree (range query tốt), Hash (exact match O(1)), Composite (thứ tự cột quan trọng)
- **Sharding**: horizontal partition data — consistent hashing tránh re-shard khi thêm node
- **Replication**: Master-Slave (read scale) vs Master-Master (write scale, conflict risk)
- **N+1 Problem**: N queries thêm cho N rows — dùng JOIN hoặc eager loading
- **Connection pool**: reuse connection thay vì tạo mới mỗi request

---

### 📨 Message Queue · 20 phút
> [📖](system-design/05_message_queue/ly_thuyet.md)

- **Why**: decouple producer/consumer, buffer traffic spike, async processing
- **Kafka vs RabbitMQ**:
  - Kafka: log-based, retain message (replay), high throughput, consumer pull
  - RabbitMQ: traditional queue, message ack/nack, routing rules, consumer push
- **At-least-once vs At-most-once vs Exactly-once**: Exactly-once rất khó, thường dùng at-least-once + idempotency
- **Dead Letter Queue**: message failed nhiều lần → DLQ để debug
- Bẫy: consumer process xong rồi mới ACK — không phải ACK ngay khi nhận

---

### 🔌 API Design · 20 phút
> [📖](system-design/06_api_design/ly_thuyet.md)

- **REST**: stateless, resource-based URL, HTTP methods có nghĩa (GET = safe + idempotent)
- **GraphQL**: client chọn field cần — tránh over-fetch/under-fetch — phức tạp hơn ở server
- **gRPC**: binary (protobuf), bidirectional streaming, tốt cho internal service-to-service
- **Idempotency**: PUT/DELETE idempotent, POST không — implement bằng idempotency key
- **Rate limiting**: Token Bucket (burst OK) vs Leaky Bucket (smooth) vs Fixed Window (simple)
- **Versioning**: URL `/v1/`, Header `Accept: application/vnd.api+json;version=1`, Subdomain

---

### 🧩 Microservices · 20 phút
> [📖](system-design/07_microservices/ly_thuyet.md)

- **Service Discovery**: Consul/Eureka — service register, client lookup
- **API Gateway**: single entry point — auth, rate limit, routing, SSL termination
- **Circuit Breaker**: fail fast khi downstream service lỗi — tránh cascade failure
  - States: Closed → Open (khi error rate cao) → Half-Open (thử lại)
- **Saga pattern**: distributed transaction — choreography (event-driven) vs orchestration (central coordinator)
- Bẫy: microservices không phải magic — overhead network, distributed tracing, harder debugging

---

### ⚖️ Consistency & Availability · 25 phút
> [📖](system-design/08_consistency_availability/ly_thuyet.md)

- **CAP Theorem**: khi có network partition → chọn CP (consistency) hoặc AP (availability)
  - CP: MongoDB, HBase, ZooKeeper
  - AP: Cassandra, DynamoDB, CouchDB
- **ACID**: Atomicity + Consistency + Isolation + Durability — SQL databases
- **BASE**: Basically Available + Soft state + Eventual consistency — NoSQL
- **Isolation levels** (từ yếu đến mạnh):
  ```
  Read Uncommitted → Read Committed → Repeatable Read → Serializable
  ```
- **Quorum**: W + R > N → strong consistency (N=3, W=2, R=2 phổ biến)
- **Eventual consistency**: system sẽ nhất quán *cuối cùng* — acceptable cho social feed, DNS

---

## 13:00 – 13:30 · Điểm yếu & Câu hỏi còn mơ

```
  Dùng 30 phút cuối để:
  1. Xem lại những topic đánh dấu "chưa chắc"
  2. Ôn lại bất kỳ complexity nào còn nhầm lẫn
  3. Đọc lướt câu hỏi phỏng vấn trong trac_nghiem.md của topic yếu nhất
```

---

## Quick Reference — Complexity tổng hợp

```
  Cấu trúc dữ liệu          Access    Search    Insert    Delete    Space
  ─────────────────────────────────────────────────────────────────────────
  Array                      O(1)      O(n)      O(n)      O(n)      O(n)
  Linked List                O(n)      O(n)      O(1)*     O(1)*     O(n)
  HashMap                    —         O(1)**    O(1)**    O(1)**    O(n)
  Stack / Queue              O(n)      O(n)      O(1)      O(1)      O(n)
  BST (balanced)             O(log n)  O(log n)  O(log n)  O(log n)  O(n)
  Heap                       O(1)***   O(n)      O(log n)  O(log n)  O(n)
  Trie                       O(L)      O(L)      O(L)      O(L)      O(n·L)

  * với known pointer  ** average case  *** chỉ min/max

  Thuật toán                 Best        Average     Worst     Space
  ─────────────────────────────────────────────────────────────────
  Quick Sort                 O(n log n)  O(n log n)  O(n²)     O(log n)
  Merge Sort                 O(n log n)  O(n log n)  O(n log n) O(n)
  Heap Sort                  O(n log n)  O(n log n)  O(n log n) O(1)
  Binary Search              O(1)        O(log n)    O(log n)  O(1)
  BFS / DFS                  —           O(V+E)      O(V+E)    O(V)
  Dijkstra                   —           O((V+E)logV) —        O(V)
  Dynamic Programming        —           depends     depends   depends
```
