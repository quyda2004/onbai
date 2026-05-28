# Câu Hỏi Tìm Hiểu — Ôn Toàn Bộ

> Mỗi câu = 1 khái niệm cần hiểu rõ. **Đừng đọc xuôi** — hãy đọc từng câu, tự trả lời, nếu không trả lời được thì mở tongquat/ly_thuyet tra.

---

# DSA

## Big O Notation
> [📖](dsa/01_bigo/ly_thuyet.md)

- Big O Notation là gì? Dùng để đo lường cái gì?
- Sự khác nhau giữa Time Complexity và Space Complexity là gì?
- Tại sao ta bỏ hệ số và bậc thấp hơn khi viết Big O? (ví dụ: O(2n) → O(n))
- O(1) nghĩa là gì trong thực tế? Cho ví dụ.
- O(log n) xảy ra khi nào? Tại sao Binary Search lại là O(log n)?
- O(n log n) xuất hiện ở đâu? Tại sao đây là giới hạn tốt nhất của comparison-based sort?
- Amortized complexity là gì? Tại sao dynamic array append là O(1) amortized?
- Worst case, Average case, Best case khác nhau thế nào? Khi nào cần phân biệt?
- Space complexity tính như thế nào với hàm đệ quy? Call stack tốn bao nhiêu?
- Đệ quy với 2 nhánh (như Fibonacci naive) có complexity là bao nhiêu? Tại sao?

---

## Array & String
> [📖](dsa/02_array_string/ly_thuyet.md)

- Array lưu dữ liệu trong bộ nhớ như thế nào? Tại sao access O(1)?
- Dynamic array (Python list, Java ArrayList) khác static array ở chỗ nào?
- Tại sao insert/delete ở giữa array lại O(n)?
- String trong Python có immutable không? Điều đó ảnh hưởng gì đến performance khi concat?
- Tại sao `"".join(list)` nhanh hơn `s += "..."` trong loop?
- Subarray là gì? Subsequence là gì? Hai cái này khác nhau ở đâu?
- Off-by-one error là gì? Hay xảy ra ở đâu trong bài array?
- In-place operation là gì? Khi nào nên dùng in-place thay vì tạo array mới?
- Làm sao swap 2 phần tử mà không cần biến temp trong Python?

---

## HashMap & HashSet
> [📖](dsa/03_hashmap_hashset/ly_thuyet.md)

- HashMap/HashSet hoạt động như thế nào bên trong? Hash function làm gì?
- Collision là gì? Có bao nhiêu cách xử lý collision phổ biến?
- Tại sao search trong HashMap là O(1) average nhưng O(n) worst case?
- Load factor là gì? Ảnh hưởng gì đến performance?
- Khi nào HashMap rehash? Điều đó tốn bao nhiêu thời gian?
- Key trong HashMap phải thỏa mãn điều kiện gì? Tại sao list không dùng làm key được?
- HashSet khác HashMap ở chỗ nào? Khi nào dùng Set thay Map?
- `defaultdict` và `Counter` trong Python là gì? Dùng khi nào?
- Pattern "two-pass hashmap" là gì? Áp dụng vào bài toán nào?
- Khi nào nên đổi time lấy space bằng HashMap?

---

## Two Pointers
> [📖](dsa/04_two_pointers/ly_thuyet.md)

- Two Pointers là gì? Ý tưởng cốt lõi là gì?
- Có bao nhiêu dạng Two Pointers? Kể tên và mô tả từng dạng.
- Tại sao Two Pointers thường cần mảng đã sort?
- Fast/Slow pointer dùng để giải quyết vấn đề gì trên Linked List?
- Floyd's Cycle Detection Algorithm hoạt động như thế nào?
- Bài "Two Sum" trên mảng đã sort giải bằng Two Pointers như thế nào?
- Điều kiện dừng vòng lặp `while left < right` — tại sao là `<` không phải `<=`?
- Two Pointers cải thiện complexity từ bao nhiêu xuống bao nhiêu (so với brute force)?

---

## Sliding Window
> [📖](dsa/05_sliding_window/ly_thuyet.md)

- Sliding Window là gì? Khác gì so với Two Pointers thông thường?
- Fixed-size window khác Variable-size window thế nào? Cho ví dụ mỗi loại.
- Template chuẩn của Variable Sliding Window trông như thế nào?
- Khi nào expand window (tăng right)? Khi nào shrink window (tăng left)?
- Tại sao Sliding Window thường O(n) thay vì O(n²)?
- Khi nào nên dùng Sliding Window thay vì Prefix Sum?
- Monotonic Deque là gì? Dùng để làm gì trong bài Sliding Window Maximum?

---

## Prefix Sum
> [📖](dsa/06_prefix_sum/ly_thuyet.md)

- Prefix Sum array là gì? Xây dựng như thế nào?
- Công thức tính sum từ index l đến r dùng prefix sum là gì?
- Tại sao dùng prefix sum thay vì tính trực tiếp mỗi lần hỏi?
- Bài toán "đếm subarray có sum = k" giải bằng prefix sum + hashmap như thế nào?
- 2D Prefix Sum là gì? Công thức tính sum của một vùng chữ nhật?
- Prefix Sum và Difference Array khác nhau thế nào? Dùng cái nào khi nào?

---

## Recursion
> [📖](dsa/07_recursion/ly_thuyet.md)

- Recursion là gì? Base case và recursive case là gì?
- Tại sao quên base case lại gây ra stack overflow?
- Memoization là gì? Khi nào nên dùng memo trong đệ quy?
- Tail recursion là gì? Python có tối ưu tail recursion không?
- Recurrence relation là gì? T(n) = 2T(n/2) + O(n) giải ra bao nhiêu?
- Master Theorem dùng để làm gì?
- Đệ quy và iteration có thể thay thế nhau không? Trade-off là gì?
- Tại sao khi viết đệ quy trả kết quả từ nhánh con, hay quên `return`?

