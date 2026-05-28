# DFS & BFS — Depth-First Search và Breadth-First Search

## 1. Giải thích cho người mới hoàn toàn

### DFS — Tìm kiếm theo chiều sâu

Hãy tưởng tượng bạn đang đi trong một mê cung. Chiến lược của DFS là: **đi thẳng vào càng sâu càng tốt, đến khi bí thì quay lại và thử đường khác**.

Ví dụ thực tế: Bạn đang tìm một quyển sách trong thư viện. DFS giống như việc bạn vào kệ A, đi hết toàn bộ kệ A từ đầu đến cuối, rồi mới sang kệ B.

Hoặc: Bạn đang duyệt cây gia phả. DFS đi theo một nhánh từ ông cố xuống hết đến chắt chít, rồi mới quay lại xét nhánh anh em của ông cố.

### BFS — Tìm kiếm theo chiều rộng

BFS ngược lại: **lan rộng ra từng tầng, tầng gần trước, tầng xa sau**. Giống như vứt một hòn đá xuống ao, các vòng sóng lan ra từng tầng một.

Ví dụ thực tế: Bạn đang tìm nhà bạn bè gần nhất. BFS giống như hỏi tất cả bạn bè cấp 1 trước, nếu không ai biết mới hỏi bạn bè cấp 2 (bạn của bạn), rồi cấp 3...

Hoặc: Facebook "People You May Know" — trước tiên xem bạn chung với bạn bè trực tiếp, rồi mới đến bạn bè của bạn bè.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### DFS — Cơ chế bên trong

DFS duy trì một **call stack** (đệ quy) hoặc **explicit stack** (iterative). Khi thăm node `u`, nó đánh dấu `u` là đã thăm rồi đệ quy vào từng neighbor chưa thăm.

**Discovery time / Finish time** (Thời điểm phát hiện / kết thúc): Trong DFS trên directed graph, ta có thể gán `d[u]` (discovery) và `f[u]` (finish) cho mỗi node. Hai số này tạo ra **parenthesis structure** — interval của node con luôn nằm trong interval của node cha.

**Phân loại cạnh trong directed graph:**
- **Tree edge**: cạnh trong DFS tree (đến node chưa thăm)
- **Back edge**: `u → v` với `v` là tổ tiên của `u` trong DFS tree → **chỉ thị cycle**
- **Forward edge**: `u → v` với `v` là hậu duệ đã thăm xong
- **Cross edge**: `u → v` với `v` không có quan hệ tổ tiên/hậu duệ

**Trade-off quan trọng:**
- Recursive DFS: code ngắn gọn, nhưng với graph có V=10^5 node, Python default recursion limit (1000) sẽ gây `RecursionError`. Có thể `sys.setrecursionlimit()` nhưng vẫn có nguy cơ stack overflow thực sự.
- Iterative DFS: an toàn hơn, nhưng thứ tự thăm neighbor có thể **đảo ngược** so với recursive nếu push vào stack theo chiều thuận.

**SCC (Strongly Connected Components):**
- Kosaraju: 2 lần DFS — DFS lần 1 để lấy finish order, transpose graph, DFS lần 2 theo reverse finish order.
- Tarjan: 1 lần DFS dùng `low-link values` và stack — hiệu quả hơn trong practice.

### BFS — Cơ chế bên trong

BFS dùng **FIFO queue**. Tất cả node ở distance `d` được xử lý trước node ở distance `d+1`. Đây là lý do BFS đảm bảo **shortest path** trong unweighted graph.

**Tại sao DFS không đảm bảo shortest path?** DFS đi theo một đường đến cùng, có thể tìm thấy đường dài trước đường ngắn.

**Multi-source BFS**: Thêm nhiều source vào queue ban đầu → tính distance từ tập source đến mọi node. Dùng cho bài toán "tìm ô đất gần biển nhất" (matrix với nhiều ô biển).

**0-1 BFS**: Với graph có edge weight 0 hoặc 1, dùng `deque` thay vì `queue` thông thường. Edge weight 0 → `appendleft`, edge weight 1 → `append`. Phức tạp O(V+E) thay vì O((V+E)logV) của Dijkstra.

