# Trắc nghiệm — Graph

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Graph gồm các thành phần cơ bản nào?

- A. Node và edge (đỉnh và cạnh)
- B. Root và leaf
- C. Key và value
- D. Front và rear

> **Đáp án: A**  
> **Giải thích:** Graph G = (V, E) gồm tập đỉnh V và tập cạnh E. Edge nối 2 đỉnh (có thể có hướng hoặc không).

---

**Câu 2:** Adjacency list lưu graph có complexity space là?

- A. O(V)
- B. O(E)
- C. O(V + E)
- D. O(V²)

> **Đáp án: C**  
> **Giải thích:** Cần V slot cho mỗi đỉnh + tổng E entry cho cạnh (mỗi cạnh xuất hiện 1 lần trong directed, 2 lần trong undirected). Tổng O(V + E). Adjacency matrix là O(V²).

---

**Câu 3:** Khi nào nên dùng adjacency matrix thay vì adjacency list?

- A. Khi graph dày (dense, E ~ V²) hoặc cần check cạnh (u, v) tồn tại trong O(1)
- B. Khi graph thưa
- C. Khi V rất lớn
- D. Không bao giờ

> **Đáp án: A**  
> **Giải thích:** Matrix: kiểm tra cạnh O(1), nhưng tốn O(V²) space ngay cả khi cạnh ít. List: kiểm tra cạnh O(degree), tốn O(V+E) space. Sparse graph → list; dense graph hoặc cần edge query thường xuyên → matrix.

---

**Câu 4:** DFS dùng cấu trúc nào?

- A. Queue
- B. Stack (tường minh hoặc call stack đệ quy)
- C. Heap
- D. Hash map

> **Đáp án: B**  
> **Giải thích:** DFS đi sâu trước → cần LIFO. Recursion ngầm dùng call stack. Iterative dùng stack tường minh.

---

**Câu 5:** Complexity của BFS/DFS trên graph dùng adjacency list là?

- A. O(V)
- B. O(E)
- C. O(V + E)
- D. O(V × E)

> **Đáp án: C**  
> **Giải thích:** Mỗi đỉnh visited 1 lần O(V). Mỗi cạnh duyệt 1 lần (directed) hoặc 2 lần (undirected) — tổng O(E). Cộng lại O(V + E).

---

**Câu 6:** Sự khác biệt giữa directed và undirected graph?

- A. Directed có hướng (u → v không tương đương v → u); undirected: cạnh 2 chiều
- B. Directed có ít cạnh hơn
- C. Directed luôn có cycle
- D. Không khác biệt

> **Đáp án: A**  
> **Giải thích:** Directed: edge có hướng, mô hình quan hệ asymmetric (follow trên Twitter). Undirected: edge 2 chiều, mô hình quan hệ symmetric (friend trên FB).

---

**Câu 7:** "Cycle" trong graph là?

- A. Đường đi bắt đầu và kết thúc tại cùng 1 đỉnh
- B. Đỉnh có self-loop
- C. Edge weight âm
- D. Graph không kết nối

> **Đáp án: A**  
> **Giải thích:** Cycle = path có start = end và độ dài ≥ 1 (≥ 3 cho undirected simple cycle để tránh tính edge ngược lại). DAG = directed acyclic graph (không có cycle).

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Thuật toán nào tìm shortest path trong graph **không trọng số**?

- A. Dijkstra
- B. BFS
- C. DFS
- D. Floyd-Warshall

> **Đáp án: B**  
> **Giải thích:** BFS đảm bảo thăm node theo distance tăng dần (số cạnh) → node được phát hiện đầu tiên có distance ngắn nhất. Dijkstra cho graph có trọng số dương.

---

**Câu 9:** Dijkstra cho shortest path KHÔNG hoạt động đúng khi?

- A. Graph có trọng số âm
- B. Graph có nhiều đỉnh
- C. Graph có hướng
- D. Graph không kết nối