---

## Stack & Queue
> [📖](dsa/08_stack_queue/ly_thuyet.md)

- Stack là gì? LIFO nghĩa là gì? Ứng dụng thực tế nào dùng Stack?
- Queue là gì? FIFO nghĩa là gì? Ứng dụng thực tế nào dùng Queue?
- Deque là gì? Khác Stack và Queue ở chỗ nào? Khi nào dùng Deque?
- Monotonic Stack là gì? Dùng để giải quyết bài toán nào?
- Tại sao `list.pop(0)` trong Python là O(n)? Dùng gì thay thế?
- BFS dùng Queue, DFS dùng Stack — tại sao?
- Min Stack (stack hỗ trợ getMin O(1)) cài đặt như thế nào?
- Next Greater Element bài toán giải bằng Monotonic Stack như thế nào?

---

## Linked List
> [📖](dsa/09_linked_list/ly_thuyet.md)

- Linked List là gì? Khác Array ở chỗ nào?
- Singly vs Doubly Linked List — khi nào dùng loại nào?
- Tại sao insert/delete ở đầu/cuối Linked List là O(1) nhưng ở giữa là O(n)?
- Dummy node (sentinel node) là gì? Giải quyết edge case nào?
- Floyd's Cycle Detection: fast pointer đi 2 bước, slow đi 1 bước — tại sao chúng gặp nhau khi có cycle?
- Tìm middle node của Linked List bằng fast/slow pointer như thế nào?
- Reverse Linked List in-place: cần những biến nào? Thứ tự update như thế nào?
- Tại sao phải lưu `next = curr.next` trước khi đứt link?
- Merge 2 sorted Linked Lists — độ phức tạp là bao nhiêu?

---

## Tree & BST
> [📖](dsa/10_tree_bst/ly_thuyet.md)

- Tree là gì? Root, Node, Leaf, Edge, Height, Depth là gì?
- Binary Tree là gì? BST (Binary Search Tree) thêm điều kiện gì so với Binary Tree?
- 3 loại DFS traversal (Inorder, Preorder, Postorder) khác nhau thế nào? Dùng khi nào?
- Tại sao Inorder traversal của BST cho ra mảng đã sort?
- BST search/insert/delete có complexity là bao nhiêu? Khi nào bị O(n)?
- Cây BST bị degenerate là gì? Xảy ra khi nào? Giải pháp?
- Balanced BST là gì? AVL Tree và Red-Black Tree giải quyết vấn đề gì?
- LCA (Lowest Common Ancestor) là gì? Tìm LCA trong BST khác gì trong Binary Tree thông thường?
- Height của tree vs Depth của node khác nhau thế nào?
- Tính height của tree bằng đệ quy — logic là gì?
- Level-order traversal (BFS trên tree) cài đặt như thế nào?

---

## Heap
> [📖](dsa/11_heap/ly_thuyet.md)

- Heap là gì? Min-heap và Max-heap khác nhau thế nào?
- Heap property là gì? Tại sao Heap không phải là sorted array?
- Heap được cài đặt bằng array như thế nào? Index của parent/left/right child tính thế nào?
- `heapify` làm gì? Tại sao O(n) không phải O(n log n)?
- `heappush` và `heappop` có complexity là bao nhiêu? Tại sao?
- Python `heapq` là min-heap hay max-heap? Muốn max-heap thì làm thế nào?
- Bài "Top K Largest Elements" giải bằng Heap như thế nào? Dùng min hay max heap?
- Priority Queue là gì? Quan hệ với Heap như thế nào?
- K-way Merge problem giải bằng Heap như thế nào?

---

## Trie
> [📖](dsa/13_trie/ly_thuyet.md)

- Trie là gì? Cấu trúc node của Trie trông như thế nào?
- Trie dùng để giải quyết bài toán nào? Ưu điểm so với HashMap?
- Insert, Search, StartsWith trong Trie có complexity là bao nhiêu?
- `is_end` flag trong Trie dùng để làm gì? Quên set nó gây ra lỗi gì?
- Compressed Trie (Radix Tree) là gì? Cải thiện gì so với Trie thông thường?
- Trie dùng khi nào trong thực tế? (autocomplete, spell check, routing...)

---

## Graph
> [📖](dsa/12_graph/ly_thuyet.md)

- Graph là gì? Directed vs Undirected khác nhau thế nào?
- Weighted vs Unweighted Graph — khi nào cần trọng số?
- Adjacency List vs Adjacency Matrix — space và time complexity từng cái?
- Khi nào dùng Adjacency List, khi nào dùng Matrix?
- Sparse graph vs Dense graph là gì?
- In-degree và Out-degree của một node là gì?
- Connected Components là gì? Strongly Connected Components (SCC) là gì?
- Union-Find (Disjoint Set Union) là gì? Dùng để làm gì?
- Path compression trong Union-Find là gì? Cải thiện complexity thế nào?
- Dijkstra's Algorithm dùng khi nào? Có dùng được với negative weight không?
- Bellman-Ford dùng khi nào? Khác Dijkstra thế nào?

---

## DFS & BFS
> [📖](dsa/16_dfs_bfs/ly_thuyet.md)

- DFS và BFS là gì? Ý tưởng cốt lõi của mỗi thuật toán?
- DFS dùng Stack, BFS dùng Queue — tại sao?
- Khi nào dùng BFS, khi nào dùng DFS?
- Tại sao BFS tìm shortest path trong unweighted graph?
- Visited set dùng để làm gì? Tại sao phải add vào visited khi **push** vào queue, không phải khi pop?
- Nếu quên visited, điều gì xảy ra với graph có cycle?
- Complexity của DFS và BFS là bao nhiêu? (tính theo V và E)
- DFS trên tree khác DFS trên graph thế nào? (cần visited không?)
- BFS cho level-order traversal trên tree cài đặt như thế nào?