**Bidirectional BFS**: Chạy BFS từ cả source lẫn target đồng thời. Khi hai frontier gặp nhau → tìm thấy đường. Complexity giảm từ O(b^d) xuống O(b^(d/2)) với b = branching factor, d = depth.

---

## 3. Định nghĩa chính xác

**DFS (Depth-First Search)**: Thuật toán duyệt đồ thị/cây bắt đầu từ một đỉnh nguồn, đi sâu nhất có thể theo mỗi nhánh trước khi backtrack. Sử dụng stack (tường minh hoặc call stack đệ quy).

**BFS (Breadth-First Search)**: Thuật toán duyệt đồ thị/cây bắt đầu từ một đỉnh nguồn, thăm tất cả đỉnh ở khoảng cách k trước khi thăm đỉnh ở khoảng cách k+1. Sử dụng queue FIFO.

**Visited set**: Tập hợp các đỉnh đã thăm, ngăn duyệt vòng lặp vô hạn trong graph có chu trình.

**DFS Tree/Forest**: Cây (rừng) tạo thành từ các tree edge trong quá trình DFS.

**Level-order traversal**: Duyệt cây theo từng tầng — đây chính là BFS áp dụng trên cây.

---

## 4. Bảng Độ phức tạp đầy đủ

### DFS

| Thao tác | Best Case | Average Case | Worst Case | Điều kiện |
|----------|-----------|--------------|------------|-----------|
| DFS trên Tree (duyệt toàn bộ) | O(V) | O(V) | O(V) | V = số đỉnh |
| DFS trên Graph (duyệt toàn bộ) | O(V+E) | O(V+E) | O(V+E) | V = đỉnh, E = cạnh |
| DFS tìm kiếm (target ở đầu) | O(1) | O(V/2) | O(V+E) | Target không tồn tại → worst |
| Cycle Detection (directed) | O(1) | O(V+E) | O(V+E) | Cycle ở đầu → best |
| Topological Sort via DFS | O(V+E) | O(V+E) | O(V+E) | Luôn cần duyệt toàn bộ |

| Thao tác | Space (Auxiliary) | Space (Total) | Ghi chú |
|----------|-------------------|---------------|---------|
| Recursive DFS trên Tree | O(h) | O(V) | h = height; balanced tree h=O(log V), skewed h=O(V) |
| Recursive DFS trên Graph | O(V) | O(V+E) | O(V) cho call stack + visited set |
| Iterative DFS trên Tree | O(h) worst | O(V) | Stack chứa tối đa h node |
| Iterative DFS trên Graph | O(V) | O(V+E) | Stack có thể chứa toàn bộ V đỉnh |

### BFS

| Thao tác | Best Case | Average Case | Worst Case | Điều kiện |
|----------|-----------|--------------|------------|-----------|
| BFS trên Tree (duyệt toàn bộ) | O(V) | O(V) | O(V) | V = số đỉnh |
| BFS trên Graph (duyệt toàn bộ) | O(V+E) | O(V+E) | O(V+E) | V = đỉnh, E = cạnh |
| BFS shortest path (target ở gần) | O(b) | O(b^(d/2)) | O(V+E) | b = branching factor, d = depth |
| Multi-source BFS | O(V+E) | O(V+E) | O(V+E) | Thêm tất cả sources vào queue |
| 0-1 BFS (edge weight 0 or 1) | O(V+E) | O(V+E) | O(V+E) | Dùng deque thay queue |
| Bidirectional BFS | O(b^(d/2)) | O(b^(d/2)) | O(b^(d/2)) | Gặp nhau ở giữa |

| Thao tác | Space (Auxiliary) | Space (Total) | Ghi chú |
|----------|-------------------|---------------|---------|
| BFS trên Tree | O(w) | O(V) | w = max width; complete binary tree w=V/2 |
| BFS trên Graph | O(V) | O(V+E) | Queue chứa tối đa O(V) đỉnh |
| BFS với level tracking | O(V) | O(V+E) | Giữ thêm distance array |

---

## 5. Code mẫu Python

### DFS — Recursive trên Graph

