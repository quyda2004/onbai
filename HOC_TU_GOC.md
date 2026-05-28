# Học Từ Gốc — DSA · OOP · OS · Network

> **Format mỗi concept:**
> 1. **Là gì?** — định nghĩa ngắn gọn
> 2. **Sinh ra để giải quyết vấn đề gì?** — context, motivation
> 3. **Công dụng / ứng dụng thực tế?** — dùng ở đâu, khi nào
> 4. **Khi nào KHÔNG dùng?** — trade-off, giới hạn
>
> Đọc từng câu → tự trả lời → nếu bí thì mở tongquat tra.

---

# ═══════════════════════════════
# DSA
# ═══════════════════════════════

---

## Big O Notation

- **Là gì?** Ký hiệu toán học để mô tả tốc độ tăng của thời gian/bộ nhớ theo kích thước input.
- **Sinh ra để giải quyết vấn đề gì?** Cần một cách so sánh hiệu quả của các thuật toán mà không phụ thuộc vào phần cứng hay ngôn ngữ lập trình.
- **Công dụng?** Đánh giá và lựa chọn thuật toán/cấu trúc dữ liệu phù hợp trước khi code.
- **Khi nào KHÔNG đủ?** Big O bỏ qua hằng số — đôi khi O(n²) với n nhỏ nhanh hơn O(n log n) trên thực tế do cache/overhead.

---

## Array

- **Là gì?** Dãy các phần tử cùng kiểu, lưu liên tiếp trong bộ nhớ, truy cập bằng index.
- **Sinh ra để giải quyết vấn đề gì?** Cần lưu trữ nhiều phần tử cùng loại và truy cập ngẫu nhiên nhanh (O(1)) theo vị trí.
- **Công dụng?** Lưu danh sách, làm nền tảng cho các cấu trúc khác (Stack, Queue, Heap, HashMap).
- **Khi nào KHÔNG dùng?** Insert/delete ở giữa thường xuyên (O(n)) → dùng Linked List.

---

## HashMap / HashSet

- **Là gì?** Cấu trúc dữ liệu dùng hash function để ánh xạ key → value, cho phép lookup O(1) trung bình.
- **Sinh ra để giải quyết vấn đề gì?** Array cần biết index → không tra được bằng "tên". Cần cấu trúc tra cứu nhanh theo giá trị bất kỳ.
- **Công dụng?** Đếm tần suất, cache, deduplication, tra cứu nhanh, two-sum pattern.
- **Khi nào KHÔNG dùng?** Cần dữ liệu có thứ tự (sorted) → dùng BST/SortedMap; key không hashable.

---

## Two Pointers

- **Là gì?** Kỹ thuật dùng 2 con trỏ (index) di chuyển trên mảng/chuỗi để tránh vòng lặp lồng nhau.
- **Sinh ra để giải quyết vấn đề gì?** Brute force O(n²) quá chậm — cần tận dụng tính đã sắp xếp hoặc tính đơn điệu để đưa về O(n).
- **Công dụng?** Two Sum sorted, 3 Sum, reverse string, remove duplicates, cycle detection trong linked list.
- **Khi nào KHÔNG dùng?** Mảng chưa sort và không có tính đơn điệu; khi cần xét tất cả cặp (buộc O(n²)).

---

## Sliding Window

- **Là gì?** Kỹ thuật duy trì một "cửa sổ" (subarray liên tiếp) di chuyển qua mảng, cập nhật kết quả dần thay vì tính lại từ đầu.
- **Sinh ra để giải quyết vấn đề gì?** Các bài toán liên quan đến subarray/substring liên tiếp thường O(n²) nếu tính brute force — Sliding Window đưa về O(n).
- **Công dụng?** Max sum subarray k phần tử, longest substring không ký tự lặp, minimum window substring.
- **Khi nào KHÔNG dùng?** Bài toán không cần subarray liên tiếp (dùng DP); cần subsequence không liên tiếp.

---

## Prefix Sum

- **Là gì?** Mảng tích lũy tổng — `prefix[i]` = tổng từ phần tử 0 đến i-1, giúp tính tổng bất kỳ đoạn trong O(1).
- **Sinh ra để giải quyết vấn đề gì?** Tính sum(l, r) ngây thơ = O(n) mỗi query; nếu Q queries = O(Q·n). Prefix Sum precompute 1 lần O(n), sau đó mỗi query O(1).
- **Công dụng?** Range sum query, đếm subarray có sum = k, 2D range sum.
- **Khi nào KHÔNG dùng?** Mảng thay đổi thường xuyên (update nhiều) → dùng Segment Tree/BIT.

---

## Recursion

- **Là gì?** Hàm tự gọi lại chính nó với bài toán con nhỏ hơn cho đến khi đạt base case.
- **Sinh ra để giải quyết vấn đề gì?** Nhiều bài toán có cấu trúc tự tương tự (tree, divide & conquer, permutation) — viết đệ quy tự nhiên và ngắn hơn iteration.
- **Công dụng?** Tree traversal, DFS, backtracking, divide & conquer (merge sort, quick sort), DP top-down.
- **Khi nào KHÔNG dùng?** Input lớn → stack overflow; Python giới hạn ~1000 frames; cần performance tối ưu → dùng iteration + stack tường minh.

---

## Stack

- **Là gì?** Cấu trúc LIFO (Last In First Out) — phần tử thêm vào sau cùng được lấy ra trước.
- **Sinh ra để giải quyết vấn đề gì?** Nhiều vấn đề cần nhớ "trạng thái gần nhất" và undo theo thứ tự ngược — call stack, undo/redo, parsing.
- **Công dụng?** DFS, balanced brackets, undo/redo, function call stack, monotonic stack (next greater element).
- **Khi nào KHÔNG dùng?** Cần truy cập phần tử ở giữa hoặc FIFO → dùng Queue/Deque.

---

## Queue

- **Là gì?** Cấu trúc FIFO (First In First Out) — phần tử vào trước ra trước.
- **Sinh ra để giải quyết vấn đề gì?** Cần xử lý theo thứ tự đến — hàng đợi in, task scheduling, BFS cần duyệt level-by-level.
- **Công dụng?** BFS, task queue, rate limiting buffer, producer-consumer.
- **Khi nào KHÔNG dùng?** Cần truy cập phần tử cuối cùng thêm vào → Stack; cần 2 đầu → Deque.

---

## Linked List

- **Là gì?** Chuỗi các node, mỗi node chứa data và pointer tới node tiếp theo (và trước đó nếu Doubly).
- **Sinh ra để giải quyết vấn đề gì?** Array cần biết kích thước trước và insert/delete giữa tốn O(n) do shift. Linked List cho phép insert/delete O(1) nếu biết vị trí.
- **Công dụng?** Implement Stack/Queue, undo history, LRU cache (kết hợp HashMap), memory allocator.
- **Khi nào KHÔNG dùng?** Cần random access O(1) theo index → Array; cache locality kém hơn Array.

---

## Binary Tree / BST

- **Là gì?** Cây mà mỗi node có tối đa 2 con (Binary Tree). BST thêm điều kiện: left < root < right.
- **Sinh ra để giải quyết vấn đề gì?** Array search O(n); HashMap không hỗ trợ range query hay ordering. BST cho search O(log n) + duy trì thứ tự + range query.
- **Công dụng?** Database index (B-tree), file system, expression parser, priority queue, sorted set.
- **Khi nào KHÔNG dùng?** BST bị degenerate (skewed) → O(n); cần O(1) lookup → HashMap; cần guaranteed O(log n) → AVL/Red-Black Tree.

