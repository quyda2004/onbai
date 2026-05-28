# Trắc nghiệm: Topological Sort

## Phân loại độ khó
- Cơ bản (câu 1–6): Khái niệm, định nghĩa
- Trung bình (câu 7–14): Áp dụng, phân tích
- Nâng cao (câu 15–20): Suy luận sâu, edge cases

---

### Câu 1 (Cơ bản)
Topological Sort có thể áp dụng cho loại đồ thị nào?

A. Bất kỳ directed graph nào
B. Chỉ undirected graph
C. Directed Acyclic Graph (DAG)
D. Complete graph

**Đáp án: C**

**Giải thích:**
- **C đúng**: Topological sort chỉ tồn tại và có ý nghĩa trên DAG. "Directed" vì cạnh có hướng thể hiện quan hệ thứ tự (u trước v). "Acyclic" vì nếu có cycle, không thể tạo thứ tự tuyến tính (mâu thuẫn vòng tròn).
- A sai: Directed graph có thể có cycle → topological sort không tồn tại.
- B sai: Undirected graph không có hướng cạnh → khái niệm "A phải trước B" không áp dụng.
- D sai: Complete directed graph thường có cycle (edge cả hai chiều giữa mọi cặp).

---

### Câu 2 (Cơ bản)
Trong Kahn's Algorithm, đỉnh nào được đưa vào queue đầu tiên?

A. Đỉnh có out-degree = 0
B. Đỉnh có in-degree = 0
C. Đỉnh có chỉ số nhỏ nhất
D. Đỉnh được thăm đầu tiên trong DFS

**Đáp án: B**

**Giải thích:**
- **B đúng**: In-degree = 0 nghĩa là không có tiên quyết (không có cạnh đi vào). Những đỉnh này có thể được xử lý ngay lập tức vì không phụ thuộc vào đỉnh khác. Đây là "source vertices" trong DAG.
- A sai: Out-degree = 0 là "sink vertices" — những đỉnh cuối cùng trong thứ tự.
- C sai: Chỉ số nhỏ nhất không đảm bảo là source.
- D sai: Kahn's không dùng DFS.

---

### Câu 3 (Cơ bản)
Cho DAG với 5 đỉnh và kết quả Kahn's Algorithm chỉ có 3 đỉnh. Điều này cho thấy gì?

A. Graph có 3 đỉnh là source
B. Có cycle trong graph — topological sort không hoàn chỉnh
C. Thuật toán có lỗi
D. Graph không liên thông

**Đáp án: B**

**Giải thích:**
- **B đúng**: Kahn's cycle detection: nếu result.length < n, nghĩa là còn 2 đỉnh chưa được xử lý. Các đỉnh này không bao giờ vào queue vì in-degree của chúng > 0 (do cycle). Đây là cách Kahn's detect cycle.
- A sai: Source là đỉnh có in-degree=0 ban đầu, không liên quan đến kết quả cuối.
- C sai: Đây là behavior có chủ ý để detect cycle.
- D sai: Disconnected graph vẫn cho result đầy đủ nếu không có cycle (DFS/BFS xử lý từng component).

---

### Câu 4 (Cơ bản)
Thứ tự topological của một DAG có thể có bao nhiêu kết quả hợp lệ?

A. Đúng 1
B. Tối đa 2
C. Có thể nhiều hơn 1 — không duy nhất
D. Luôn bằng n! với n đỉnh

**Đáp án: C**

**Giải thích:**
- **C đúng**: Topological order không duy nhất. Ví dụ: graph chỉ có cạnh 0→2, không có cạnh liên quan đến 1 → cả [0, 1, 2] và [1, 0, 2] đều hợp lệ. Khi có nhiều source cùng lúc, có thể chọn bất kỳ source nào trước.
- A sai: Chỉ duy nhất khi graph là một chain tuyến tính.
- B sai: Không giới hạn ở 2.
- D sai: n! chỉ đúng khi graph không có cạnh nào — mọi thứ tự đều hợp lệ.

---

### Câu 5 (Cơ bản)
Trong DFS-based topological sort, đỉnh được thêm vào result khi nào?

A. Ngay khi phát hiện (discovery time)
B. Khi DFS hoàn tất xong subtree của đỉnh đó (finish time / postorder)
C. Theo thứ tự BFS
D. Theo thứ tự chỉ số đỉnh