```python
from collections import defaultdict

def dfs_recursive(graph, start):
    """
    DFS đệ quy trên undirected/directed graph.
    graph: dict of list (adjacency list)
    """
    visited = set()
    order = []

    def dfs(node):
        visited.add(node)
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)

    dfs(start)
    return order


# Ví dụ sử dụng
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B', 'F'],
    'F': ['C', 'E']
}
print(dfs_recursive(graph, 'A'))  # ['A', 'B', 'D', 'E', 'F', 'C']
```

### DFS — Iterative (Stack-based) trên Graph

```python
def dfs_iterative(graph, start):
    """
    DFS dùng explicit stack — tránh RecursionError cho graph lớn.
    LƯU Ý: thứ tự thăm có thể khác recursive do cách push vào stack.
    """
    visited = set()
    order = []
    stack = [start]

    while stack:
        node = stack.pop()  # LIFO — đây là điểm khác biệt với BFS
        if node in visited:
            continue
        visited.add(node)
        order.append(node)
        # Push neighbors theo thứ tự ngược để đảm bảo thứ tự giống recursive
        for neighbor in reversed(graph[node]):
            if neighbor not in visited:
                stack.append(neighbor)

    return order
```

### DFS — Phát hiện Cycle trong Directed Graph

```python
def has_cycle_directed(graph, n):
    """
    Phát hiện cycle trong directed graph dùng 3 trạng thái:
    0 = unvisited, 1 = visiting (trong call stack hiện tại), 2 = visited
    """
    color = [0] * n  # 0: white, 1: gray, 2: black

    def dfs(node):
        color[node] = 1  # Đánh dấu đang thăm (gray)
        for neighbor in graph[node]:
            if color[neighbor] == 1:
                return True   # Back edge → có cycle
            if color[neighbor] == 0:
                if dfs(neighbor):
                    return True
        color[node] = 2  # Đánh dấu đã xong (black)
        return False

    for node in range(n):
        if color[node] == 0:
            if dfs(node):
                return True
    return False
```

### BFS trên Graph với Shortest Path

```python
from collections import deque

def bfs(graph, start):
    """
    BFS chuẩn — trả về distance từ start đến mọi node.
    """
    visited = {start}
    queue = deque([start])
    distance = {start: 0}
    order = []

    while queue:
        node = queue.popleft()  # FIFO — điểm khác biệt với DFS
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                distance[neighbor] = distance[node] + 1
                queue.append(neighbor)

    return order, distance


# Ví dụ: tìm shortest path
def bfs_shortest_path(graph, start, end):
    """Trả về đường đi ngắn nhất từ start đến end."""
    if start == end:
        return [start]

    visited = {start}
    queue = deque([[start]])  # Queue of paths

    while queue:
        path = queue.popleft()
        node = path[-1]
        for neighbor in graph[node]:
            if neighbor == end:
                return path + [neighbor]
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(path + [neighbor])

    return []  # Không có đường đi


graph = {'A': ['B', 'C'], 'B': ['D'], 'C': ['D', 'E'], 'D': ['F'], 'E': ['F'], 'F': []}
print(bfs_shortest_path(graph, 'A', 'F'))  # ['A', 'C', 'D', 'F'] hoặc tương đương
```

### BFS — Level-order Traversal trên Binary Tree

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def level_order(root):
    """
    BFS level-by-level trên binary tree.
    Trả về list of lists, mỗi inner list là một tầng.
    """
    if not root:
        return []

    result = []
    queue = deque([root])

    while queue:
        level_size = len(queue)  # Số node ở tầng hiện tại
        current_level = []

        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        result.append(current_level)

    return result
```

### Multi-source BFS

```python
def multi_source_bfs(grid, sources):
    """
    BFS từ nhiều điểm nguồn — tính distance gần nhất từ tập sources đến mọi ô.
    Ứng dụng: 01 Matrix (LeetCode 542), Rotting Oranges (LeetCode 994)
    """
    rows, cols = len(grid), len(grid[0])
    dist = [[float('inf')] * cols for _ in range(rows)]
    queue = deque()

    # Thêm TẤT CẢ nguồn vào queue ban đầu
    for r, c in sources:
        dist[r][c] = 0
        queue.append((r, c))

    directions = [(0,1), (0,-1), (1,0), (-1,0)]

    while queue:
        r, c = queue.popleft()
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and dist[nr][nc] == float('inf'):
                dist[nr][nc] = dist[r][c] + 1
                queue.append((nr, nc))

    return dist