---

## Heap (Priority Queue)

- **Là gì?** Cây nhị phân hoàn chỉnh thỏa heap property (min-heap: cha ≤ con), cài bằng array. Luôn cho lấy min/max trong O(1).
- **Sinh ra để giải quyết vấn đề gì?** Cần lấy phần tử ưu tiên cao nhất liên tục (min hoặc max) mà không cần sort toàn bộ.
- **Công dụng?** Dijkstra, Prim's MST, Top-K elements, merge K sorted lists, task scheduling, median of stream.
- **Khi nào KHÔNG dùng?** Cần search/access phần tử bất kỳ O(log n) → BST; cần order toàn bộ → sort.

---

## Graph

- **Là gì?** Tập hợp đỉnh (vertices) và cạnh (edges) nối chúng. Có thể directed/undirected, weighted/unweighted.
- **Sinh ra để giải quyết vấn đề gì?** Nhiều vấn đề thực tế có quan hệ nhiều-nhiều không thể biểu diễn bằng tree (mạng xã hội, bản đồ, dependency graph).
- **Công dụng?** Shortest path (Dijkstra, Bellman-Ford), network flow, social network analysis, compiler dependency, map navigation.
- **Khi nào KHÔNG dùng?** Nếu chỉ có quan hệ 1 cha-nhiều con → Tree đơn giản hơn.

---

## DFS (Depth-First Search)

- **Là gì?** Thuật toán duyệt đồ thị/cây bằng cách đi sâu tối đa trước khi quay lui.
- **Sinh ra để giải quyết vấn đề gì?** Cần khám phá tất cả đường đi, tìm connected components, detect cycle, topological sort — không cần tìm đường ngắn nhất.
- **Công dụng?** Backtracking, cycle detection, topological sort, connected components, maze solving.
- **Khi nào KHÔNG dùng?** Cần shortest path trong unweighted graph → BFS; đồ thị rất sâu → stack overflow.

---

## BFS (Breadth-First Search)

- **Là gì?** Thuật toán duyệt đồ thị/cây theo từng level, dùng Queue.
- **Sinh ra để giải quyết vấn đề gì?** DFS không đảm bảo đường ngắn nhất. BFS duyệt level-by-level → tìm được shortest path trong unweighted graph.
- **Công dụng?** Shortest path (unweighted), level-order traversal, word ladder, minimum steps problems.
- **Khi nào KHÔNG dùng?** Graph có weighted edges → Dijkstra; cần explore tất cả paths → DFS.

---

## Binary Search

- **Là gì?** Thuật toán tìm kiếm trên mảng đã sort bằng cách liên tục loại bỏ nửa không chứa kết quả.
- **Sinh ra để giải quyết vấn đề gì?** Linear search O(n) quá chậm cho mảng lớn đã sort. Binary search tận dụng tính đã sort để đạt O(log n).
- **Công dụng?** Tìm kiếm trong sorted array, tìm giá trị nhỏ nhất thỏa điều kiện đơn điệu, tìm trong rotated array.
- **Khi nào KHÔNG dùng?** Mảng chưa sort (phải sort trước, tốn O(n log n)); dữ liệu thay đổi liên tục → cân nhắc BST.

---

## Sorting

- **Là gì?** Sắp xếp dãy phần tử theo thứ tự nhất định.
- **Sinh ra để giải quyết vấn đề gì?** Tìm kiếm nhanh (Binary Search), dễ xử lý (Two Pointers, Merge), hiển thị có thứ tự.
- **Công dụng?** Tiền xử lý trước Binary Search/Two Pointers, bài toán interval, rank/leaderboard.
- **So sánh nhanh:** Quick Sort (fast average, in-place, không stable) · Merge Sort (stable, O(n) space) · Heap Sort (in-place, không stable) · Counting Sort (O(n) khi range nhỏ).

---

## Dynamic Programming (DP)

- **Là gì?** Kỹ thuật tối ưu bằng cách chia bài toán thành subproblems, lưu kết quả để tránh tính lại (memoization/tabulation).
- **Sinh ra để giải quyết vấn đề gì?** Đệ quy thuần túy tính lại cùng subproblem nhiều lần → exponential time. DP lưu kết quả → polynomial time.
- **Công dụng?** Knapsack, LCS, Edit Distance, Fibonacci, Shortest path (Bellman-Ford), coin change, DP trên string/grid.
- **Khi nào KHÔNG dùng?** Không có overlapping subproblems → Greedy hoặc D&C; state space quá lớn → memory issue.

---

## Greedy

- **Là gì?** Thuật toán chọn lựa tốt nhất tại mỗi bước (locally optimal) với hy vọng đạt kết quả tối ưu toàn cục.
- **Sinh ra để giải quyết vấn đề gì?** DP đôi khi quá phức tạp/chậm — với một số bài toán, chọn greedy tại mỗi bước lại cho kết quả optimal và đơn giản hơn nhiều.
- **Công dụng?** Interval scheduling, Huffman encoding, Dijkstra, Prim's MST, Jump Game, coin change (với coin set chuẩn).
- **Khi nào KHÔNG dùng?** Không chứng minh được greedy choice property → dùng DP (ví dụ: coin change với coin set không chuẩn).

---

## Backtracking

- **Là gì?** Kỹ thuật duyệt tất cả khả năng bằng đệ quy, có cơ chế "undo" khi nhánh hiện tại không hợp lệ.
- **Sinh ra để giải quyết vấn đề gì?** Các bài toán cần liệt kê/tìm tất cả lời giải không có cấu trúc tối ưu rõ ràng — không thể dùng Greedy hay DP.
- **Công dụng?** Permutation, combination, subset, N-Queens, Sudoku, Word Search.
- **Khi nào KHÔNG dùng?** Bài toán chỉ cần 1 lời giải tối ưu → DP/Greedy; khi không có pruning hiệu quả → quá chậm.

---

## Trie

- **Là gì?** Cây prefix — mỗi node đại diện cho 1 ký tự, đường đi từ root tới node = prefix của từ.
- **Sinh ra để giải quyết vấn đề gì?** Tìm kiếm prefix trong tập từ bằng HashMap = O(L·N); Trie = O(L) không phụ thuộc N (số lượng từ).
- **Công dụng?** Autocomplete, spell checker, IP routing (longest prefix match), word search trong grid.
- **Khi nào KHÔNG dùng?** Chỉ cần exact match → HashMap nhanh hơn; bộ nhớ hạn chế khi vocabulary lớn.

---

## Union-Find (Disjoint Set Union)

- **Là gì?** Cấu trúc dữ liệu quản lý các tập hợp rời nhau, hỗ trợ 2 thao tác: `find` (tập nào?) và `union` (gộp 2 tập).
- **Sinh ra để giải quyết vấn đề gì?** Detect cycle và tìm connected components trong graph hiệu quả hơn DFS/BFS cho một số bài toán online (thêm cạnh dần dần).
- **Công dụng?** Kruskal's MST, detect cycle, dynamic connectivity, network connectivity.
- **Khi nào KHÔNG dùng?** Cần split (tách tập) — Union-Find không hỗ trợ; cần path → BFS/DFS.

