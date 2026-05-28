# Trắc nghiệm: DFS & BFS

## Phân loại độ khó
- Cơ bản (câu 1–6): Khái niệm, định nghĩa, nhận biết
- Trung bình (câu 7–14): Phân tích, áp dụng
- Nâng cao (câu 15–20): Suy luận sâu, edge cases, optimization

---

### Câu 1 (Cơ bản)
DFS sử dụng cấu trúc dữ liệu nào để theo dõi các đỉnh cần thăm?

A. Queue (hàng đợi)
B. Stack (ngăn xếp)
C. Priority Queue
D. Heap

**Đáp án: B**

**Giải thích:**
- **B đúng**: DFS dùng stack (LIFO). Đỉnh được thêm vào stack và luôn lấy đỉnh gần nhất ra để duyệt tiếp, tạo ra hành vi "đi sâu trước". Trong cài đặt đệ quy, call stack của hệ thống chính là stack ẩn.
- A sai: Queue (FIFO) là cấu trúc của BFS, không phải DFS.
- C sai: Priority Queue dùng cho Dijkstra/A*.
- D sai: Heap là cấu trúc lưu trữ, không phải cấu trúc điều khiển duyệt.

---

### Câu 2 (Cơ bản)
BFS đảm bảo tìm được đường đi ngắn nhất trong loại đồ thị nào?

A. Weighted directed graph
B. Unweighted graph (có thể directed hoặc undirected)
C. Graph với negative weight
D. Chỉ trong tree, không phải graph tổng quát

**Đáp án: B**

**Giải thích:**
- **B đúng**: BFS đảm bảo shortest path trong unweighted graph (hoặc graph với tất cả edge weight bằng nhau) vì nó duyệt theo khoảng cách tăng dần — tất cả đỉnh ở distance d được thăm trước distance d+1.
- A sai: Weighted graph → cần Dijkstra (non-negative weight).
- C sai: Negative weight → cần Bellman-Ford.
- D sai: BFS hoạt động đúng trên graph tổng quát, không chỉ tree.

---

### Câu 3 (Cơ bản)
Cho binary tree sau:
```
    1
   / \
  2   3
 / \
4   5
```
Thứ tự duyệt Inorder (Left-Root-Right) là gì?

A. 1, 2, 4, 5, 3
B. 4, 2, 5, 1, 3
C. 4, 5, 2, 3, 1
D. 1, 2, 3, 4, 5

**Đáp án: B**

**Giải thích:**
- **B đúng**: Inorder = Left → Root → Right. Đi từ node 1: trái (subtree 2) → root 1 → phải (3). Subtree 2: trái (4) → root 2 → phải (5). Kết quả: 4, 2, 5, 1, 3.
- A sai: Đây là Preorder (Root-L-R).
- C sai: Đây là Postorder (L-R-Root).
- D sai: Không theo bất kỳ thứ tự DFS nào.

---

### Câu 4 (Cơ bản)
Space complexity của BFS trên một cây nhị phân đầy đủ (complete binary tree) có n đỉnh là bao nhiêu?

A. O(log n)
B. O(n)
C. O(n log n)
D. O(1)

**Đáp án: B**

**Giải thích:**
- **B đúng**: Queue của BFS trong worst case chứa tất cả đỉnh ở một tầng. Tầng cuối của complete binary tree có khoảng n/2 đỉnh → O(n/2) = O(n). Đây là lý do BFS tốn nhiều memory hơn DFS khi cây rộng.
- A sai: O(log n) là chiều cao của balanced tree — đó là space của DFS (call stack), không phải BFS.
- C, D sai: Không phản ánh đúng.

---

### Câu 5 (Cơ bản)
Trong BFS, tại sao phải đánh dấu một đỉnh là "visited" ngay khi **thêm vào queue**, không phải khi **lấy ra khỏi queue**?

A. Không có sự khác biệt, cả hai đều đúng
B. Để tránh thêm cùng một đỉnh vào queue nhiều lần
C. Vì BFS yêu cầu thứ tự LIFO
D. Để giảm time complexity từ O(V+E) xuống O(V)

**Đáp án: B**