```

### DFS — Preorder / Inorder / Postorder trên Binary Tree

```python
def tree_traversals(root):
    """Ba cách duyệt DFS trên binary tree."""

    def preorder(node, result):
        """Root → Left → Right. Dùng: serialize tree, prefix expression."""
        if not node:
            return
        result.append(node.val)   # Thăm ROOT trước
        preorder(node.left, result)
        preorder(node.right, result)

    def inorder(node, result):
        """Left → Root → Right. Với BST → cho ra kết quả sorted."""
        if not node:
            return
        inorder(node.left, result)
        result.append(node.val)   # Thăm ROOT giữa
        inorder(node.right, result)

    def postorder(node, result):
        """Left → Right → Root. Dùng: delete tree, postfix expression."""
        if not node:
            return
        postorder(node.left, result)
        postorder(node.right, result)
        result.append(node.val)   # Thăm ROOT sau

    pre, ino, post = [], [], []
    preorder(root, pre)
    inorder(root, ino)
    postorder(root, post)
    return pre, ino, post
```

---

## 6. Khi nào dùng / Không dùng

### Dùng DFS khi:
- Cần detect cycle trong graph
- Cần topological sort
- Giải maze (tìm BẤT KỲ đường nào, không cần ngắn nhất)
- Tìm tất cả đường đi (backtracking)
- Tính SCC (Strongly Connected Components)
- Duyệt cây với preorder/inorder/postorder
- Memory hạn chế (DFS dùng O(h) space vs BFS dùng O(w) space — khi cây/graph rộng thì DFS tiết kiệm hơn)

### Không dùng DFS khi:
- Cần tìm **shortest path** trong unweighted graph → dùng BFS
- Graph rất sâu và đệ quy → nguy cơ stack overflow → dùng iterative DFS hoặc BFS
- Cần tìm solution gần nhất (nearest) → BFS phù hợp hơn

### Dùng BFS khi:
- Cần tìm **shortest path** trong unweighted graph
- Level-order traversal
- Tìm node gần nhất thỏa điều kiện
- Multi-source shortest distance
- Kiểm tra bipartite graph (2-coloring)
- Tìm connected components trong undirected graph

### Không dùng BFS khi:
- Graph/cây rất rộng (wide branching factor) → queue sẽ rất lớn, tốn memory
- Cần duyệt theo thứ tự prefix/infix/postfix → dùng DFS
- Cần tìm tất cả paths → DFS + backtracking

---

## 7. So sánh DFS và BFS

| Tiêu chí | DFS | BFS |
|----------|-----|-----|
| **Data structure** | Stack (đệ quy hoặc tường minh) | Queue (FIFO) |
| **Thứ tự duyệt** | Đi sâu theo nhánh trước | Duyệt từng tầng |
| **Shortest path** | Không đảm bảo | Đảm bảo (unweighted graph) |
| **Space (tree)** | O(h) — h = height | O(w) — w = max width |
| **Space (graph)** | O(V) | O(V) |
| **Cycle detection** | Dễ (back edge) | Có thể nhưng kém tự nhiên hơn |
| **Topological sort** | Tự nhiên (reverse postorder) | Kahn's Algorithm |
| **Ứng dụng maze** | Tốt (tìm bất kỳ path) | Tốt (tìm path ngắn nhất) |
| **Implementation** | Đệ quy ngắn gọn | Iterative tự nhiên |
| **Stack overflow risk** | Có (recursive, graph sâu) | Không |
| **Complete?** | Có (finite graph) | Có |
| **Optimal?** | Không | Có (unweighted) |

---

## 8. Common Pitfalls

### Pitfall 1: Quên đánh dấu visited TRƯỚC khi thêm vào queue (BFS)
```python
# SAI — có thể thêm cùng node vào queue nhiều lần
while queue:
    node = queue.popleft()
    visited.add(node)  # SAI: đánh dấu quá muộn
    for neighbor in graph[node]:
        if neighbor not in visited:
            queue.append(neighbor)