**Đáp án: B**

**Giải thích:**
- **B đúng**: Postorder — thêm đỉnh vào result khi hoàn tất xong tất cả neighbor. Đỉnh không có dependency được thêm trước (vì DFS xong nhanh hơn). Sau khi reverse → đỉnh source (có nhiều dependency outgoing) đứng đầu. Đây là tính chất "finish time" trong DFS.
- A sai: Discovery time (preorder) không tạo ra topological order đúng.
- C, D sai: DFS-based không dùng BFS hay sort theo chỉ số.

---

### Câu 6 (Cơ bản)
Ứng dụng nào SAU ĐÂY là ví dụ điển hình của topological sort?

A. Tìm đường đi ngắn nhất trong mạng đường bộ
B. Xác định thứ tự compile các module trong build system
C. Tìm kiếm một phần tử trong array đã sort
D. Cân bằng tải server

**Đáp án: B**

**Giải thích:**
- **B đúng**: Build system là ứng dụng kinh điển. Module A depend on B → phải compile B trước A → đây là topological ordering trên dependency DAG.
- A sai: Shortest path → Dijkstra/BFS.
- C sai: Binary search.
- D sai: Load balancing không liên quan đến graph ordering.

---

### Câu 7 (Trung bình)
Cho DAG sau:
```
edges = [(0,1), (0,2), (1,3), (2,3), (3,4)]
```
Có bao nhiêu topological ordering hợp lệ?

A. 1
B. 2
C. 3
D. 4

**Đáp án: B**

**Giải thích:**
- **B đúng**: Source: {0}. Sau khi xử lý 0: sources = {1, 2}. Có thể chọn 1 hoặc 2 trước. Nếu chọn 1: {2} → rồi {3} → {4}. Nếu chọn 2: {1} → rồi {3} → {4}. Kết quả: [0,1,2,3,4] và [0,2,1,3,4]. Đúng 2 thứ tự hợp lệ. Sau khi xử lý cả 1 và 2, chỉ còn {3} → duy nhất.
- A sai: Có hai lựa chọn cho thứ hai.
- C, D sai: Không có thêm nhánh lựa chọn nào.

---

### Câu 8 (Trung bình)
Time complexity của Kahn's Algorithm với adjacency list là?

A. O(V²)
B. O(V + E)
C. O(V log V)
D. O(E log E)

**Đáp án: B**

**Giải thích:**
- **B đúng**: Khởi tạo in-degree: O(V+E) (duyệt adjacency list). Tìm source ban đầu: O(V). BFS: mỗi đỉnh xử lý đúng 1 lần (O(V)), mỗi cạnh xét đúng 1 lần khi giảm in-degree neighbor (O(E)). Tổng: O(V+E).
- A sai: O(V²) xảy ra khi dùng adjacency matrix hoặc nested loop không cần thiết.
- C sai: O(V log V) là khi cần lexicographic order (dùng heap).
- D sai: Không có sorting nào của E.

---

### Câu 9 (Trung bình)
Trong DFS-based topological sort, "gray → gray" edge (đỉnh đang thăm → đỉnh đang thăm khác) cho biết điều gì?

A. Đây là forward edge, không có cycle
B. Đây là cross edge, graph liên thông tốt
C. Đây là back edge, xác nhận có cycle
D. Đây là tree edge bình thường

**Đáp án: C**

**Giải thích:**
- **C đúng**: Trong DFS, "gray" (visiting) nghĩa là đỉnh đang trong call stack hiện tại. Cạnh từ gray node đến gray node khác nghĩa là đang đi từ một đỉnh đến tổ tiên của nó trong DFS tree — đây là definition của back edge. Back edge ↔ cycle tồn tại.
- A sai: Forward edge đi đến descendant đã BLACK (đã xong), không phải gray.
- B sai: Cross edge đến đỉnh đã BLACK hoàn toàn.
- D sai: Tree edge đến đỉnh WHITE (chưa thăm).

---

### Câu 10 (Trung bình)
Bạn có dependency graph của các package (A depends on B nghĩa là cạnh A→B). Khi chạy Kahn's Algorithm, queue ban đầu trống. Điều này nghĩa là gì?

A. Không có package nào cần cài
B. Tất cả packages đều có ít nhất một dependency → có cycle
C. Graph rỗng
D. Kahn's Algorithm không áp dụng được