**Giải thích:**
- **B đúng**: Nếu đánh dấu khi lấy ra, nhiều đỉnh hàng xóm có thể thêm cùng một đỉnh vào queue trước khi nó được lấy ra và đánh dấu. Kết quả: cùng một đỉnh xử lý nhiều lần → tăng độ phức tạp và có thể vô hạn vòng lặp.
- A sai: Có sự khác biệt quan trọng như đã giải thích.
- C sai: BFS dùng FIFO, không LIFO.
- D sai: Cả hai đều O(V+E) nếu đúng, nhưng đánh dấu muộn có thể làm worse case tệ hơn.

---

### Câu 6 (Cơ bản)
Preorder traversal của binary tree có ứng dụng thực tế nào?

A. Sắp xếp các phần tử của BST theo thứ tự tăng dần
B. Serialize (lưu) cấu trúc của một cây để có thể reconstruct lại
C. Tính tổng tất cả node trong cây
D. Tìm node có giá trị lớn nhất

**Đáp án: B**

**Giải thích:**
- **B đúng**: Preorder (Root-L-R) đảm bảo root được thăm trước khi con, nên khi deserialize, ta có thể tái tạo cây đúng cấu trúc. LeetCode 297: Serialize and Deserialize Binary Tree dùng preorder.
- A sai: Inorder của BST mới cho kết quả sorted.
- C sai: Có thể dùng bất kỳ traversal nào để tính tổng.
- D sai: Cũng có thể dùng bất kỳ traversal nào.

---

### Câu 7 (Trung bình)
Cho directed graph:
```
0 → 1 → 2
↑       ↓
└───────3
```
(0→1, 1→2, 2→3, 3→0). DFS từ đỉnh 0 sẽ phát hiện điều gì?

A. Graph không có cycle
B. Graph có cycle vì tồn tại back edge
C. Graph là DAG
D. DFS không thể detect cycle trong directed graph

**Đáp án: B**

**Giải thích:**
- **B đúng**: Khi DFS thăm: 0 (gray) → 1 (gray) → 2 (gray) → 3 (gray) → cố thăm 0, nhưng 0 đang là gray (đang trong call stack). Đây là back edge 3→0 → xác nhận có cycle.
- A, C sai: Graph này có cycle 0→1→2→3→0.
- D sai: DFS hoàn toàn có thể detect cycle qua 3 màu (white/gray/black).

---

### Câu 8 (Trung bình)
Time complexity của BFS và DFS trên một graph có V đỉnh và E cạnh là bao nhiêu?

A. BFS: O(V²), DFS: O(V²)
B. BFS: O(V+E), DFS: O(V²)
C. BFS: O(V+E), DFS: O(V+E)
D. BFS: O(E log V), DFS: O(V+E)

**Đáp án: C**

**Giải thích:**
- **C đúng**: Cả BFS và DFS đều thăm mỗi đỉnh đúng 1 lần (O(V)) và xét mỗi cạnh tối đa 2 lần/1 lần (O(E)), nên tổng là O(V+E) với adjacency list.
- A sai: O(V²) chỉ xảy ra khi dùng adjacency matrix (mỗi đỉnh xét V neighbor).
- B sai: DFS cũng là O(V+E) với adjacency list.
- D sai: O(E log V) là complexity của Dijkstra.

---

### Câu 9 (Trung bình)
Trong BFS trên unweighted graph, giả sử source là S. Một đỉnh v được thêm vào queue ở level k. Điều này có nghĩa là gì?

A. v có đúng k neighbors
B. Đường đi ngắn nhất từ S đến v qua đúng k đỉnh trung gian
C. Khoảng cách ngắn nhất từ S đến v là k (tức là k cạnh)
D. v là đỉnh thứ k được thăm

**Đáp án: C**

**Giải thích:**
- **C đúng**: BFS duyệt theo từng level. Level 0: chỉ có S (dist=0). Level 1: các neighbor trực tiếp của S (dist=1). Level k: các đỉnh cách S đúng k cạnh. Đây là tính chất cốt lõi đảm bảo shortest path của BFS.
- A sai: Số neighbor không liên quan đến level trong BFS.
- B sai: k cạnh có nghĩa là k-1 đỉnh trung gian, không phải k đỉnh trung gian.
- D sai: Thứ tự thăm không nhất thiết tương ứng với level.