# ĐÚNG — đánh dấu ngay khi thêm vào queue
visited = {start}
queue = deque([start])
while queue:
    node = queue.popleft()
    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)  # ĐÚNG: đánh dấu ngay
            queue.append(neighbor)
```

### Pitfall 2: Iterative DFS có thứ tự thăm khác Recursive DFS
```python
# Recursive DFS thăm neighbors theo thứ tự: A, B, C
# Iterative DFS nếu push theo thứ tự thẳng: stack = [A, B, C]
# → pop C trước (LIFO) → thứ tự thực tế: C, B, A
# → Phải push theo thứ tự NGƯỢC để giống recursive
for neighbor in reversed(neighbors):
    stack.append(neighbor)
```

### Pitfall 3: DFS cycle detection sai cho Undirected Graph
```python
# Undirected graph: A-B là 2 cạnh A→B và B→A
# Nếu chỉ dùng visited set, B→A sẽ bị coi là back edge nhưng thực ra là cạnh cha
# Phải truyền thêm parent
def dfs_undirected(node, parent, visited, graph):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor == parent:
            continue  # Bỏ qua cạnh ngược về parent
        if neighbor in visited:
            return True  # Thực sự là cycle
        if dfs_undirected(neighbor, node, visited, graph):
            return True
    return False
```

### Pitfall 4: Không xử lý Disconnected Graph
```python
# Nếu graph không liên thông, DFS/BFS từ một node không thăm hết tất cả
# Phải lặp qua mọi node chưa thăm
def dfs_all(graph, n):
    visited = set()
    for node in range(n):
        if node not in visited:
            dfs(node, visited, graph)  # Mỗi lần gọi = một connected component