---

# ═══════════════════════════════
# OOP
# ═══════════════════════════════

---

## Class & Object

- **Là gì?** Class = bản thiết kế (blueprint) định nghĩa attributes + methods. Object = instance cụ thể được tạo từ class.
- **Sinh ra để giải quyết vấn đề gì?** Lập trình thủ tục (procedural) khó quản lý khi hệ thống lớn — data và function tách rời, khó tái sử dụng. OOP gom data + behavior vào 1 đơn vị.
- **Công dụng?** Mô hình hóa thực thể thực tế (User, Product, Order), tổ chức code có cấu trúc, tái sử dụng qua inheritance.
- **Khi nào KHÔNG dùng?** Script nhỏ, data pipeline đơn giản → functional/procedural đơn giản hơn.

---

## Encapsulation

- **Là gì?** Che giấu nội bộ của object, chỉ expose những gì cần thiết qua public interface.
- **Sinh ra để giải quyết vấn đề gì?** Nếu mọi thứ đều public, code bên ngoài có thể thay đổi state nội bộ tùy tiện → bug khó tìm, khó maintain.
- **Công dụng?** Kiểm soát cách data được đọc/ghi (`@property`), ẩn implementation detail, giảm coupling giữa các phần của hệ thống.
- **Khi nào KHÔNG dùng?** Data class thuần túy (DTO) không cần logic → không cần getter/setter phức tạp.

---

## Inheritance

- **Là gì?** Cơ chế cho phép class con (subclass) kế thừa attributes và methods từ class cha (superclass).
- **Sinh ra để giải quyết vấn đề gì?** Tránh lặp code khi nhiều class có chung đặc điểm — viết 1 lần ở class cha, các con tự có.
- **Công dụng?** Tái sử dụng code, tạo cấu trúc phân cấp (Animal → Dog/Cat), override behavior.
- **Khi nào KHÔNG dùng?** Quan hệ không phải IS-A thực sự → dùng Composition; hierarchy quá sâu → khó maintain; dùng Composition thay Inheritance khi có thể.

---

## Polymorphism

- **Là gì?** Cùng 1 interface hoặc method name nhưng hành vi khác nhau tùy loại object thực tế.
- **Sinh ra để giải quyết vấn đề gì?** Code không nên biết cụ thể đang làm việc với loại object nào — chỉ cần gọi method, object tự xử lý đúng cách của nó → giảm if/else, dễ mở rộng.
- **Công dụng?** Plugin system, strategy pattern, event handler, render engine (mỗi shape tự vẽ).
- **Khi nào KHÔNG dùng?** Số lượng loại object ít và cố định → switch/if đơn giản hơn.

---

## Abstraction

- **Là gì?** Ẩn đi chi tiết phức tạp, chỉ expose interface đơn giản cần thiết.
- **Sinh ra để giải quyết vấn đề gì?** Khi hệ thống phức tạp, người dùng class không cần biết bên trong hoạt động thế nào — chỉ cần biết "gọi cái gì để làm gì".
- **Công dụng?** Abstract class định nghĩa template method, interface định nghĩa contract, API hiding implementation.
- **Khi nào KHÔNG dùng?** Over-abstraction làm code phức tạp không cần thiết — YAGNI.

---

## Interface vs Abstract Class

- **Interface là gì?** Hợp đồng thuần túy — định nghĩa "class có thể làm gì" (CAN-DO), không có implementation.
- **Abstract Class là gì?** Bản thiết kế có thể có code thực — định nghĩa "class là gì" (IS-A), có thể có implementation một phần.
- **Sinh ra để giải quyết vấn đề gì?** Interface giải quyết: nhiều class không liên quan cần cùng hành vi (Serializable, Comparable). Abstract Class giải quyết: các class liên quan cần share code chung.
- **Công dụng?** Interface → dependency injection, decoupling. Abstract Class → template method pattern, shared code.

---

## SOLID Principles

- **Là gì?** 5 nguyên tắc thiết kế OOP giúp code dễ maintain, dễ extend, ít coupling.
- **Sinh ra để giải quyết vấn đề gì?** Code không có nguyên tắc → "spaghetti code": thay đổi 1 chỗ gây bug chỗ khác, class làm quá nhiều thứ, khó test.
- **Công dụng?** Hướng dẫn cách tổ chức class/method để code scalable và maintainable.
- **Từng chữ:**
  - **S** — SRP: mỗi class 1 lý do thay đổi → tránh God class
  - **O** — OCP: thêm feature = thêm code mới, không sửa code cũ → tránh regression
  - **L** — LSP: subclass thay thế được superclass → tránh surprise behavior
  - **I** — ISP: interface nhỏ hơn tốt hơn → tránh implement method vô dụng
  - **D** — DIP: depend on abstraction → dễ swap implementation, dễ test

---

## Design Patterns

- **Là gì?** Giải pháp có tên gọi cho các vấn đề thiết kế phổ biến tái xuất hiện trong lập trình OOP.
- **Sinh ra để giải quyết vấn đề gì?** Các vấn đề thiết kế giống nhau xuất hiện lặp đi lặp lại — đặt tên và chuẩn hóa giải pháp giúp communicate nhanh hơn và tránh tái phát minh bánh xe.
- **3 nhóm:**
  - **Creational** (tạo object): Singleton, Factory, Builder, Prototype
  - **Structural** (cấu trúc): Adapter, Decorator, Facade, Proxy
  - **Behavioral** (hành vi): Observer, Strategy, Command, Iterator
- **Khi nào KHÔNG dùng?** Không nên áp dụng pattern cho vấn đề không cần — over-engineering.

---

# ═══════════════════════════════
# OS
# ═══════════════════════════════

---

## Process

- **Là gì?** Một chương trình đang chạy — có không gian bộ nhớ riêng, resources riêng, được OS quản lý qua PCB.
- **Sinh ra để giải quyết vấn đề gì?** Cần chạy nhiều chương trình đồng thời và isolate chúng — process crash không ảnh hưởng process khác.
- **Công dụng?** Mỗi tab Chrome là 1 process, mỗi app là 1 process → crash isolation.
- **Khi nào KHÔNG dùng?** Giao tiếp giữa các process tốn kém (IPC) → nếu cần share data nhiều, xem xét Thread.

---

## Thread

- **Là gì?** Đơn vị thực thi nhỏ hơn process, chạy trong process, chia sẻ bộ nhớ với các thread khác cùng process.
- **Sinh ra để giải quyết vấn đề gì?** Process quá nặng để tạo và communicate. Thread nhẹ hơn, share memory trực tiếp → tốt cho concurrency trong cùng ứng dụng.
- **Công dụng?** Web server xử lý mỗi request 1 thread, UI thread + background thread, parallel processing.
- **Khi nào KHÔNG dùng?** CPU-bound với Python → GIL block (dùng multiprocessing); 1 thread crash có thể kill cả process.

---

## Virtual Memory

- **Là gì?** Kỹ thuật cho mỗi process thấy một không gian địa chỉ ảo riêng, lớn hơn RAM thực tế, ánh xạ sang physical memory qua Page Table.
- **Sinh ra để giải quyết vấn đề gì?** Nếu dùng physical memory trực tiếp: (1) process có thể đọc/ghi vùng nhớ của process khác — không an toàn; (2) tổng RAM giới hạn số process chạy đồng thời.
- **Công dụng?** Isolation giữa các process, cho phép chạy program lớn hơn RAM (swap), memory-mapped files.
- **Khi nào CÓ VẤN ĐỀ?** Thrashing = swap quá nhiều → performance xuống thảm.