> **Đáp án: A**  
> **Giải thích:** Dijkstra giả định "node đã settle có distance tối ưu" — sai khi có cạnh âm (có thể có đường đi qua cạnh âm tốt hơn). Dùng Bellman-Ford cho trọng số âm (không cycle âm).

---

**Câu 10:** Complexity của Dijkstra với min-heap (priority queue) là?

- A. O(V + E)
- B. O((V + E) log V)
- C. O(V²)
- D. O(V³)

> **Đáp án: B**  
> **Giải thích:** Mỗi đỉnh extract-min O(log V), mỗi cạnh có thể push O(log V). Tổng O((V + E) log V). Với matrix + linear scan: O(V²). Với Fibonacci heap: O(E + V log V).

---

**Câu 11:** Detect cycle trong directed graph dùng?

- A. BFS đếm in-degree
- B. DFS với 3 màu (white/gray/black) hoặc topological sort thất bại
- C. Union-Find
- D. Cả B đều đúng (Union-Find cho undirected)

> **Đáp án: B**  
> **Giải thích:** Directed: DFS, khi gặp gray node (đang trong stack đệ quy hiện tại) → có back edge → cycle. Union-Find chỉ dùng cho undirected (tính bipartite/cycle qua kết nối). BFS Kahn's algorithm cũng detect được — không xếp đủ V node → có cycle.

---

**Câu 12:** Topological Sort yêu cầu graph phải là?

- A. Tree
- B. DAG (Directed Acyclic Graph)
- C. Undirected
- D. Có trọng số dương

> **Đáp án: B**  
> **Giải thích:** Topological order chỉ tồn tại trên DAG. Có cycle → không thể có thứ tự thỏa "u trước v cho mọi edge u→v". Xem chuyên đề 17_topological_sort.

---

**Câu 13:** Connected components (thành phần liên thông) đếm bằng?

- A. DFS/BFS từ mỗi đỉnh chưa visited, tăng counter mỗi lần
- B. Sort
- C. Dijkstra
- D. Binary search

> **Đáp án: A**  
> **Giải thích:** Đếm số lần khởi động DFS/BFS mới. Mỗi lần khởi động duyệt 1 component. Union-Find cũng giải quyết được, đặc biệt khi cạnh được thêm dần online.

---

**Câu 14:** MST (Minimum Spanning Tree) thường được tìm bằng?

- A. Dijkstra
- B. Kruskal (sort edge + Union-Find) hoặc Prim (greedy + heap)
- C. Bellman-Ford
- D. DFS

> **Đáp án: B**  
> **Giải thích:** Kruskal O(E log E) sort cạnh, lấy theo trọng số tăng, dùng Union-Find tránh cycle. Prim O(E log V) tương tự Dijkstra: greedy mở rộng cây từ 1 đỉnh, mỗi bước lấy cạnh nhẹ nhất nối cây với đỉnh ngoài.

---

**Câu 15:** Bipartite graph là?

- A. Graph có thể chia đỉnh thành 2 tập sao cho mọi cạnh nối 2 tập khác nhau (không có cạnh trong cùng tập)
- B. Graph có 2 component
- C. Graph có 2 chu trình
- D. Graph có hướng

> **Đáp án: A**  
> **Giải thích:** Tô màu 2 màu sao cho 2 đỉnh kề khác màu → bipartite. Tương đương: không có cycle độ dài lẻ. Detect bằng BFS/DFS gán màu xen kẽ.

---

**Câu 16:** Floyd-Warshall (all-pairs shortest path) complexity?

- A. O(V²)
- B. O(V³)
- C. O(V² log V)
- D. O(VE log V)

> **Đáp án: B**  
> **Giải thích:** 3 vòng for V × V × V, DP qua intermediate node. Cho all-pairs, có thể handle weight âm (không cycle âm). Với graph thưa, V lần Dijkstra (O(V × (V+E) log V)) tốt hơn.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Bellman-Ford detect cycle âm bằng cách?