---

### Câu 10 (Trung bình)
Cho cây nhị phân có chiều cao h và n đỉnh. Space complexity của DFS đệ quy là bao nhiêu?

A. O(n) trong mọi trường hợp
B. O(h), với h từ O(log n) đến O(n) tùy cây
C. O(1)
D. O(n log n)

**Đáp án: B**

**Giải thích:**
- **B đúng**: DFS đệ quy dùng call stack, sâu tối đa h (chiều cao cây). Balanced tree: h = O(log n). Skewed tree (linked list): h = O(n). Nên space từ O(log n) đến O(n).
- A sai: O(n) chỉ đúng khi cây là skewed (degenerate), không phải mọi trường hợp.
- C sai: Luôn cần ít nhất O(h) stack space.
- D sai: Không có trường hợp O(n log n) space.

---

### Câu 11 (Trung bình)
Bài toán "Rotting Oranges" (LeetCode 994): Có ma trận ô vuông, mỗi ô là 0 (trống), 1 (cam tươi), 2 (cam thối). Mỗi phút, cam thối lây sang 4 ô lân cận. Tìm thời gian tối thiểu để tất cả cam thối. Thuật toán nào phù hợp nhất?

A. DFS từ từng cam thối
B. Multi-source BFS từ tất cả cam thối cùng lúc
C. DFS từ từng cam tươi
D. Dijkstra từ từng cam thối

**Đáp án: B**

**Giải thích:**
- **B đúng**: Cam thối lây song song đồng thời từ nhiều điểm → Multi-source BFS. Thêm tất cả cam thối vào queue ban đầu (distance=0), BFS lan ra đồng đều. Thời gian tối thiểu = max distance trong BFS.
- A sai: DFS không đảm bảo tối thiểu hóa thời gian, và cần nhiều lần DFS.
- C sai: Không cần BFS/DFS từ cam tươi.
- D sai: Dijkstra quá phức tạp và không cần thiết (tất cả edge weight = 1).

---

### Câu 12 (Trung bình)
Khi nào Iterative DFS và Recursive DFS cho kết quả thứ tự thăm **khác nhau**?

A. Luôn luôn khác nhau
B. Khi không push neighbors theo thứ tự ngược vào stack trong iterative
C. Khi graph có cycle
D. Khi graph là disconnected

**Đáp án: B**

**Giải thích:**
- **B đúng**: Stack là LIFO. Nếu ta push neighbors theo thứ tự [A, B, C], thì pop sẽ lấy C trước. Recursive DFS thăm A trước. Để iterative khớp với recursive, phải push theo thứ tự ngược: [C, B, A], thì pop lấy A trước. Nếu không làm vậy, thứ tự sẽ khác.
- A sai: Hai cách cho kết quả giống nhau nếu cẩn thận về thứ tự push.
- C, D sai: Cycle và disconnected graph không ảnh hưởng đến vấn đề này.

---

### Câu 13 (Trung bình)
Graph bipartite là graph mà đỉnh có thể tô 2 màu sao cho không có 2 đỉnh kề nhau cùng màu. Thuật toán nào detect được graph bipartite?

A. Chỉ BFS
B. Chỉ DFS
C. Cả BFS và DFS đều có thể
D. Cần thuật toán đặc biệt, không dùng BFS/DFS được

**Đáp án: C**

**Giải thích:**
- **C đúng**: Cả hai đều có thể tô màu 2 màu trong khi duyệt. BFS: tô màu xen kẽ theo từng level. DFS: tô màu xen kẽ khi đi sâu, kiểm tra khi gặp back/cross edge. Nếu tô màu thất bại (2 đỉnh kề cùng màu) → không phải bipartite.
- A, B sai: Cả hai đều làm được.
- D sai: Không cần thuật toán đặc biệt.

---

### Câu 14 (Trung bình)
0-1 BFS khác BFS thường ở điểm gì?