---

## Paging

- **Là gì?** Chia bộ nhớ thành các trang (page) kích thước cố định — virtual pages ánh xạ sang physical frames qua Page Table.
- **Sinh ra để giải quyết vấn đề gì?** External fragmentation: memory bị phân mảnh, không dùng được dù tổng còn đủ. Paging loại bỏ external fragmentation (nhưng có internal fragmentation nhỏ).
- **Công dụng?** Nền tảng của virtual memory hiện đại.
- **TLB:** Cache của Page Table — vì page table lookup tốn thêm 1 memory access, TLB giúp giảm về ~0 overhead cho hot pages.

---

## CPU Scheduling

- **Là gì?** OS quyết định process/thread nào được chạy trên CPU tại một thời điểm.
- **Sinh ra để giải quyết vấn đề gì?** CPU chỉ có 1 (hoặc vài) cores nhưng có hàng trăm process muốn chạy — cần cơ chế phân bổ công bằng và hiệu quả.
- **Công dụng?** FCFS (đơn giản), SJF (min avg wait), Round Robin (fair, time-sharing), Priority (real-time).
- **Trade-off cốt lõi:** Throughput vs Fairness vs Response time vs Starvation avoidance.

---

## Deadlock

- **Là gì?** Tình trạng 2+ processes/threads chờ nhau mãi mãi — mỗi cái giữ tài nguyên mà cái kia cần.
- **Sinh ra từ vấn đề gì?** Concurrency + shared resources → nếu không quản lý cẩn thận, có thể circular wait.
- **4 điều kiện để xảy ra:** Mutual Exclusion + Hold & Wait + No Preemption + Circular Wait. Phá 1 trong 4 → không deadlock.
- **Giải pháp thực tế:** Lock ordering (always acquire locks in same order), timeout, Banker's Algorithm (avoidance).

---

## Mutex

- **Là gì?** Mutual Exclusion lock — chỉ 1 thread được vào critical section tại 1 thời điểm; có ownership (chỉ thread lock mới unlock được).
- **Sinh ra để giải quyết vấn đề gì?** Race condition: nhiều threads cùng đọc/ghi shared data → kết quả không dự đoán được.
- **Công dụng?** Bảo vệ critical section, bất kỳ shared mutable state nào.
- **Khi nào KHÔNG dùng?** Cần đếm (không phải binary) → Semaphore; cần high-level sync → Monitor/Condition variable.

---

## Semaphore

- **Là gì?** Biến đếm (counter) cho phép N threads vào critical section đồng thời; không có ownership.
- **Sinh ra để giải quyết vấn đề gì?** Mutex chỉ cho 1 thread — nhưng đôi khi cần giới hạn N concurrent access (connection pool 10 connections, 10 threads cùng vào được).
- **Công dụng?** Connection pool limiting, producer-consumer synchronization, rate limiting.
- **Khi nào KHÔNG dùng?** Chỉ cần mutual exclusion → Mutex (có ownership, an toàn hơn).

---

## File System & inode

- **Là gì?** inode (index node) = metadata của file: permissions, size, timestamps, owner, pointer tới data blocks. Tên file lưu trong directory, không phải trong inode.
- **Sinh ra để giải quyết vấn đề gì?** Cần tách biệt metadata khỏi data và khỏi tên file → cho phép hard link (nhiều tên → cùng inode), flexible directory structure.
- **Công dụng?** Tất cả thao tác file (read, write, chmod, chown) đều đi qua inode.
- **Hard link vs Soft link:** Hard link = tên thứ hai cho cùng inode; Soft link = shortcut trỏ tới đường dẫn (có thể broken).

---

# ═══════════════════════════════
# NETWORK
# ═══════════════════════════════

---

## OSI Model

- **Là gì?** Framework 7 tầng mô tả cách dữ liệu di chuyển qua network — mỗi tầng có vai trò riêng và chỉ giao tiếp với tầng liền kề.
- **Sinh ra để giải quyết vấn đề gì?** Trước OSI, mỗi vendor có protocol riêng, không tương thích. OSI chuẩn hóa → các hệ thống khác nhau có thể nói chuyện với nhau.
- **Công dụng?** Framework để debug network issues (problem ở tầng nào?), thiết kế protocol, hiểu cách các công nghệ fit vào nhau.
- **Thực tế:** TCP/IP model (4 tầng) được dùng thực tế hơn OSI (7 tầng).

---

## TCP

- **Là gì?** Transmission Control Protocol — giao thức transport layer đảm bảo delivery tin cậy, đúng thứ tự, không mất gói.
- **Sinh ra để giải quyết vấn đề gì?** IP (Internet Protocol) chỉ là "best effort" — gói có thể mất, đến sai thứ tự. Cần layer đảm bảo reliability trên nền IP không tin cậy.
- **Công dụng?** HTTP/HTTPS, SSH, FTP, email — mọi thứ cần dữ liệu toàn vẹn.
- **Cơ chế:** 3-way handshake, sequence numbers, ACK, retransmission, flow control (sliding window), congestion control.
- **Khi nào KHÔNG dùng?** Cần tốc độ, chấp nhận mất một ít → UDP (video call, gaming, DNS).

---

## UDP

- **Là gì?** User Datagram Protocol — giao thức transport layer không kết nối, không đảm bảo delivery, không đảm bảo thứ tự.
- **Sinh ra để giải quyết vấn đề gì?** TCP có overhead: handshake, ACK, retransmission → latency cao. Một số ứng dụng cần tốc độ hơn độ tin cậy.
- **Công dụng?** Video streaming, online gaming, VoIP, DNS query, DHCP. Mất 1 frame video → OK; delay vì retransmit → không OK.
- **Khi nào KHÔNG dùng?** Data phải toàn vẹn (file transfer, email, banking) → TCP.

---

## HTTP

- **Là gì?** HyperText Transfer Protocol — giao thức application layer, request-response, stateless, dùng TCP.
- **Sinh ra để giải quyết vấn đề gì?** Cần chuẩn hóa cách browser và server giao tiếp để chia sẻ tài liệu hypertext (web pages).
- **Công dụng?** Mọi web request, REST API, downloading files.
- **Stateless:** Server không nhớ gì giữa các request → cần Cookie/Session/JWT để maintain state.
- **Tiến hóa:** HTTP/1.1 (persistent) → HTTP/2 (multiplexing) → HTTP/3 (QUIC/UDP, no HoL blocking).

---

## HTTPS / TLS

- **Là gì?** HTTP over TLS (Transport Layer Security) — mã hóa toàn bộ HTTP traffic.
- **Sinh ra để giải quyết vấn đề gì?** HTTP plain text → bất kỳ ai trên đường truyền đều đọc được (MITM attack), không verify server identity.
- **TLS giải quyết 3 vấn đề:** (1) Confidentiality — mã hóa; (2) Integrity — không bị tamper; (3) Authentication — verify server là ai nói.
- **Cơ chế:** Asymmetric crypto để exchange session key → Symmetric crypto để encrypt data (vì asymmetric chậm hơn nhiều).
- **Công dụng?** Mọi website production, API, bất kỳ đâu truyền sensitive data.

---