**Đáp án: B**

**Giải thích:**
- **B đúng**: Queue ban đầu trống = không có đỉnh nào có in-degree = 0 = mọi đỉnh đều có ít nhất một cạnh đi vào = mọi package đều depend on package khác. Trong một tập hữu hạn, điều này chỉ có thể xảy ra nếu có cycle (vòng tròn phụ thuộc). Kahn's phát hiện: result.length = 0 < n.
- A sai: Queue trống không nghĩa là không có package.
- C sai: Graph có đỉnh nhưng mọi đỉnh có in-degree > 0.
- D sai: Kahn's áp dụng được và chính xác phát hiện cycle này.

---

### Câu 11 (Trung bình)
Cho bài toán "Course Schedule II" (LeetCode 210): Trả về thứ tự học hợp lệ, [] nếu có cycle. Đoạn code sau có lỗi gì?

```python
def findOrder(numCourses, prerequisites):
    adj = [[] for _ in range(numCourses)]
    indegree = [0] * numCourses
    for a, b in prerequisites:
        adj[a].append(b)  # Dòng này
        indegree[b] += 1  # Dòng này
    # ... Kahn's algorithm
```

A. Không có lỗi
B. Chiều cạnh bị ngược: prerequisites[i] = [a, b] nghĩa là b là tiên quyết của a → cạnh phải từ b đến a
C. indegree nên là indegree[a] += 1
D. adj nên dùng dict thay vì list

**Đáp án: B**

**Giải thích:**
- **B đúng**: prerequisites = [a, b] nghĩa là "phải học b trước a" → b là tiên quyết của a → cạnh đi từ b đến a (b→a). Code trên build cạnh a→b → sai chiều! Đúng phải là: `adj[b].append(a)` và `indegree[a] += 1`. Chiều cạnh ảnh hưởng trực tiếp đến kết quả topological sort.
- A sai: Có lỗi logic.
- C sai: indegree[a] đúng về mặt đỉnh nhận cạnh, nhưng vấn đề là cả hướng adj[a].append(b) cũng sai.
- D sai: List hoạt động tốt cho graph với n đỉnh đánh số từ 0.

---

### Câu 12 (Trung bình)
Topological sort và DFS thông thường khác nhau như thế nào về output?

A. Giống nhau hoàn toàn
B. DFS cho discovery order; Topological sort cho reverse finish order
C. DFS cho sorted output; Topological sort cho unsorted output
D. Topological sort luôn nhanh hơn DFS

**Đáp án: B**

**Giải thích:**
- **B đúng**: DFS thông thường trả về thứ tự phát hiện (discovery/preorder). Topological sort (DFS-based) cần reverse postorder (finish time). Hai thứ khác nhau: discovery order không đảm bảo topological property, nhưng reverse finish order thì có (node finish muộn hơn = source = đứng đầu khi reverse).
- A sai: Output khác nhau.
- C sai: DFS không sort.
- D sai: Cùng O(V+E) complexity.

---

### Câu 13 (Trung bình)
Trong build system, file A.cpp include B.h và C.h; B.h include D.h. Graph dependency là gì và topological sort sẽ compile theo thứ tự nào?

A. Graph: A→B, A→C, B→D. Compile: D, B/C (theo thứ tự tùy), A
B. Graph: B→A, C→A, D→B. Compile: A, B, C, D
C. Không thể dùng topological sort cho bài này
D. Compile theo alphabetical order: A, B, C, D

**Đáp án: A**

**Giải thích:**
- **A đúng**: Dependency có nghĩa "A cần B" → cạnh A→B (A depend on B). Để build A, phải build B trước → topological sort đặt B trước A. Kết quả: D trước (không depend gì), rồi B và C (đã có D), rồi A (đã có B và C). Thứ tự: D, B, C, A hoặc D, C, B, A.
- B sai: Chiều cạnh ngược — nếu B→A thì B không depend A, A depend B là cạnh A→B.
- C sai: Đây chính xác là use case của topological sort.
- D sai: Alphabetical không đảm bảo dependency order.

---

### Câu 14 (Trung bình)
Lexicographically smallest topological order yêu cầu thay đổi gì trong Kahn's Algorithm?

A. Sort kết quả cuối cùng
B. Thay deque/queue bằng min-heap (priority queue)
C. Chạy DFS thay vì BFS
D. Thêm bước pre-sort adjacency list