A. Dùng stack thay vì queue
B. Dùng deque: edge weight 0 → appendleft, edge weight 1 → append
C. Chỉ áp dụng cho tree, không cho graph
D. Luôn chậm hơn BFS thường

**Đáp án: B**

**Giải thích:**
- **B đúng**: 0-1 BFS xử lý graph với edge weight 0 hoặc 1. Edge weight 0 không tăng distance → thêm vào đầu deque (ưu tiên xử lý trước). Edge weight 1 tăng distance 1 → thêm vào cuối. Kết quả: O(V+E) thay vì O((V+E)logV) của Dijkstra.
- A sai: Dùng deque (double-ended queue), không phải stack.
- C sai: Hoạt động trên graph tổng quát.
- D sai: Nhanh hơn Dijkstra trong trường hợp 0-1 weight.

---

### Câu 15 (Nâng cao)
Bidirectional BFS từ source S và target T. Giả sử branching factor b, khoảng cách ngắn nhất d. Complexity là O(b^(d/2)). Tại sao lại nhỏ hơn BFS thường O(b^d)?

A. Vì chỉ cần duyệt nửa đỉnh
B. Vì frontier của mỗi bên chỉ rộng b^(d/2) khi gặp nhau ở giữa, tổng là 2×b^(d/2) << b^d
C. Vì Bidirectional BFS dùng DFS một chiều
D. Vì b/2 nhỏ hơn b

**Đáp án: B**

**Giải thích:**
- **B đúng**: BFS thường: frontier ở depth d có b^d nodes. Bidirectional: mỗi bên expand đến d/2, frontier có b^(d/2) nodes. Tổng = 2×b^(d/2). Ví dụ: b=10, d=6 → BFS thường: 10^6 = 1,000,000; Bidirectional: 2×10^3 = 2,000, nhỏ hơn 500 lần!
- A sai: Không phải đơn giản là "nửa đỉnh" — số đỉnh giảm theo hàm mũ.
- C sai: Bidirectional BFS dùng BFS cả hai chiều.
- D sai: Branching factor không thay đổi, chỉ depth giảm đi một nửa.

---

### Câu 16 (Nâng cao)
Trong DFS trên directed graph, "forward edge" và "cross edge" khác nhau thế nào?

A. Forward edge và cross edge là như nhau
B. Forward edge: u→v với v là hậu duệ đã thăm xong (d[u]<d[v]). Cross edge: u→v với v không có quan hệ tổ tiên/hậu duệ (d[u]>d[v])
C. Forward edge chỉ tồn tại trong undirected graph
D. Cross edge là edge từ một SCC sang SCC khác, forward edge là edge trong cùng SCC

**Đáp án: B**

**Giải thích:**
- **B đúng**: Sử dụng discovery time d[] và finish time f[]: Tree edge và Forward edge: d[u] < d[v] (v phát hiện sau u). Back edge: d[v] < d[u] và v chưa xong (v là tổ tiên). Cross edge: f[v] < d[u] (v đã xong hoàn toàn trước khi u được phát hiện). Forward edge dẫn đến hậu duệ, cross edge dẫn đến node đã xong không liên quan.
- A sai: Chúng khác nhau về quan hệ với DFS tree.
- C sai: Undirected graph không có forward hay cross edge — chỉ có tree edge và back edge.
- D sai: Định nghĩa theo SCC không chính xác.

---

### Câu 17 (Nâng cao)
Thuật toán Kosaraju tìm SCC (Strongly Connected Components) thực hiện mấy lần DFS và trên graph nào?

A. 1 lần DFS trên graph gốc
B. 2 lần DFS: lần 1 trên graph gốc để lấy finish order, lần 2 trên transposed graph theo reverse finish order
C. 2 lần DFS: cả hai lần trên graph gốc
D. 3 lần DFS trên graph gốc

**Đáp án: B**

**Giải thích:**
- **B đúng**: Kosaraju: (1) DFS trên graph gốc G, ghi lại finish time. (2) Tạo transposed graph G^T (đảo chiều tất cả cạnh). (3) DFS trên G^T theo thứ tự finish time giảm dần — mỗi lần DFS trong bước này tìm ra một SCC. Complexity: O(V+E).
- A sai: Một lần DFS không đủ để tìm tất cả SCC.
- C sai: Bước 2 cần transposed graph để phá vỡ các cạnh cross giữa SCC.
- D sai: Chỉ cần 2 lần DFS.