## DNS

- **Là gì?** Domain Name System — hệ thống phân tán dịch domain name (google.com) → IP address.
- **Sinh ra để giải quyết vấn đề gì?** Người dùng không thể nhớ IP address. Cần hệ thống đặt tên dễ nhớ và phân tán (không có single point of failure, scale được).
- **Công dụng?** Mọi kết nối internet đều bắt đầu bằng DNS lookup; load balancing qua DNS (multiple A records); CDN routing.
- **Cơ chế:** Phân cấp: Root → TLD (.com, .vn) → Authoritative NS → IP. Caching với TTL.
- **Khi nào DNS fail?** DNS poisoning, server down → dùng multiple DNS providers (8.8.8.8, 1.1.1.1).

---

## Socket

- **Là gì?** Endpoint của kết nối mạng — xác định bởi (IP, Port). TCP socket = (src_ip, src_port, dst_ip, dst_port).
- **Sinh ra để giải quyết vấn đề gì?** Cần abstraction để application code giao tiếp mạng mà không cần biết chi tiết TCP/IP — "file-like interface" cho network.
- **Công dụng?** Mọi network programming dùng socket ở mức thấp; web server listen socket; WebSocket cho real-time.
- **Blocking vs Non-blocking:** Blocking = chờ đến khi có data; Non-blocking = return ngay, check sau → cần select/poll/epoll cho nhiều connections.

---

## Firewall & NAT

- **Firewall là gì?** Bộ lọc packet dựa trên rules (IP, port, protocol) — block traffic không mong muốn.
- **Sinh ra để giải quyết vấn đề gì?** Internet public không an toàn — cần kiểm soát traffic vào/ra mạng nội bộ.
- **NAT (Network Address Translation) là gì?** Dịch private IP ↔ public IP — nhiều máy trong LAN dùng chung 1 public IP.
- **Sinh ra để giải quyết vấn đề gì?** IPv4 chỉ có ~4 tỷ địa chỉ — không đủ cho mọi thiết bị. NAT cho phép mạng nội bộ dùng private IP range (192.168.x.x, 10.x.x.x).

---

## Load Balancer

- **Là gì?** Thành phần phân phối traffic đến nhiều servers theo thuật toán nhất định.
- **Sinh ra để giải quyết vấn đề gì?** 1 server không chịu được hàng triệu request — cần phân tải. Cũng giải quyết single point of failure — server chết thì LB route sang server khác.
- **Công dụng?** Horizontal scaling, high availability, SSL termination, health check.
- **L4 vs L7:** L4 (TCP level, fast, dumb) vs L7 (HTTP level, slow hơn một chút, smart — routing theo URL/header/cookie).

---

## CDN (Content Delivery Network)

- **Là gì?** Mạng lưới servers phân tán toàn cầu (PoPs — Points of Presence), cache và serve content gần user nhất.
- **Sinh ra để giải quyết vấn đề gì?** Server ở Mỹ → user ở Việt Nam → latency cao (round-trip 200ms+). CDN cache content ở Singapore → latency xuống 20ms.
- **Công dụng?** Static assets (JS, CSS, images, video), DDoS protection (absorb traffic), SSL termination.
- **Khi nào KHÔNG đủ?** Dynamic, personalized content không cache được → vẫn phải về origin server.

---

# ═══════════════════════════════
# SYSTEM DESIGN
# ═══════════════════════════════

---

## Scalability

- **Là gì?** Khả năng hệ thống xử lý tải tăng lên mà không giảm performance.
- **Sinh ra để giải quyết vấn đề gì?** Hệ thống hoạt động tốt với 100 user, nhưng crash với 100,000 user — cần thiết kế để scale.
- **Vertical (scale up):** Nâng cấp phần cứng máy hiện tại. Đơn giản nhưng có giới hạn cứng, single point of failure.
- **Horizontal (scale out):** Thêm nhiều máy. Cần stateless service — session/state phải lưu ngoài (Redis/DB).
- **Công dụng?** Nền tảng của mọi quyết định kiến trúc khi traffic tăng.

---

## Caching

- **Là gì?** Lưu tạm kết quả tính toán hoặc data hay dùng ở bộ nhớ nhanh hơn (RAM) để tránh tính lại hoặc query DB lại.
- **Sinh ra để giải quyết vấn đề gì?** DB query tốn 10-100ms; cùng data được request hàng nghìn lần/giây → bottleneck. Cache giảm latency xuống <1ms và giảm tải DB.
- **Công dụng?** Session store, DB query cache, computed results, static content, API response.
- **Patterns:** Cache-aside (lazy, app kiểm soát) · Write-through (consistent, write chậm) · Write-behind (fast write, risk mất data).
- **Khi nào KHÔNG dùng?** Data thay đổi liên tục và cần real-time accuracy; data sensitive không nên cache.

---

## Database Sharding

- **Là gì?** Chia data của 1 database thành nhiều mảnh (shards) lưu ở nhiều máy khác nhau.
- **Sinh ra để giải quyết vấn đề gì?** 1 DB server không đủ capacity cho data lớn (hàng tỷ rows) hoặc write throughput cao — vertical scale có giới hạn.
- **Công dụng?** Scale writes và storage horizontally. Ví dụ: shard theo user_id % N → user 1 vào shard 0, user 2 vào shard 1...
- **Vấn đề:** Cross-shard queries phức tạp, resharding khó. Consistent hashing giảm data movement khi thêm shard.

---

## Message Queue

- **Là gì?** Hệ thống trung gian nhận messages từ producer và deliver đến consumer, buffer lại khi consumer bận.
- **Sinh ra để giải quyết vấn đề gì?** Gọi trực tiếp (synchronous): nếu service B chậm → service A bị block; nếu B down → A fail. MQ decouples producer và consumer, buffer traffic spike.
- **Công dụng?** Async processing (gửi email, resize ảnh), decouple microservices, event streaming, task queue.
- **Khi nào KHÔNG dùng?** Cần response ngay lập tức (synchronous API call); bài toán quá đơn giản → overhead không đáng.

---

## Microservices

- **Là gì?** Kiến trúc chia ứng dụng thành nhiều services nhỏ, độc lập, mỗi service làm 1 việc và có DB riêng.
- **Sinh ra để giải quyết vấn đề gì?** Monolith lớn: deploy 1 feature phải deploy cả app; team lớn conflict code; scale toàn bộ app dù chỉ 1 phần bị tải.
- **Công dụng?** Independent deployment, independent scaling, technology flexibility, fault isolation.
- **Khi nào KHÔNG dùng?** Team nhỏ (<10 người), ứng dụng đơn giản — microservices overhead (distributed tracing, network latency, data consistency) không đáng.

---

## Circuit Breaker

- **Là gì?** Pattern ngăn gọi liên tục đến service đang lỗi — fail fast thay vì chờ timeout.
- **Sinh ra để giải quyết vấn đề gì?** Service A gọi Service B đang lất → A chờ timeout (30s) × 1000 requests = resource exhaustion → A cũng chết → cascade failure.
- **Công dụng?** Resilience trong microservices, ngăn cascade failure.
- **3 states:** Closed (bình thường) → Open (error rate cao, fail fast) → Half-Open (thử lại 1 request) → Closed nếu OK.

---

## CAP Theorem