---

## Binary Search
> [📖](dsa/14_binary_search/ly_thuyet.md)

- Binary Search yêu cầu điều kiện gì với input?
- Tại sao `mid = (left + right) // 2` có thể gây overflow? Viết cách nào an toàn hơn?
- Template `while left <= right` khác `while left < right` thế nào? Khi nào dùng cái nào?
- Tại sao `right = mid - 1` và `left = mid + 1` thay vì `right = mid` và `left = mid`?
- "Search space ẩn" là gì? Cho ví dụ bài toán binary search trên answer.
- Binary Search biến thể: tìm leftmost/rightmost index thỏa điều kiện — cài đặt thế nào?
- Complexity của Binary Search là bao nhiêu? Tại sao?

---

## Sorting
> [dsa/15_sorting hoặc REVIEW_1_5_DAYS.md]

- Quick Sort hoạt động như thế nào? Pivot chọn thế nào?
- Tại sao Quick Sort O(n²) worst case? Khi nào worst case xảy ra?
- Merge Sort hoạt động như thế nào? Tại sao O(n) space?
- Merge Sort stable, Quick Sort không stable — stable nghĩa là gì?
- Heap Sort dùng Heap như thế nào? Tại sao O(1) space?
- Counting Sort và Radix Sort dùng khi nào? Điều kiện là gì?
- Python `sort()` dùng thuật toán gì? Tại sao O(n) best case?
- In-place sorting là gì? Sort nào in-place, sort nào không?
- Khi nào chọn Merge Sort, khi nào Quick Sort?

---

## Topological Sort
> [📖](dsa/17_topological_sort/ly_thuyet.md)