```

### Pitfall 5: BFS không tối ưu cho Weighted Graph
```python
# BFS chỉ đảm bảo shortest path khi tất cả cạnh có weight bằng nhau
# Với weighted graph → dùng Dijkstra
# Với 0-1 weight → 0-1 BFS (deque)
# Với negative weight → Bellman-Ford
```

### Pitfall 6: Stack Overflow với Recursive DFS trên Graph lớn
```python
import sys
sys.setrecursionlimit(200000)  # Giải pháp tạm thời
# Giải pháp tốt hơn: dùng iterative DFS
```

---

## 9. Câu hỏi phỏng vấn hay gặp

1. **BFS vs DFS: khi nào dùng cái nào?**
   Trả lời: BFS khi cần shortest path (unweighted), level-order, node gần nhất. DFS khi cần cycle detection, topological sort, tất cả paths, tiết kiệm memory cho cây/graph rộng.

2. **Tại sao BFS đảm bảo shortest path còn DFS thì không?**
   Trả lời: BFS xử lý node theo thứ tự khoảng cách tăng dần. Node ở distance d được xử lý hết trước distance d+1. DFS có thể đi qua đường dài trước.

3. **DFS có thể implement bằng stack không? Có giống recursive không?**
   Trả lời: Có thể, nhưng thứ tự có thể khác nếu không cẩn thận. Để giống, push theo thứ tự ngược.

4. **Làm thế nào detect cycle trong directed graph vs undirected graph?**
   Trả lời: Directed — dùng 3 màu (unvisited/visiting/visited), back edge là cycle. Undirected — dùng visited + parent, nếu neighbor đã visited và không phải parent → cycle.

5. **Time complexity của DFS/BFS trên graph là gì? Tại sao là O(V+E)?**
   Trả lời: Mỗi đỉnh thăm đúng 1 lần O(V), mỗi cạnh xét tối đa 2 lần (undirected) hoặc 1 lần (directed) O(E).

6. **Multi-source BFS là gì? Cho ví dụ bài toán.**
   Trả lời: BFS từ nhiều điểm nguồn cùng lúc. Ví dụ: 01 Matrix, Rotting Oranges, Walls and Gates.

7. **Bidirectional BFS là gì và tại sao nhanh hơn?**
   Trả lời: BFS từ cả source và target. Phức tạp O(b^(d/2)) thay vì O(b^d) vì mỗi bên chỉ tìm đến giữa đường.

8. **DFS tree traversal: preorder, inorder, postorder khác nhau như thế nào và dùng khi nào?**
   Trả lời: Preorder (Root-L-R): serialize, copy tree. Inorder (L-Root-R): BST sorted output. Postorder (L-R-Root): delete tree, evaluate expression tree.

9. **0-1 BFS là gì? Tại sao không dùng Dijkstra?**
   Trả lời: Graph với edge weight 0 hoặc 1, dùng deque. Edge 0 → appendleft (không tăng distance), edge 1 → append. O(V+E) thay vì O((V+E)logV).

10. **Kahn's Algorithm vs DFS-based Topological Sort?**
    Trả lời: Kahn's (BFS-based) tự nhiên detect cycle, dễ implement. DFS-based dùng reverse postorder, cần 3 trạng thái để detect cycle.

---

## 10. Dạng Bài Thường Gặp Trong Thi Cử / Phỏng Vấn

### Dạng 1: Connected Components — đếm số thành phần liên thông

**Nhận dạng đề:** "Đếm số island", "số nhóm bạn bè", "số vùng liên thông", "number of connected components". Graph hoặc matrix, cần đếm số nhóm không kết nối với nhau.

**Approach chuẩn:** DFS/BFS từ mỗi node chưa thăm, mỗi lần bắt đầu DFS = thêm 1 component. Đánh dấu visited để không thăm lại. O(V+E) hoặc O(m×n) cho matrix.

**LeetCode tiêu biểu:** "Number of Islands" (LC 200), "Number of Provinces" (LC 547), "Max Area of Island" (LC 695)

---

### Dạng 2: Shortest Path trong Unweighted Graph / Matrix

**Nhận dạng đề:** "Đường đi ngắn nhất từ A đến B", "minimum steps", "minimum moves". Graph không có weight hoặc tất cả weight bằng 1. BFS đảm bảo shortest path.

**Approach chuẩn:** BFS từ source. Dùng `distance` dict/array. Khi pop node và tìm thấy target → return distance. Không cần thăm hết graph nếu chỉ cần shortest path đến 1 target.

**LeetCode tiêu biểu:** "Shortest Path in Binary Matrix" (LC 1091), "Word Ladder" (LC 127), "Open the Lock" (LC 752)

---

### Dạng 3: Cycle Detection — phát hiện chu trình

**Nhận dạng đề:** "Có chu trình không?", "detect cycle", "can you finish all courses?" (ám chỉ DAG check). Directed graph cần 3-color DFS; undirected graph cần visited + parent.

**Approach chuẩn (directed):** 3 màu: WHITE=0 (chưa thăm), GRAY=1 (đang thăm), BLACK=2 (xong). GRAY→GRAY = back edge = cycle.

**Approach chuẩn (undirected):** DFS với `parent` parameter. Nếu neighbor đã visited và không phải parent → cycle.

**LeetCode tiêu biểu:** "Course Schedule" (LC 207), "Detect Cycle in Directed Graph", "Graph Valid Tree" (LC 261)

---

### Dạng 4: Topological Sort — sắp xếp thứ tự phụ thuộc

**Nhận dạng đề:** "Thứ tự hoàn thành các task có dependency", "build order", "compile order", "course prerequisites". DAG (Directed Acyclic Graph) với quan hệ phụ thuộc.

**Approach chuẩn (Kahn's Algorithm — BFS):**
1. Tính in-degree cho mỗi node
2. Queue bắt đầu với tất cả node có in-degree = 0
3. Pop node → thêm vào result → giảm in-degree neighbors → thêm neighbor vào queue nếu in-degree = 0
4. Nếu result.length < V → có cycle

**LeetCode tiêu biểu:** "Course Schedule II" (LC 210), "Alien Dictionary" (LC 269), "Sequence Reconstruction" (LC 444)

---

### Dạng 5: Multi-source BFS — khoảng cách từ tập nguồn

**Nhận dạng đề:** "Khoảng cách gần nhất đến bất kỳ ô nào trong tập S", "thời gian để ô thối lan ra", "tất cả 0 lan ra thành 1". Nhiều điểm nguồn, tìm distance đến gần nhất.

**Approach chuẩn:** Thêm TẤT CẢ sources vào queue với distance = 0 ngay từ đầu. BFS bình thường. Vì BFS xử lý theo lớp, mỗi ô sẽ được cập nhật bởi source gần nhất trước.

**LeetCode tiêu biểu:** "01 Matrix" (LC 542), "Rotting Oranges" (LC 994), "Walls and Gates" (LC 286)

---

### Dạng 6: Flood Fill / Paint Region

**Nhận dạng đề:** "Thay đổi màu vùng liên thông", "flood fill", "paint with new color", "spread infection to connected cells". Cần thay đổi tất cả ô liên thông.

**Approach chuẩn:** DFS/BFS từ ô bắt đầu. Đánh dấu đã thăm (có thể thay đổi trực tiếp giá trị ô). Chú ý edge case: màu mới = màu cũ → không cần thay đổi.

**LeetCode tiêu biểu:** "Flood Fill" (LC 733), "Pacific Atlantic Water Flow" (LC 417), "Surrounded Regions" (LC 130)

---

### Dạng 7: Graph Coloring / Bipartite Check

**Nhận dạng đề:** "Kiểm tra đồ thị 2-coloring", "chia đỉnh thành 2 nhóm không có cạnh trong cùng nhóm", "is bipartite?", "can you divide into two teams?".

**Approach chuẩn:** BFS/DFS, tô màu xen kẽ (0/1). Nếu neighbor cùng màu với node hiện tại → không phải bipartite. Xử lý disconnected graph bằng cách loop qua mọi node chưa thăm.

**LeetCode tiêu biểu:** "Is Graph Bipartite?" (LC 785), "Possible Bipartition" (LC 886)

---

### Dạng 8: DFS với Backtracking — tìm tất cả đường đi / tổ hợp

**Nhận dạng đề:** "Tìm tất cả đường đi từ A đến B", "liệt kê tất cả tổ hợp thỏa mãn", "all possible paths". Cần enumerate tất cả solutions, không chỉ một.

**Approach chuẩn:**
1. DFS với path hiện tại
2. Khi đến target → thêm path vào results
3. Sau khi return từ đệ quy → **bỏ phần tử cuối** (backtrack)
4. Dùng visited để tránh vòng lặp

**LeetCode tiêu biểu:** "All Paths From Source to Target" (LC 797), "Path Sum II" (LC 113), "Combination Sum" (LC 39)

---

### Dạng 9: Strongly Connected Components (SCC)

**Nhận dạng đề:** "Tìm các nhóm đỉnh có thể đến nhau theo cả 2 chiều", "strongly connected", "condensation graph", bài toán trên directed graph cần nhóm các đỉnh liên kết chặt.

**Approach chuẩn (Kosaraju):**
1. DFS lần 1 trên graph gốc → lưu finish order vào stack
2. Transpose graph (đảo chiều tất cả cạnh)
3. DFS lần 2 theo reverse finish order trên transposed graph
4. Mỗi DFS tree trong bước 2 = 1 SCC

**LeetCode tiêu biểu:** "Critical Connections in a Network" (LC 1192), "Number of Strongly Connected Components" (bài contest)

---

### Dạng 10: 0-1 BFS và Dijkstra-lite cho Graph với Weight 0/1

**Nhận dạng đề:** "Minimum cost path" với cost chỉ là 0 hoặc 1, "minimum number of obstacles to remove", "flip minimum cells". Graph có edge weight 0 hoặc 1.

**Approach chuẩn (0-1 BFS):**
- Dùng `deque` thay vì queue thông thường
- Edge weight 0 → `appendleft` (priority cao hơn, không tăng distance)
- Edge weight 1 → `append` (vào cuối queue)
- O(V+E) thay vì O((V+E)logV) của Dijkstra

**LeetCode tiêu biểu:** "Minimum Cost to Make at Least One Valid Path in a Grid" (LC 1368), "Minimum Obstacle Removal to Reach Corner" (LC 2290)