- **Là gì?** Định lý phát biểu: hệ thống phân tán không thể đồng thời đảm bảo cả 3: Consistency + Availability + Partition Tolerance.
- **Sinh ra để giải quyết vấn đề gì?** Giúp architects hiểu trade-off khi thiết kế distributed system — không có "best of all worlds".
- **Thực tế:** Network partition luôn xảy ra → phải chọn CP hoặc AP. CP = nhất quán nhưng có thể không available (MongoDB). AP = luôn available nhưng data có thể stale (Cassandra, DynamoDB).
- **Công dụng?** Framework để chọn database và thiết kế data consistency strategy.

---

# ═══════════════════════════════
# BACKEND
# ═══════════════════════════════

---

## Authentication vs Authorization

- **Authentication là gì?** Xác minh "mày là ai" — verify identity.
- **Authorization là gì?** Kiểm tra "mày được làm gì" — verify permissions.
- **Sinh ra để giải quyết vấn đề gì?** Hệ thống cần biết user là ai (authn) và user đó được phép làm gì (authz) — 2 bước riêng biệt.
- **Ví dụ:** Login = authentication. Kiểm tra có phải admin không = authorization.

---

## JWT (JSON Web Token)

- **Là gì?** Token tự chứa thông tin (header.payload.signature), server verify bằng signature mà không cần lookup DB.
- **Sinh ra để giải quyết vấn đề gì?** Session-based auth cần server lưu state (session store) — không scale tốt với horizontal scaling. JWT stateless — mọi server đều verify được mà không cần shared session store.
- **Công dụng?** Stateless authentication trong REST API, microservices, mobile apps.
- **Khi nào KHÔNG dùng?** Cần revoke token ngay lập tức (logout, ban user) — JWT khó revoke vì không có server-side state. Cần blacklist hoặc short expiry + refresh token.

---

## OAuth 2.0

- **Là gì?** Framework ủy quyền (authorization) cho phép app thứ 3 truy cập resource của user trên service khác mà không cần biết password.
- **Sinh ra để giải quyết vấn đề gì?** "Login with Google" — user không muốn cho app thứ 3 biết password Google của mình.
- **Công dụng?** Social login, third-party API access, service-to-service auth (Client Credentials flow).
- **Lưu ý:** OAuth 2.0 là authorization, không phải authentication → OIDC (OpenID Connect) thêm lớp authentication lên trên OAuth 2.0.

---

## REST API

- **Là gì?** Kiến trúc thiết kế API dựa trên HTTP, sử dụng resource-based URL và HTTP methods có nghĩa ngữ nghĩa.
- **Sinh ra để giải quyết vấn đề gì?** Trước REST, không có chuẩn chung cho web API — mỗi service thiết kế khác nhau, khó tích hợp.
- **Công dụng?** Standard cho web API, dễ hiểu, stateless, scalable, cacheable.
- **Khi nào KHÔNG dùng?** Cần real-time bidirectional → WebSocket; cần client chọn fields → GraphQL; internal service-to-service performance critical → gRPC.

---

## Middleware

- **Là gì?** Hàm/component nằm trong request processing pipeline, chạy trước/sau business logic handler.
- **Sinh ra để giải quyết vấn đề gì?** Các concerns cross-cutting (auth, logging, rate limiting, CORS) cần áp dụng cho nhiều endpoints — không muốn viết lại trong mỗi handler.
- **Công dụng?** Authentication check, request logging, rate limiting, CORS headers, compression, error handling.
- **Ví dụ:** Express.js `app.use(authMiddleware)`, Django middleware, Spring `@Interceptor`.

---

## Idempotency

- **Là gì?** Property: gọi cùng 1 operation nhiều lần = kết quả giống gọi 1 lần.
- **Sinh ra để giải quyết vấn đề gì?** Network timeout → client retry → nếu operation không idempotent → trừ tiền 2 lần, tạo 2 orders.
- **Công dụng?** Payment processing, order creation, any mutation API. Implement bằng idempotency key (client tạo unique ID per request → server deduplicate).
- **Khi nào quan trọng nhất?** Mọi write operation có thể bị retry do network failure.

---

# ═══════════════════════════════
# DATABASE
# ═══════════════════════════════

---

## SQL vs NoSQL

- **SQL là gì?** Relational database — data dạng bảng có schema cứng, quan hệ qua foreign keys, hỗ trợ JOIN.
- **NoSQL là gì?** Non-relational database — nhiều loại (document, key-value, column-family, graph), schema flexible.
- **SQL sinh ra để giải quyết vấn đề gì?** Cần lưu data có cấu trúc, quan hệ phức tạp, đảm bảo ACID.
- **NoSQL sinh ra để giải quyết vấn đề gì?** SQL khó scale horizontally; schema cứng khó thay đổi; không phải data nào cũng có dạng bảng.
- **Chọn SQL khi:** ACID cần thiết, data có quan hệ phức tạp, reporting/analytics, team quen SQL.
- **Chọn NoSQL khi:** Scale lớn, schema thay đổi liên tục, key-value/document patterns, low latency.

---

## ACID

- **Là gì?** 4 properties đảm bảo transaction database đáng tin cậy: Atomicity, Consistency, Isolation, Durability.
- **Sinh ra để giải quyết vấn đề gì?** Database operations có thể fail giữa chừng, nhiều users đọc/ghi đồng thời, system crash — cần đảm bảo data không bị corrupt.
- **A** — Atomicity: transaction = all or nothing. Transfer tiền: trừ A VÀ cộng B, không thể trừ A mà không cộng B.
- **C** — Consistency: transaction chỉ đưa DB từ valid state này sang valid state khác (constraints không bị vi phạm).
- **I** — Isolation: transactions đồng thời không thấy được intermediate state của nhau.
- **D** — Durability: khi committed, data tồn tại dù system crash.

---

## Database Index

- **Là gì?** Cấu trúc dữ liệu phụ (B-tree hoặc Hash) giúp tìm rows nhanh hơn mà không phải scan toàn bộ table.
- **Sinh ra để giải quyết vấn đề gì?** Table 1 triệu rows, query `WHERE email = ?` phải scan 1M rows = O(n). Index → O(log n).
- **Công dụng?** Tăng tốc SELECT, JOIN, ORDER BY, WHERE. Covering index còn tránh cả table lookup.
- **Trade-off:** Index tốn thêm disk space + làm chậm INSERT/UPDATE/DELETE (phải update index). Không index mọi column.

---

## Transaction & Locking

- **Transaction là gì?** Nhóm các operations SQL thực thi như 1 đơn vị — commit hoặc rollback toàn bộ.
- **Sinh ra để giải quyết vấn đề gì?** Multi-step operations (transfer tiền = 2 UPDATEs) không atomic → có thể bị interrupted giữa chừng.
- **Locking:** Pessimistic (`SELECT FOR UPDATE` — lock ngay) vs Optimistic (check version khi write — không lock).
- **Deadlock trong DB:** 2 transactions chờ nhau → DB auto-detect và rollback 1 cái.
- **Công dụng?** Bất kỳ multi-step write operation nào cần atomic.

---

## Redis

- **Là gì?** In-memory data structure store — hỗ trợ String, Hash, List, Set, Sorted Set, với persistence tuỳ chọn.
- **Sinh ra để giải quyết vấn đề gì?** DB query chậm (10-100ms) không đủ cho high-traffic use cases. Redis in-memory = <1ms latency.
- **Công dụng?** Cache layer, session store, rate limiting (INCR + EXPIRE), leaderboard (Sorted Set), pub/sub, job queue.
- **Khi nào KHÔNG dùng?** Data lớn hơn RAM; cần complex queries/joins; data cần durability tuyệt đối mà không chấp nhận mất.