**Đáp án: B**

**Giải thích:**
- **B đúng**: Kahn's chuẩn dùng queue (FIFO) — khi có nhiều source, xử lý theo thứ tự đến. Min-heap đảm bảo luôn pop đỉnh có index nhỏ nhất trong số các source hiện tại. Complexity tăng từ O(V+E) lên O((V+E)logV) do heap operations.
- A sai: Sort cuối cùng không đúng vì thứ tự xử lý ảnh hưởng đến việc đỉnh nào trở thành source tiếp theo.
- C sai: DFS không dễ enforce lexicographic order.
- D sai: Sort adjacency list không đảm bảo lexicographic topological order.

---

### Câu 15 (Nâng cao)
Cho DAG có n đỉnh và n-1 cạnh. Điều này cho biết gì về cấu trúc graph và số topological orderings?

A. Graph là complete DAG với n! orderings
B. Graph là một chain (path graph) với đúng 1 topological ordering
C. Graph là một tree (rooted DAG) và có nhiều orderings có thể
D. Không thể kết luận gì từ số cạnh

**Đáp án: C**

**Giải thích:**
- **C đúng**: DAG với n đỉnh và n-1 cạnh là một tree structure (DAG tree/forest). Tuy nhiên, tree không nhất thiết là chain — có thể là một root với nhiều nhánh. Ví dụ: root→[A, B, C] (1 root, 3 leaves, 3 edges). Topological ordering: root đứng đầu, nhưng A, B, C có thể theo thứ tự bất kỳ → 3! = 6 orderings.
- A sai: Complete DAG có n(n-1)/2 cạnh.
- B sai: Chain là một trường hợp đặc biệt của tree, nhưng không phải mọi tree đều là chain.
- D sai: Số cạnh = n-1 trong DAG có nghĩa rõ ràng về cấu trúc.

---

### Câu 16 (Nâng cao)
Hệ thống build phát hiện "circular dependency: A→B→C→A". Kahn's Algorithm sẽ xử lý thế nào và trả về gì?

A. Infinite loop
B. Trả về [] hoặc báo lỗi cycle vì result.length < n
C. Trả về thứ tự một phần, bỏ qua cycle
D. Crash với stack overflow

**Đáp án: B**

**Giải thích:**
- **B đúng**: A, B, C đều có in-degree > 0 (A←C, B←A, C←B). Chúng không bao giờ vào queue ban đầu. BFS loop kết thúc ngay khi queue rỗng (không xử lý A/B/C). result.length = 0 < n = 3. Thuật toán trả về [] (cycle detected). Không có infinite loop, không crash — đây là why Kahn's cycle detection được ưa chuộng.
- A sai: BFS với visited tracking không infinite loop.
- C sai: Kahn's không bỏ qua một phần — kết quả hoặc đầy đủ hoặc báo lỗi.
- D sai: Kahn's iterative không dùng call stack.

---

### Câu 17 (Nâng cao)
Cho DAG đại diện cho spreadsheet (mỗi cell là node, cạnh A→B nghĩa là B dùng giá trị A). Topological sort được dùng để tính toán đúng thứ tự. Nếu user thêm công thức tạo cycle (B3 = A1 + B3), hệ thống nên làm gì?

A. Tính toán vô hạn
B. Chạy Kahn's và nếu phát hiện cycle (result < n) → báo lỗi "Circular reference"
C. Bỏ qua cell B3 trong tính toán
D. Dùng giá trị 0 cho cell trong cycle

**Đáp án: B**