---

### Câu 18 (Nâng cao)
Cho graph sau (adjacency list), DFS từ node 0:
```
0: [1, 2]
1: [3]
2: [3]
3: []
```
Nếu dùng iterative DFS với stack, push theo thứ tự thuận (không reversed), thứ tự thăm là gì?

A. 0, 1, 3, 2
B. 0, 2, 3, 1
C. 0, 1, 2, 3
D. 0, 2, 1, 3

**Đáp án: B**

**Giải thích:**
- **B đúng**: Stack ban đầu: [0]. Pop 0, push neighbors [1, 2] → stack: [1, 2]. Pop 2 (LIFO!), push neighbor [3] → stack: [1, 3]. Pop 3, không có neighbor → stack: [1]. Pop 1, push neighbor [3] nhưng 3 đã visited → stack rỗng. Thứ tự: 0, 2, 3, 1.
- A sai: Đây là kết quả của recursive DFS hoặc iterative với reversed push.
- C, D sai: Không khớp với LIFO behavior.

---

### Câu 19 (Nâng cao)
Xét bài toán "Word Ladder" (LeetCode 127): Tìm shortest transformation sequence từ word `beginWord` đến `endWord`, mỗi bước đổi 1 chữ cái. Thuật toán nào tối ưu?

A. DFS
B. BFS thường từ beginWord
C. Bidirectional BFS từ cả beginWord lẫn endWord
D. Dynamic Programming

**Đáp án: C**

**Giải thích:**
- **C đúng**: Word Ladder cần shortest path → BFS. Nhưng dictionary có thể rất lớn (branching factor cao). Bidirectional BFS giảm complexity từ O(b^d) xuống O(b^(d/2)). Trong practice, Bidirectional BFS nhanh hơn nhiều lần với input lớn.
- A sai: DFS không đảm bảo shortest path.
- B sai: BFS đơn hướng chậm hơn bidirectional khi branching factor lớn.
- D sai: DP không phù hợp vì không có cấu trúc optimal substructure rõ ràng ở đây.

---

### Câu 20 (Nâng cao)
Cho một maze (ma trận m×n), ô 'S' là start, 'E' là end, '#' là tường, '.' là lối đi. Tìm đường đi ngắn nhất từ S đến E. Phân tích space complexity của BFS cho bài này.

A. O(1) — vì chỉ cần lưu path
B. O(m×n) — vì queue có thể chứa tất cả ô
C. O(m+n) — vì chiều rộng queue tối đa là m+n
D. O(min(m,n)) — vì chiều dài path tối đa

**Đáp án: B**

**Giải thích:**
- **B đúng**: Trong worst case, queue BFS có thể chứa gần như tất cả m×n ô (ví dụ maze toàn lối đi không có tường). Ngoài queue, cần visited array m×n và distance/parent array m×n. Total space = O(m×n).
- A sai: Không thể O(1) vì cần visited set ít nhất.
- C sai: Queue frontier rộng tối đa là một layer của BFS, có thể là O(m×n) trong trường hợp đặc biệt.
- D sai: Không liên quan đến space của BFS.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Độ khó |
|-----|--------|--------|
| 1 | B | Cơ bản |
| 2 | B | Cơ bản |
| 3 | B | Cơ bản |
| 4 | B | Cơ bản |
| 5 | B | Cơ bản |
| 6 | B | Cơ bản |
| 7 | B | Trung bình |
| 8 | C | Trung bình |
| 9 | C | Trung bình |
| 10 | B | Trung bình |
| 11 | B | Trung bình |
| 12 | B | Trung bình |
| 13 | C | Trung bình |
| 14 | B | Trung bình |
| 15 | B | Nâng cao |
| 16 | B | Nâng cao |
| 17 | B | Nâng cao |
| 18 | B | Nâng cao |
| 19 | C | Nâng cao |
| 20 | B | Nâng cao |