---

# ═══════════════════════════════
# API
# ═══════════════════════════════

---

## GraphQL

- **Là gì?** Query language cho API — client specify chính xác data mình cần, không hơn không kém.
- **Sinh ra để giải quyết vấn đề gì?** REST over-fetching (trả về fields không cần) và under-fetching (phải gọi nhiều endpoints để lấy đủ data). Mobile apps đặc biệt sensitive với payload size.
- **Công dụng?** APIs cần flexible queries, mobile clients, BFF (Backend for Frontend) layer.
- **Khi nào KHÔNG dùng?** Simple CRUD API — REST đơn giản hơn; caching khó hơn REST (vì POST body thay đổi).

---

## gRPC

- **Là gì?** Remote Procedure Call framework dùng Protocol Buffers (binary serialization), HTTP/2, hỗ trợ streaming.
- **Sinh ra để giải quyết vấn đề gì?** REST/JSON chậm cho internal service-to-service communication: JSON parsing overhead, HTTP/1.1 overhead. gRPC binary + HTTP/2 = nhanh hơn nhiều.
- **Công dụng?** Internal microservices communication, streaming (server/client/bidirectional), polyglot environments (auto-generate client code từ .proto).
- **Khi nào KHÔNG dùng?** Browser clients (gRPC-web limited); public API (REST/GraphQL dễ consume hơn).

---

## WebSocket

- **Là gì?** Protocol cho phép kết nối persistent, bidirectional giữa client và server trên 1 TCP connection (upgrade từ HTTP).
- **Sinh ra để giải quyết vấn đề gì?** HTTP request-response không đủ cho real-time: client phải liên tục poll (long polling) → inefficient. WebSocket cho phép server push data bất cứ lúc nào.
- **Công dụng?** Chat apps, live notifications, collaborative editing (Google Docs), live sports scores, trading platforms.
- **Khi nào KHÔNG dùng?** Chỉ cần server → client (one-way) → SSE đơn giản hơn; HTTP/2 server push cho simple cases.

---

## Rate Limiting

- **Là gì?** Giới hạn số lượng requests từ 1 client trong 1 khoảng thời gian nhất định.
- **Sinh ra để giải quyết vấn đề gì?** Không có rate limit → 1 client gửi hàng triệu requests → DoS, tốn resources, abuse API.
- **Công dụng?** API protection, fair usage, prevent abuse, billing (pay-per-use).
- **Algorithms:** Token Bucket (burst OK), Leaky Bucket (smooth output), Sliding Window (accurate).
- **Implement:** Redis INCR + EXPIRE per user/IP; API Gateway level.

---

# ═══════════════════════════════
# MACHINE LEARNING
# ═══════════════════════════════

---

## Machine Learning (tổng quan)

- **Là gì?** Nhánh của AI — máy tính học patterns từ data mà không được lập trình explicit rules.
- **Sinh ra để giải quyết vấn đề gì?** Một số bài toán (nhận diện ảnh, spam filter, recommendation) quá phức tạp để viết rules thủ công — ML tự học rules từ examples.
- **3 loại:** Supervised (có label), Unsupervised (tìm structure), Reinforcement (reward signal).

---

## Gradient Descent

- **Là gì?** Thuật toán tối ưu — lặp đi lặp lại update parameters theo hướng ngược gradient của loss function để minimize loss.
- **Sinh ra để giải quyết vấn đề gì?** Không thể tìm minimum của loss function analytically khi có hàng triệu parameters → cần iterative optimization.
- **Công dụng?** Training mọi ML model — Linear Regression, Neural Network, SVM.
- **Variants:** Batch GD (chính xác, chậm) → SGD (noisy, nhanh) → Mini-batch → Adam (adaptive learning rate, phổ biến nhất).

---

## Neural Network

- **Là gì?** Model lấy cảm hứng từ não người — nhiều layers của "neurons" (linear transformation + activation function) xếp chồng nhau.
- **Sinh ra để giải quyết vấn đề gì?** Linear models không học được non-linear patterns. Neural network với activation functions phi tuyến có thể approximate bất kỳ function nào (Universal Approximation Theorem).
- **Công dụng?** Image recognition (CNN), NLP (Transformer), speech recognition, game playing.
- **Backpropagation:** Tính gradient của loss với respect to mỗi weight qua chain rule, từ output layer ngược về input.

---

## Overfitting & Underfitting

- **Overfitting là gì?** Model học quá tốt trên training data — "nhớ" thay vì "hiểu" → performance kém trên test data.
- **Underfitting là gì?** Model quá đơn giản — không học được patterns trong data → performance kém cả train lẫn test.
- **Sinh ra từ vấn đề gì?** Bias-variance tradeoff: model phức tạp hơn = ít bias hơn nhưng variance cao hơn.
- **Fix overfitting:** More data, regularization (L1/L2), dropout, early stopping, simpler model.
- **Fix underfitting:** More complex model, more features, ít regularization hơn.

---

## Transformer & Attention

- **Là gì?** Kiến trúc Neural Network dùng self-attention mechanism — mỗi token "chú ý" đến tất cả tokens khác trong sequence để hiểu ngữ cảnh.
- **Sinh ra để giải quyết vấn đề gì?** RNN xử lý tuần tự → không parallelizable, vanishing gradient với sequence dài. Transformer xử lý toàn sequence song song, attention capture long-range dependencies.
- **Công dụng?** BERT, GPT, T5, ChatGPT, Google Translate — state-of-the-art cho hầu hết NLP tasks.
- **Self-attention:** Query × Key^T → softmax → weights → weighted sum of Values.

---

# ═══════════════════════════════
# LINUX
# ═══════════════════════════════

---

## Linux File System

- **Là gì?** Cấu trúc cây thư mục bắt đầu từ root `/`, mọi thứ (file, device, process) đều là file.
- **Sinh ra để giải quyết vấn đề gì?** "Everything is a file" — unified interface: đọc disk, network socket, thiết bị ngoại vi đều dùng cùng read/write API.
- **Công dụng?** `/etc` = config, `/var` = logs/runtime, `/tmp` = temp, `/proc` = process info (virtual FS), `/dev` = devices.

---

## Linux Permissions

- **Là gì?** Hệ thống kiểm soát ai được đọc/ghi/thực thi file — mỗi file có owner, group, others với rwx permissions.
- **Sinh ra để giải quyết vấn đề gì?** Multi-user OS cần ngăn users truy cập data/program của nhau; ngăn chạy malicious code.
- **Octal:** rwx = 4+2+1=7; rw- = 4+2=6; r-x = 5; `chmod 755` = rwxr-xr-x.
- **Công dụng?** Security isolation giữa users, web server chỉ đọc được static files.

---

## Process Management

- **Là gì?** Linux quản lý các process đang chạy — mỗi process có PID, PPID, trạng thái, resources.
- **Sinh ra để giải quyết vấn đề gì?** Cần start/stop/monitor programs, xem resource usage, kill hung processes.
- **Công dụng?** `ps`, `top`, `kill`, `nohup`, `systemd` (service management), `cron` (scheduling).
- **Signals:** SIGTERM (15) = graceful shutdown; SIGKILL (9) = force kill (không thể ignore); SIGHUP (1) = reload config.