**Giải thích:**
- **B đúng**: Đây chính xác là cách Excel và Google Sheets hoạt động. Sau mỗi lần thay đổi công thức, build lại dependency DAG, chạy topological sort (Kahn's). Nếu cycle được phát hiện → hiện "Circular reference error". Microsoft Excel báo lỗi "#REF!" hoặc "#VALUE!" trong trường hợp này. Cycle detection là critical feature của spreadsheet engines.
- A sai: Tính toán vô hạn không acceptable.
- C, D sai: Bỏ qua hay dùng 0 sẽ cho kết quả sai im lặng, tệ hơn báo lỗi.

---

### Câu 18 (Nâng cao)
Xét bài "Alien Dictionary" (LeetCode 269): Từ danh sách từ được sắp theo thứ tự alien alphabet, suy ra thứ tự alphabet. Bài này dùng topological sort như thế nào?

A. Sort các từ theo length
B. So sánh các từ liền kề để suy ra edge u→v (u trước v trong alphabet), rồi topological sort
C. DFS trên trie của tất cả các từ
D. Kahn's Algorithm trực tiếp trên danh sách từ

**Đáp án: B**

**Giải thích:**
- **B đúng**: Với mỗi cặp từ liền kề (word_i, word_{i+1}), so sánh character theo character đến khi tìm thấy character đầu tiên khác nhau. Ký tự đó cho biết thứ tự: word_i[j] → word_{i+1}[j] (word_i[j] đứng trước trong alphabet). Xây dựng graph từ các edge này, rồi topological sort → thứ tự alphabet. Edge case: nếu word_i là prefix của word_{i+1} và dài hơn → invalid ("abc" trước "ab" là sai).
- A sai: Sort by length không suy ra alphabet order.
- C sai: Trie traverse không trực tiếp cho alphabet order.
- D sai: Cần bước xây dựng graph từ comparisons trước, rồi mới Kahn's.

---

### Câu 19 (Nâng cao)
Parallel job scheduling: Có n jobs, một số jobs phụ thuộc lẫn nhau (job A phải xong trước job B). Muốn tìm **minimum time** để hoàn thành tất cả (với unlimited parallelism). Kahn's algorithm cần được modify như thế nào?

A. Chạy Kahn's bình thường, kết quả là số levels
B. Chạy Kahn's nhưng track số level (số "round" của BFS) — minimum time = số levels
C. Chạy Dijkstra thay vì Kahn's
D. Không cần modify, Kahn's đã cho minimum time

**Đáp án: B**

**Giải thích:**
- **B đúng**: Với unlimited parallelism, tất cả jobs ở cùng "level" (không có dependency giữa chúng) có thể chạy song song. Minimum time = số levels trong BFS topological sort. Mỗi round của BFS = 1 time unit. Tương tự bài "Parallel Courses" (LeetCode 1136). Kahn's modified: track depth của từng node (depth[v] = max(depth[u]+1) cho mọi u→v). Answer = max depth.
- A sai: Số levels không tự động được trả về bởi Kahn's chuẩn, cần tracking.
- C sai: Dijkstra cho shortest path trong weighted graph, không liên quan trực tiếp.
- D sai: Cần thêm level tracking.

---

### Câu 20 (Nâng cao)
Cho DAG và hai thuật toán: Kahn's (BFS) và DFS-based. Trong trường hợp nào hai thuật toán cho ra cùng một topological ordering?

A. Luôn luôn giống nhau
B. Khi DAG là một chain tuyến tính (0→1→2→...→n-1)
C. Khi tất cả edges đi từ trái sang phải theo chỉ số
D. Không bao giờ giống nhau

**Đáp án: B**

**Giải thích:**
- **B đúng**: Khi DAG là chain, ở mỗi bước chỉ có đúng 1 source (đỉnh có in-degree=0). Kahn's không có lựa chọn nào khác ngoài lấy đỉnh đó. DFS từ đỉnh đầu cũng đi thẳng theo chain. Cả hai cho [0, 1, 2, ..., n-1]. Đây là trường hợp topological order duy nhất.
- A sai: Khi có nhiều sources, Kahn's (FIFO queue) và DFS (stack) có thể cho thứ tự khác nhau.
- C sai: Điều này không đủ đảm bảo giống nhau — vẫn có thể có branching.
- D sai: Chain là counterexample.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Độ khó |
|-----|--------|--------|
| 1 | C | Cơ bản |
| 2 | B | Cơ bản |
| 3 | B | Cơ bản |
| 4 | C | Cơ bản |
| 5 | B | Cơ bản |
| 6 | B | Cơ bản |
| 7 | B | Trung bình |
| 8 | B | Trung bình |
| 9 | C | Trung bình |
| 10 | B | Trung bình |
| 11 | B | Trung bình |
| 12 | B | Trung bình |
| 13 | A | Trung bình |
| 14 | B | Trung bình |
| 15 | C | Nâng cao |
| 16 | B | Nâng cao |
| 17 | B | Nâng cao |
| 18 | B | Nâng cao |
| 19 | B | Nâng cao |
| 20 | B | Nâng cao |