- A. Sau V-1 vòng relax tất cả cạnh, chạy thêm 1 vòng nữa — nếu vẫn còn relax thành công → có cycle âm
- B. Đếm số cạnh
- C. Check cạnh đầu tiên
- D. Dùng heap

> **Đáp án: A**  
> **Giải thích:** Shortest path không cycle dài tối đa V-1 cạnh. Sau V-1 lần relax đầy đủ, distance đã optimal nếu không có cycle âm. Lần thứ V vẫn cải thiện → tồn tại cycle âm có thể giảm distance vô hạn.

---

**Câu 18:** Strongly Connected Components (SCC) trong directed graph bằng Tarjan/Kosaraju có complexity?

- A. O(V × E)
- B. O(V + E)
- C. O(V²)
- D. O(V³)

> **Đáp án: B**  
> **Giải thích:** Kosaraju: 2 lần DFS (DFS gốc lấy finish order + DFS transpose). Tarjan: 1 lần DFS với low-link values. Cả hai O(V + E). Tarjan thường nhanh hơn trong practice (1 lần DFS).

---

**Câu 19:** A* search là gì so với Dijkstra?

- A. Dijkstra + heuristic h(n) ước lượng distance tới đích. Priority = g(n) + h(n). Khi h admissible (không over-estimate), A* optimal
- B. Random search
- C. DFS với pruning
- D. BFS với bias

> **Đáp án: A**  
> **Giải thích:** A* khai thác thông tin về đích (heuristic) để giảm số node phải explore. Nếu h(n) = 0 → A* = Dijkstra. Heuristic tốt (vd Euclidean cho path planning) tăng tốc đáng kể.

---

**Câu 20:** Bài "Number of Islands" (matrix 0/1) giải bằng?

- A. DFS/BFS từ mỗi ô '1' chưa visited, đánh dấu visited cả component → đếm số lần khởi động
- B. Sort
- C. Dynamic programming
- D. Binary search

> **Đáp án: A**  
> **Giải thích:** Mỗi "đảo" là 1 connected component. Duyệt matrix, gặp '1' chưa visited → DFS/BFS mark toàn component, tăng counter. O(M × N). Union-Find cũng giải được (hữu ích cho variant dynamic).

---

**Câu 21:** Trong DFS trên directed graph, "back edge" là cạnh đi tới?

- A. Tổ tiên của node hiện tại trong DFS tree → chỉ thị cycle
- B. Hậu duệ
- C. Node ở cây khác (cross edge)
- D. Node chưa thăm

> **Đáp án: A**  
> **Giải thích:** Edge u → v với v là ancestor trong DFS tree → tạo cycle. Phát hiện back edge = detect cycle trong directed. Đối với undirected, back edge cũng chỉ thị cycle (trừ edge ngược lại parent trực tiếp).

---

**Câu 22:** Union-Find (Disjoint Set) với path compression + union by rank có complexity gần như?

- A. O(1) per op
- B. O(log n) per op
- C. O(α(n)) per op (inverse Ackermann ≤ 4 cho mọi n thực tế)
- D. O(n) per op

> **Đáp án: C**  
> **Giải thích:** Inverse Ackermann function α(n) tăng cực chậm, ≤ 4 cho n ≤ 10^80. Trong thực tế coi như O(1). Đây là cấu trúc cơ bản cho Kruskal MST, detect cycle undirected, dynamic connectivity.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | A      | 12  | B      |
| 2   | C      | 13  | A      |
| 3   | A      | 14  | B      |
| 4   | B      | 15  | A      |
| 5   | C      | 16  | B      |
| 6   | A      | 17  | A      |
| 7   | A      | 18  | B      |
| 8   | B      | 19  | A      |
| 9   | A      | 20  | A      |
| 10  | B      | 21  | A      |
| 11  | B      | 22  | C      |