---

## Shell & Bash Scripting

- **Là gì?** Shell = command interpreter (bash, zsh). Bash script = file chứa commands để automate tasks.
- **Sinh ra để giải quyết vấn đề gì?** Lặp đi lặp lại cùng commands thủ công → error-prone, tốn thời gian. Script automate và reproducible.
- **Công dụng?** Deployment scripts, backup automation, log rotation, CI/CD pipeline steps, sysadmin tasks.
- **Pipe philosophy:** Mỗi tool làm 1 việc tốt, kết hợp qua pipe → powerful workflows (`grep | awk | sort | uniq`).

---

# ═══════════════════════════════
# TOÁN
# ═══════════════════════════════

---

## Toán Rời Rạc (Discrete Mathematics)

- **Là gì?** Toán học nghiên cứu các cấu trúc rời rạc (không liên tục): graph, set, logic, relation, combinatorics.
- **Sinh ra để giải quyết vấn đề gì?** Computer science làm việc với dữ liệu rời rạc (bit, số nguyên, node) — cần toán nền tảng phù hợp, khác toán liên tục (calculus).
- **Công dụng?** Algorithm analysis (graph theory), cryptography (number theory), compiler design (automata), database (relation theory), network design.

---

## Graph Theory

- **Là gì?** Nghiên cứu đồ thị (tập đỉnh + cạnh) và tính chất của chúng.
- **Sinh ra để giải quyết vấn đề gì?** Euler (1736) giải bài toán 7 cầu Königsberg — cần formal framework để model network problems.
- **Công dụng?** Map routing, social network analysis, compiler optimization, network topology, scheduling.
- **Concepts quan trọng:** Connected components, shortest path, spanning tree, cycle detection, topological sort, bipartite.

---

## Modular Arithmetic

- **Là gì?** Toán học với số nguyên "theo vòng tròn" — `a mod m` = remainder khi chia a cho m.
- **Sinh ra để giải quyết vấn đề gì?** Cần làm việc với số rất lớn (cryptography) mà không bị overflow; tính chu kỳ (ngày trong tuần, hash function).
- **Công dụng?** Cryptography (RSA dùng modular exponentiation), hash function, competitive programming (tránh overflow: `(a*b) mod p`), calendar calculations.
- **Fermat's Little Theorem:** `a^(p-1) ≡ 1 (mod p)` với p nguyên tố — dùng để tính modular inverse, fast exponentiation.

---

## Combinatorics (Tổ Hợp)

- **Là gì?** Đếm số cách chọn/sắp xếp đối tượng theo điều kiện nhất định.
- **Sinh ra để giải quyết vấn đề gì?** Trong CS và toán học, thường cần đếm số lượng cấu hình (số subsets, số permutations, số đường đi) mà không liệt kê hết.
- **Công dụng?** Phân tích algorithm (đếm operations), probability, competitive programming, cryptography (key space size).
- **Công thức cốt lõi:** Permutation P(n,k) = n!/(n-k)! · Combination C(n,k) = n!/(k!(n-k)!) · Pascal Triangle · Inclusion-Exclusion.

---

## Logic & Proof Techniques

- **Là gì?** Formal system để suy luận đúng/sai và chứng minh statements toán học.
- **Sinh ra để giải quyết vấn đề gì?** Cần verify correctness của algorithms và programs một cách rigorous — không chỉ "có vẻ đúng".
- **Công dụng?** Formal verification, algorithm correctness proof, database query optimization (relational algebra), type systems.
- **Proof techniques:** Direct proof, Contradiction (giả sử ngược lại → mâu thuẫn), Induction (base case + inductive step), Contrapositive.

---

# ═══════════════════════════════
# SOFTWARE ENGINEERING
# ═══════════════════════════════

---

## Concurrency

- **Là gì?** Nhiều tasks "in progress" cùng lúc — có thể xen kẽ trên 1 CPU (concurrency) hoặc chạy thực sự đồng thời trên nhiều CPUs (parallelism).
- **Sinh ra để giải quyết vấn đề gì?** CPU nhanh hơn I/O rất nhiều — nếu chờ I/O mới làm việc khác thì lãng phí tài nguyên. Concurrency tận dụng thời gian chờ.
- **Công dụng?** Web server xử lý nhiều requests đồng thời, background tasks, parallel computation.
- **Vấn đề:** Race condition, deadlock, starvation — cần synchronization primitives (mutex, semaphore).

---

## Async/Await

- **Là gì?** Programming model cho concurrent I/O-bound tasks trên 1 thread — coroutine yield control khi chờ I/O, event loop chạy coroutine khác.
- **Sinh ra để giải quyết vấn đề gì?** Threading có overhead (context switch, memory per thread). Async đạt concurrency mà không cần nhiều threads — 1 thread handle hàng nghìn concurrent connections (Node.js model).
- **Công dụng?** Web servers (FastAPI, Node.js), database clients, HTTP clients.
- **Khi nào KHÔNG dùng?** CPU-bound tasks — async không giúp; dùng multiprocessing thay.

---

## Clean Code

- **Là gì?** Code dễ đọc, dễ hiểu, dễ maintain — "code as documentation".
- **Sinh ra để giải quyết vấn đề gì?** Code viết 1 lần nhưng đọc nhiều lần. Spaghetti code: thêm feature = nightmare, bug = không tìm được, onboard member mới = mất 2 tuần.
- **Principles:** DRY (Don't Repeat Yourself), KISS (Keep It Simple), YAGNI (You Ain't Gonna Need It), meaningful names, small functions, no magic numbers.
- **Khi nào code cần comment?** Khi WHY không rõ ràng — không comment WHAT (code đã nói rồi).

---

## Testing

- **Là gì?** Verify code hoạt động đúng — unit test (1 function), integration test (nhiều components), E2E test (full flow).
- **Sinh ra để giải quyết vấn đề gì?** Code không có test → refactor = sợ; add feature = không biết có break gì không; deploy = cầu nguyện.
- **Công dụng?** Confidence to refactor, regression detection, documentation (test = spec), force good design (testable code = loosely coupled).
- **TDD:** Test trước → code sau → refactor. Benefit: design tốt hơn, 100% coverage tự nhiên.

---

## Docker

- **Là gì?** Platform containerize applications — đóng gói app + dependencies vào container chạy nhất quán trên mọi môi trường.
- **Sinh ra để giải quyết vấn đề gì?** "It works on my machine" — dev environment khác production → bugs chỉ xuất hiện khi deploy. Container = same environment everywhere.
- **Công dụng?** Dev environment consistency, CI/CD, microservices deployment, local testing của production stack.
- **Image vs Container:** Image = template (như class); Container = running instance (như object).

---

## CI/CD

- **Là gì?** Continuous Integration (tự động build + test khi push code) + Continuous Delivery/Deployment (tự động deploy sau khi test pass).
- **Sinh ra để giải quyết vấn đề gì?** Manual testing và deployment: chậm, error-prone, "integration hell" khi merge code sau nhiều tuần.
- **Công dụng?** Phát hiện bug sớm, deploy nhanh hơn, giảm manual error, confidence khi release.
- **Deployment strategies:** Rolling (không downtime), Blue-Green (instant rollback), Canary (gradual rollout, detect issues early).