- Topological Sort là gì? Áp dụng được trên loại graph nào?
- DAG là gì? Tại sao Topological Sort chỉ dùng cho DAG?
- Kahn's Algorithm (BFS-based) hoạt động như thế nào? In-degree là gì?
- DFS-based Topological Sort hoạt động như thế nào?
- Làm sao phát hiện cycle bằng Topological Sort (Kahn's)?
- Topological Sort dùng trong thực tế ở đâu? (build system, course schedule...)

---

## Greedy
> [📖](dsa/18_greedy/ly_thuyet.md)

- Greedy Algorithm là gì? Ý tưởng cốt lõi?
- Greedy Choice Property là gì?
- Optimal Substructure là gì?
- Tại sao Greedy không phải lúc nào cũng đúng? Cho ví dụ Greedy sai.
- Exchange Argument dùng để chứng minh Greedy đúng như thế nào?
- Interval Scheduling problem giải bằng Greedy như thế nào? Tại sao sort by end time?
- Greedy khác DP thế nào?

---

## Dynamic Programming
> [📖](dsa/19_dynamic_programming/ly_thuyet.md)

- Dynamic Programming là gì? Hai điều kiện để áp dụng DP?
- Optimal Substructure và Overlapping Subproblems là gì?
- Top-down (Memoization) và Bottom-up (Tabulation) khác nhau thế nào? Trade-off?
- Khi nào dùng Top-down, khi nào Bottom-up?
- 1D DP, 2D DP, Interval DP, Bitmask DP — mỗi dạng áp dụng vào bài toán nào?
- State trong DP là gì? Cách xác định state?
- Thứ tự tính toán (computation order) quan trọng thế nào trong Bottom-up DP?
- Knapsack 0/1 là gì? Cài đặt 2D và tối ưu xuống 1D như thế nào?
- LCS (Longest Common Subsequence) vs LIS (Longest Increasing Subsequence) — DP state là gì?
- Edit Distance bài toán DP state transition thế nào?

---

## Backtracking
> [📖](dsa/20_backtracking/ly_thuyet.md)

- Backtracking là gì? Khác brute force ở điểm nào?
- Template chuẩn của Backtracking trông như thế nào?
- Tại sao phải "undo" choice sau khi đệ quy? Nếu quên undo thì sao?
- Pruning là gì? Tại sao Pruning quan trọng trong Backtracking?
- Tại sao phải `result.append(state[:])` chứ không phải `result.append(state)`?
- Permutation, Combination, Subset — bài nào dùng Backtracking? Điểm khác nhau trong cài đặt?
- Backtracking vs DP: khi nào dùng cái nào?

---

# OOP

## Class & Object
> [📖](oop/01_class_object/ly_thuyet.md)

- Class là gì? Object là gì? Quan hệ giữa chúng?
- `__init__` trong Python làm gì? Khác `__new__` thế nào?
- Instance variable và Class variable khác nhau thế nào? Cho ví dụ lỗi hay gặp.
- Instance method, Class method (`@classmethod`), Static method (`@staticmethod`) khác nhau thế nào?
- `self` trong Python là gì? Tại sao phải truyền `self`?
- Mutable default argument trong `__init__` gây ra lỗi gì?
- `__str__` và `__repr__` khác nhau thế nào? Cái nào dùng để debug?

---

## Encapsulation
> [📖](oop/02_encapsulation/ly_thuyet.md)

- Encapsulation là gì? Giải quyết vấn đề gì?
- Access modifiers trong Python (public, protected `_`, private `__`) hoạt động như thế nào?
- Name mangling là gì? `__var` thực ra được lưu dưới tên gì?
- `@property` và `@setter` dùng để làm gì? Tại sao cần chúng?
- Getter/Setter có lúc nào không cần không? Khi nào thì cần?
- Data hiding khác Encapsulation thế nào?

---

## Inheritance
> [📖](oop/03_inheritance/ly_thuyet.md)

- Inheritance là gì? IS-A relationship là gì?
- `super()` trong Python dùng để làm gì? Hoạt động như thế nào với MRO?
- MRO (Method Resolution Order) là gì? Python dùng thuật toán gì để tính MRO?
- Multiple Inheritance là gì? Vấn đề "Diamond Problem" là gì? Python giải quyết thế nào?
- Method overriding là gì? Khác method overloading thế nào?
- Tại sao đôi khi không nên dùng Inheritance? "Composition over Inheritance" nghĩa là gì?
- Cooperative multiple inheritance là gì? Tại sao phải gọi `super().__init__()` theo cách đặc biệt?

---

## Polymorphism
> [📖](oop/04_polymorphism/ly_thuyet.md)

- Polymorphism là gì? Tại sao nó hữu ích?
- Runtime polymorphism (dynamic dispatch) là gì? Xảy ra như thế nào?
- Compile-time polymorphism (method overloading) là gì? Python có hỗ trợ natively không?
- Duck typing là gì? Tại sao Python ưu tiên duck typing?
- Virtual method (virtual table) là gì? Java/C++ cài đặt polymorphism thế nào?

---

## Abstraction
> [📖](oop/05_abstraction/ly_thuyet.md)

- Abstraction là gì? Khác Encapsulation ở chỗ nào?
- Abstract class trong Python cài đặt thế nào?
- `@abstractmethod` làm gì? Nếu subclass không implement thì sao?
- Abstraction giúp ích gì trong thiết kế hệ thống lớn?

---

## Interface & Abstract Class
> [📖](oop/06_interface_abstract/ly_thuyet.md)

- Interface là gì? Python không có `interface` keyword — mô phỏng thế nào?
- Abstract Class vs Interface — điểm giống và khác nhau?
- Khi nào dùng Abstract Class, khi nào dùng Interface?
- Dependency Injection là gì? Tại sao inject interface thay vì concrete class?
- "Program to an interface, not an implementation" nghĩa là gì?

---

## SOLID Principles
> [📖](oop/07_solid_principles/ly_thuyet.md)

- Single Responsibility Principle (SRP) — vi phạm thế nào? Cách nhận biết?
- Open/Closed Principle (OCP) — "open for extension, closed for modification" nghĩa là gì thực tế?
- Liskov Substitution Principle (LSP) — subclass "yếu hóa precondition" nghĩa là gì?
- Interface Segregation Principle (ISP) — tại sao interface to lại xấu?
- Dependency Inversion Principle (DIP) — module high-level không nên depend trực tiếp vào module low-level — tại sao?
- SOLID có phải áp dụng tất cả mọi lúc không? Trade-off?

---

## Design Patterns
> [📖](oop/08_design_patterns/ly_thuyet.md)

- Design Pattern là gì? Tại sao cần học chúng?
- Creational, Structural, Behavioral — 3 loại pattern này khác nhau thế nào?
- **Singleton**: dùng để làm gì? Thread-safe Singleton cài đặt thế nào?
- **Factory Method**: khác gì Abstract Factory?
- **Builder**: giải quyết vấn đề gì? Telescoping constructor là gì?
- **Observer**: Subject và Observer là gì? Ví dụ thực tế nào dùng Observer?
- **Strategy**: inject algorithm nghĩa là gì? Cho ví dụ thực tế.
- **Decorator** (OOP): wrap object cùng interface là gì? Khác Python `@decorator` (function decorator) thế nào?
- **Adapter**: dùng để làm gì? Khi nào cần Adapter?

---

# OS

## Process & Thread
> [📖](os/01_process_thread/ly_thuyet.md)

- Process là gì? Thread là gì?
- Process và Thread khác nhau về memory space như thế nào?
- PCB (Process Control Block) chứa thông tin gì?
- Context switch là gì? Tại sao tốn kém?
- Tại sao crash 1 thread có thể kill cả process?
- `fork()` làm gì? Parent và child process sau `fork()` trả về gì?
- `fork()` + `exec()` dùng để làm gì? Tại sao không tạo process trực tiếp?
- Process states (new, ready, running, waiting, terminated) — mô tả transition giữa các states?
- Zombie process là gì? Orphan process là gì?
- Multi-threading vs Multi-processing — khi nào dùng cái nào?

---

## Memory Management
> [📖](os/02_memory_management/ly_thuyet.md)

- Virtual memory là gì? Tại sao cần virtual memory?
- Page và Page frame là gì? Page table dùng để làm gì?
- MMU (Memory Management Unit) làm gì?
- TLB (Translation Lookaside Buffer) là gì? Tại sao cần TLB?
- Page fault là gì? Khi nào xảy ra? OS xử lý page fault thế nào?
- Thrashing là gì? Tại sao xảy ra? Cách phòng tránh?
- Internal fragmentation và External fragmentation là gì?
- Paging vs Segmentation — ưu nhược điểm từng cái?
- Memory leak là gì? Ảnh hưởng gì đến hệ thống theo thời gian?
- Heap vs Stack trong memory model của process — cái nào tự quản lý, cái nào thủ công?

---

## CPU Scheduling
> [📖](os/03_scheduling/ly_thuyet.md)

- CPU Scheduling là gì? Scheduler làm gì?
- Preemptive vs Non-preemptive scheduling — khác nhau thế nào?
- FCFS: convoy effect là gì? Tại sao FCFS có convoy effect?
- SJF: tại sao tối ưu average waiting time? Vấn đề gì của SJF?
- Round Robin: quantum size ảnh hưởng thế nào đến performance?
- Priority Scheduling: starvation là gì? Aging giải quyết starvation thế nào?
- Turnaround time, Waiting time, Response time — định nghĩa và công thức tính?
- Multilevel Queue scheduling là gì?

---

## Deadlock
> [📖](os/04_deadlock/ly_thuyet.md)

- Deadlock là gì? Khi nào xảy ra?
- 4 điều kiện Coffman là gì? Giải thích từng điều kiện.
- Phá vỡ điều kiện nào trong 4 điều kiện là dễ nhất trong thực tế?
- Deadlock Prevention, Deadlock Avoidance, Deadlock Detection — khác nhau thế nào?
- Banker's Algorithm là gì? Làm gì để tránh deadlock?
- Resource Allocation Graph là gì? Đọc graph thế nào để phát hiện deadlock?
- Deadlock vs Livelock vs Starvation — ba cái này khác nhau thế nào?

---

## File System
> [📖](os/05_file_system/ly_thuyet.md)

- inode là gì? Lưu thông tin gì? Tại sao không chứa tên file?
- Hard link và Soft link (Symbolic link) khác nhau thế nào?
- Tại sao xóa file nhưng có hard link thì file vẫn còn?
- FAT, ext4, NTFS — điểm khác nhau cơ bản?
- Journaling trong file system là gì? Giúp ích gì?
- File permission trong Linux (rwx) đọc như thế nào? `chmod 755` nghĩa là gì?

---

## Synchronization
> [📖](os/06_synchronization/ly_thuyet.md)

- Race condition là gì? Cho ví dụ cụ thể.
- Critical section là gì? Mutual exclusion là gì?
- Mutex là gì? Cách hoạt động? Tại sao chỉ owner mới unlock được?
- Semaphore là gì? Counting semaphore khác binary semaphore thế nào?
- Monitor là gì? Java `synchronized` block là ví dụ của Monitor không?
- Spinlock là gì? Khi nào nên dùng, khi nào không?
- Condition variable là gì? `wait()` và `signal()` làm gì?
- Producer-Consumer problem giải bằng Semaphore thế nào?
- Reader-Writer problem là gì? Giải pháp?

---

# Network

## OSI Model
> [📖](network/01_osi_model/ly_thuyet.md)

- OSI model có bao nhiêu tầng? Mỗi tầng làm gì?
- TCP/IP model có bao nhiêu tầng? So sánh với OSI?
- Encapsulation trong networking là gì? Mỗi tầng thêm gì vào packet?
- PDU (Protocol Data Unit) của mỗi tầng gọi là gì? (bit, frame, packet, segment...)
- Tầng nào xử lý IP address? Tầng nào xử lý MAC address?
- Router hoạt động ở tầng nào? Switch? Hub?

---

## TCP/IP
> [📖](network/02_tcp_ip/ly_thuyet.md)

- TCP 3-way handshake diễn ra thế nào? Tại sao cần 3 bước?
- TCP 4-way termination diễn ra thế nào?
- TCP vs UDP — khi nào dùng cái nào? Cho ví dụ ứng dụng thực tế.
- Flow control trong TCP là gì? Sliding window hoạt động thế nào?
- Congestion control trong TCP là gì? Slow start là gì?
- TIME_WAIT state là gì? Tại sao cần?
- Tại sao TCP không đảm bảo message boundary?
- IP address là gì? IPv4 vs IPv6?
- Subnet mask và CIDR notation là gì? `/24` nghĩa là bao nhiêu hosts?

---

## HTTP/HTTPS
> [📖](network/03_http_https/ly_thuyet.md)

- HTTP là gì? Stateless nghĩa là gì?
- HTTP methods: GET, POST, PUT, DELETE, PATCH — mỗi cái dùng khi nào?
- Idempotent method là gì? Method nào idempotent, method nào không?
- Safe method là gì? GET có safe không?
- HTTP/1.0 vs HTTP/1.1 vs HTTP/2 vs HTTP/3 — điểm cải tiến chính mỗi version?
- Head-of-line blocking là gì? HTTP/2 giải quyết thế nào? HTTP/3 giải quyết thế nào?
- QUIC là gì? Tại sao HTTP/3 dùng UDP?
- HTTPS khác HTTP thế nào? TLS handshake diễn ra thế nào?
- Status code: 301 vs 302, 401 vs 403, 429 vs 503 — phân biệt?
- CORS là gì? Ai enforce CORS? Tại sao không phải security thực sự?
- Cookie vs Session vs JWT — mỗi cái dùng để làm gì?

---

## DNS
> [📖](network/04_dns/ly_thuyet.md)

- DNS là gì? Giải quyết vấn đề gì?
- Quá trình resolve DNS từ đầu đến cuối diễn ra thế nào?
- Recursive query và Iterative query khác nhau thế nào?
- DNS caching hoạt động thế nào? TTL là gì?
- Các loại DNS record: A, AAAA, CNAME, MX, NS, TXT — mỗi loại dùng cho gì?
- Tại sao không dùng CNAME cho apex domain (root domain)?
- DNS over HTTPS (DoH) là gì? Giải quyết vấn đề gì?
- DNS poisoning (cache poisoning) là gì?

---

## Socket
> [📖](network/05_socket/ly_thuyet.md)

- Socket là gì? Một socket được xác định bởi những thông tin gì?
- TCP socket và UDP socket khác nhau thế nào khi sử dụng?
- Blocking socket và Non-blocking socket khác nhau thế nào?
- `select()`, `poll()`, `epoll()` là gì? Tại sao epoll tốt hơn select?
- Server socket lắng nghe connection bằng cách nào? (`bind`, `listen`, `accept`)
- WebSocket là gì? Khác HTTP long-polling thế nào?

---

## Security
> [📖](network/06_security/ly_thuyet.md)

- Symmetric encryption và Asymmetric encryption khác nhau thế nào?
- TLS dùng cả 2 loại encryption như thế nào?
- Certificate (chứng chỉ SSL/TLS) là gì? CA (Certificate Authority) là gì?
- MITM (Man-in-the-Middle) attack là gì? TLS ngăn chặn thế nào?
- XSS (Cross-Site Scripting) là gì? Cách phòng chống?
- CSRF (Cross-Site Request Forgery) là gì? CSRF token hoạt động thế nào?
- SQL Injection là gì? Prepared statement ngăn chặn thế nào?
- Hashing (bcrypt, SHA) dùng để làm gì? Khác encryption thế nào?

---

# System Design

## Scalability
> [📖](system-design/01_scalability/ly_thuyet.md)

- Scalability là gì? Tại sao cần?
- Vertical scaling và Horizontal scaling khác nhau thế nào? Trade-off?
- Tại sao horizontal scaling yêu cầu stateless service?
- Bottleneck là gì? CPU-bound, I/O-bound, Memory-bound — cách xử lý khác nhau thế nào?
- CAP theorem liên quan thế nào đến scalability?

---

## Load Balancing
> [📖](system-design/02_load_balancing/ly_thuyet.md)

- Load Balancer là gì? Làm gì?
- L4 Load Balancer và L7 Load Balancer khác nhau thế nào?
- Round Robin, Weighted Round Robin, Least Connections, IP Hash — mỗi cái hoạt động thế nào?
- Sticky session (session affinity) là gì? Vấn đề khi dùng sticky session?
- Health check trong Load Balancer là gì?
- Active-Active vs Active-Passive HA setup cho Load Balancer là gì?

---

## Caching
> [📖](system-design/03_caching/ly_thuyet.md)

- Caching là gì? Dùng để giải quyết vấn đề gì?
- Cache hit và Cache miss là gì? Cache hit rate tốt là bao nhiêu?
- Cache-aside, Write-through, Write-behind — mỗi cái hoạt động thế nào? Trade-off?
- Read-through cache là gì?
- Eviction policy: LRU, LFU, FIFO, TTL — khi nào dùng cái nào?
- LRU cache cài đặt thế nào để đạt O(1) cho cả get và put?
- Cache stampede (thundering herd) là gì? Cách phòng tránh?
- CDN là gì? Dùng để làm gì? Khác server-side cache thế nào?
- Redis vs Memcached — điểm khác nhau chính?

---

## Database
> [📖](system-design/04_database/ly_thuyet.md)

- SQL vs NoSQL — điểm khác nhau cốt lõi? Khi nào dùng cái nào?
- ACID là gì? Giải thích từng chữ.
- Transaction là gì? Commit và Rollback là gì?
- Database Index là gì? Tại sao Index giúp tìm kiếm nhanh hơn?
- B-tree index và Hash index khác nhau thế nào? Range query dùng cái nào?
- Composite index là gì? Thứ tự columns trong composite index quan trọng thế nào?
- Database sharding là gì? Consistent hashing dùng trong sharding thế nào?
- Replication là gì? Master-Slave vs Master-Master — khi nào dùng cái nào?
- N+1 query problem là gì? Cách giải quyết?
- Connection pool là gì? Tại sao cần?
- Normalization là gì? Khi nào denormalize?

---

## Message Queue
> [📖](system-design/05_message_queue/ly_thuyet.md)

- Message Queue là gì? Giải quyết vấn đề gì?
- Producer và Consumer là gì?
- Tại sao dùng Message Queue thay vì gọi trực tiếp (synchronous call)?
- Kafka và RabbitMQ khác nhau thế nào? Khi nào dùng cái nào?
- At-least-once, At-most-once, Exactly-once delivery là gì? Cái nào khó implement nhất?
- Idempotency trong Message Queue là gì? Tại sao quan trọng?
- Dead Letter Queue (DLQ) là gì?
- Consumer group trong Kafka là gì?
- Message ordering đảm bảo thế nào?

---

## API Design
> [📖](system-design/06_api_design/ly_thuyet.md)

- REST là gì? 6 constraints của REST là gì?
- RESTful API: resource-based URL nghĩa là gì? Cho ví dụ URL tốt và xấu.
- HTTP methods mapping với CRUD như thế nào?
- GraphQL là gì? Giải quyết vấn đề gì mà REST không giải quyết được?
- Over-fetching và Under-fetching là gì?
- gRPC là gì? Dùng khi nào thay vì REST?
- API versioning: có những cách nào? Ưu nhược điểm từng cách?
- Rate limiting là gì? Token Bucket và Leaky Bucket hoạt động thế nào?
- API Gateway là gì? Làm gì?
- Idempotency key trong API là gì?

---

## Microservices
> [📖](system-design/07_microservices/ly_thuyet.md)

- Microservices architecture là gì? Khác Monolith thế nào?
- Ưu và nhược điểm của Microservices?
- Service Discovery là gì? Client-side vs Server-side discovery?
- Circuit Breaker pattern là gì? 3 states của Circuit Breaker?
- Saga pattern là gì? Choreography vs Orchestration trong Saga?
- Distributed transaction problem là gì? Tại sao khó?
- Service mesh là gì? (Istio, Linkerd)
- Microservices communication: synchronous (REST/gRPC) vs asynchronous (MQ) — khi nào dùng cái nào?

---

## Consistency & Availability (CAP)
> [📖](system-design/08_consistency_availability/ly_thuyet.md)

- CAP theorem phát biểu gì?
- Consistency, Availability, Partition Tolerance — mỗi cái nghĩa là gì?
- Tại sao không thể có cả 3 cùng lúc?
- CP system và AP system — cho ví dụ database/system mỗi loại.
- ACID và BASE là gì? Cái nào cho SQL, cái nào cho NoSQL?
- Isolation levels: Read Uncommitted, Read Committed, Repeatable Read, Serializable — mỗi level ngăn chặn vấn đề gì?
- Dirty read, Non-repeatable read, Phantom read là gì?
- Eventual consistency là gì? Dùng ở đâu chấp nhận được?
- Quorum là gì? Công thức W + R > N nghĩa là gì?
- Strong consistency vs Eventual consistency — trade-off?

---

# Machine Learning

## Supervised Learning
> [📖](ml/01_linear_regression/ly_thuyet.md) → [📖](ml/04_svm_knn/ly_thuyet.md)

- Supervised Learning là gì? Cần gì để train?
- Regression vs Classification — khác nhau thế nào?
- Linear Regression: cost function là gì? Gradient Descent tối ưu cái gì?
- Logistic Regression: tại sao cần Sigmoid function? Output là gì?
- Decision Tree: cách split node như thế nào? Gini impurity và Information Gain là gì?
- Random Forest: khác Decision Tree thế nào? Bagging là gì?
- SVM: hyperplane là gì? Support vectors là gì? Kernel trick là gì?
- KNN: hoạt động thế nào? Chọn K như thế nào?
- Overfitting và Underfitting nhận biết thế nào?

---

## Unsupervised Learning
> [📖](ml/05_clustering/ly_thuyet.md)

- Unsupervised Learning là gì? Khác Supervised thế nào?
- K-Means Clustering: thuật toán hoạt động thế nào? Chọn K như thế nào?
- DBSCAN: điểm khác K-Means? Core point, border point, noise point là gì?
- PCA (Principal Component Analysis) là gì? Dùng để làm gì?
- Dimensionality reduction tại sao cần thiết?

---

## Bias-Variance & Regularization
> [📖](tongquat_ml.md)

- Bias là gì? Variance là gì? Trade-off giữa chúng?
- Underfitting và Overfitting — cách nhận biết qua train/val loss?
- L1 regularization (Lasso) và L2 regularization (Ridge) hoạt động thế nào? Khác nhau thế nào?
- Dropout là gì? Hoạt động thế nào lúc train và lúc inference?
- Early stopping là gì?
- Cross-validation là gì? K-fold cross-validation hoạt động thế nào?

---

## Metrics & Evaluation
> [📖](tongquat_ml.md)

- Confusion matrix là gì? TP, TN, FP, FN là gì?
- Accuracy tại sao không đủ khi dataset imbalanced?
- Precision và Recall là gì? Khi nào ưu tiên Precision, khi nào Recall?
- F1-score là gì? Khi nào dùng F1 thay vì Accuracy?
- ROC curve và AUC là gì?
- RMSE và MAE — khi nào dùng cái nào?

---

## Neural Network & Deep Learning
> [📖](ml/06_neural_network_backprop/ly_thuyet.md) → [📖](ml/07_cnn_rnn/ly_thuyet.md)

- Neural Network cơ bản gồm những thành phần gì?
- Activation function làm gì? Tại sao cần? ReLU, Sigmoid, Tanh, Softmax — khi nào dùng cái nào?
- Vanishing gradient problem là gì? ReLU giải quyết thế nào?
- Backpropagation là gì? Chain rule áp dụng thế nào?
- Gradient Descent, SGD, Mini-batch GD, Adam — điểm khác nhau?
- Learning rate ảnh hưởng thế nào đến training?
- CNN (Convolutional Neural Network): convolution layer, pooling layer làm gì?
- RNN (Recurrent Neural Network): vấn đề gì? LSTM giải quyết thế nào?
- Transformer: self-attention mechanism hoạt động thế nào?
- Transfer learning là gì? Fine-tuning là gì?

---

# AI

## Search & Planning
> [📖](ai/01_search_algorithms/ly_thuyet.md)

- Uninformed search (BFS, DFS) vs Informed search (A*, Greedy Best-First) — khác nhau thế nào?
- A* algorithm là gì? Heuristic function là gì? Admissible heuristic là gì?
- Tại sao BFS guaranteed optimal trong unweighted graph nhưng A* không luôn optimal?

---

## NLP
> [📖](ai/02_nlp_basics/ly_thuyet.md)

- Tokenization là gì? Tại sao cần?
- TF-IDF là gì? TF là gì, IDF là gì?
- Word2Vec là gì? Tại sao word embedding hữu ích hơn one-hot encoding?
- Language Model là gì? N-gram model hoạt động thế nào?
- BERT và GPT — hai hướng tiếp cận khác nhau thế nào?

---

## Transformer & LLM
> [📖](ai/03_transformer_attention/ly_thuyet.md) → [📖](ai/04_llm_prompting/ly_thuyet.md)

- Attention mechanism là gì? Tại sao quan trọng hơn RNN?
- Self-attention: Query, Key, Value là gì?
- Multi-head attention là gì?
- Positional encoding trong Transformer là gì? Tại sao cần?
- LLM là gì? Pre-training và Fine-tuning khác nhau thế nào?
- Prompt engineering: zero-shot, few-shot, chain-of-thought là gì?
- RAG (Retrieval-Augmented Generation) là gì?
- Hallucination trong LLM là gì?

---

# Linux

## File System & Navigation
> [📖](linux/01_filesystem_navigation/ly_thuyet.md)

- Cấu trúc thư mục Linux: `/`, `/home`, `/etc`, `/var`, `/tmp`, `/usr`, `/bin` — mỗi thư mục chứa gì?
- `ls -la` output mỗi cột nghĩa là gì?
- `find` vs `locate` — khác nhau thế nào?
- Absolute path và relative path khác nhau thế nào?
- `~` và `.` và `..` nghĩa là gì?

---

## File Operations & Permissions
> [📖](linux/02_file_operations/ly_thuyet.md) → [📖](linux/03_permissions_users/ly_thuyet.md)

- `cp`, `mv`, `rm` khác nhau thế nào? `rm -rf` làm gì?
- Hard link và Symbolic link tạo bằng lệnh gì? Khác nhau thế nào?
- Permission string `rwxr-xr--` đọc như thế nào?
- `chmod 755` nghĩa là gì? Octal permission tính thế nào?
- `chown` và `chgrp` dùng để làm gì?
- `umask` là gì? Ảnh hưởng thế nào đến file mới tạo?
- `sudo` vs `su` khác nhau thế nào?

---

## Text Processing
> [📖](linux/04_text_processing/ly_thuyet.md)

- `grep` dùng để làm gì? `-r`, `-i`, `-v`, `-n` flag nghĩa là gì?
- `awk` là gì? `awk '{print $1, $3}'` làm gì?
- `sed` là gì? `sed 's/old/new/g'` làm gì?
- `sort` và `uniq` — dùng cùng nhau như thế nào?
- `cut`, `tr`, `wc` mỗi lệnh dùng để làm gì?
- Pipe `|` hoạt động thế nào? `>`, `>>`, `<`, `2>`, `2>&1` nghĩa là gì?
- `xargs` là gì? Dùng khi nào?

---

## Process Management
> [📖](linux/05_process_management/ly_thuyet.md)

- `ps aux` output đọc thế nào?
- `kill`, `kill -9`, `pkill` — khác nhau thế nào?
- Foreground và background process — `&`, `nohup`, `fg`, `bg`, `jobs`?
- `top` và `htop` hiển thị thông tin gì?
- `df` và `du` — khác nhau thế nào?
- Exit code là gì? `$?` cho biết gì?
- Daemon process là gì?
- `cron` dùng để làm gì? Crontab syntax như thế nào?

---

# Toán

## Toán Rời Rạc
> [📖](tongquat_roi_rac.md)

- Handshaking Lemma phát biểu gì? Ứng dụng thế nào?
- Điều kiện để có Euler Circuit? Điều kiện để có Euler Path?
- Euler Path khác Hamiltonian Path thế nào?
- Bipartite graph là gì? Tại sao bipartite graph không có odd cycle?
- Chromatic number là gì? Bipartite graph có chromatic number là bao nhiêu?
- Planar graph là gì? Euler's formula cho planar graph là gì?
- `a ≡ b (mod m)` nghĩa là gì?
- Fermat's Little Theorem phát biểu gì? Ứng dụng vào competitive programming thế nào?
- Chinese Remainder Theorem (CRT) là gì?
- Equivalence relation cần thỏa 3 tính chất gì? Cho ví dụ.
- DFA và NFA là gì? NFA có mạnh hơn DFA không?

---

## Toán Tổ Hợp
> [📖](tongquat_to_hop.md)

- Quy tắc nhân và Quy tắc cộng — khi nào dùng quy tắc nào?
- Hoán vị không lặp và Hoán vị có lặp — công thức và ví dụ?
- Tổ hợp không lặp và Tổ hợp có lặp — công thức và ví dụ?
- C(n,k) = C(n-1,k-1) + C(n-1,k) — chứng minh thế nào?
- Tam giác Pascal: tổng dòng n là bao nhiêu? Khai triển (a+b)^n thế nào?
- Inclusion-Exclusion Principle: phát biểu với 2 tập, 3 tập?
- Catalan number là gì? Xuất hiện trong bài toán nào?
- Stars and Bars: phân phối n vật vào k hộp (có thể rỗng) — công thức?
- Bài toán "sắp xếp MISSISSIPPI" — công thức gì?

---

## Toán Tư Duy & Logic
> [📖](tongquat_toan_tu_duy.md)

- Bảng chân trị của P→Q: khi nào P→Q = False?
- Contrapositive, Converse, Inverse của P→Q là gì? Cái nào tương đương P→Q?
- Modus Ponens và Modus Tollens là gì?
- Proof by Contradiction: template chuẩn là gì?
- Mathematical Induction: 2 bước là gì? Khi nào dùng strong induction?
- Pigeonhole Principle phát biểu gì? Ứng dụng vào bài toán thế nào?
- XOR có những tính chất gì? Dùng XOR để giải bài "tìm số xuất hiện 1 lần"?
- `n & (n-1)` làm gì? Ứng dụng?
- Popcount (đếm số bit 1) cài đặt bằng `n & (n-1)` thế nào?
- Bit masking để enumerate subsets hoạt động thế nào?

---

## Bảng tổng hợp — Câu hỏi so sánh quan trọng

> Những câu dưới đây hay bị hỏi trong phỏng vấn dạng "phân biệt A và B":

**DSA**
- Array vs Linked List — khi nào dùng cái nào?
- Stack vs Queue — khi nào dùng cái nào?
- BFS vs DFS — khi nào dùng cái nào?
- Greedy vs DP — khi nào dùng cái nào?
- Quick Sort vs Merge Sort — khi nào dùng cái nào?
- Heap vs Sorted Array để tìm K phần tử lớn nhất?

**OOP**
- Abstract Class vs Interface?
- Composition vs Inheritance?
- Override vs Overload?
- `@classmethod` vs `@staticmethod`?

**OS**
- Process vs Thread?
- Mutex vs Semaphore vs Monitor?
- Deadlock vs Livelock vs Starvation?
- Hard link vs Soft link?

**Network**
- TCP vs UDP?
- HTTP/1.1 vs HTTP/2 vs HTTP/3?
- 401 vs 403?
- Cookies vs Sessions vs JWT?

**System Design**
- SQL vs NoSQL?
- Cache-aside vs Write-through vs Write-behind?
- Kafka vs RabbitMQ?
- REST vs GraphQL vs gRPC?
- Vertical scaling vs Horizontal scaling?
- CP vs AP (CAP theorem)?

**ML**
- Supervised vs Unsupervised vs Reinforcement?
- Precision vs Recall — khi nào ưu tiên cái nào?
- L1 vs L2 regularization?
- CNN vs RNN vs Transformer?
