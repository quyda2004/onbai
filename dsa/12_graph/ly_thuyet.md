# Graph — Lý Thuyết Toàn Diện

---

## 1. Giải Thích Cho Người Mới Hoàn Toàn

Hãy tưởng tượng bạn nhìn vào bản đồ Google Maps. Các **thành phố** là các điểm (node/vertex), các **con đường** nối chúng là các cạnh (edge). Đây chính là Graph.

**Ví dụ cuộc sống thực:**
- **Mạng xã hội (Facebook):** Người dùng = node, kết bạn = edge (hai chiều — undirected)
- **Twitter/Instagram:** Người dùng = node, follow = edge (một chiều — directed)
- **Google Maps:** Giao lộ = node, đường = edge có trọng số (distance/time)
- **Internet:** Router = node, kết nối = edge
- **Lịch trình công việc:** Công việc = node, phụ thuộc (A phải xong trước B) = directed edge

Graph **tổng quát hơn** cây (tree). Cây là một trường hợp đặc biệt của graph: connected, acyclic, directed graph. Graph có thể có chu trình, nhiều đường đi giữa 2 node, thậm chí không kết nối nhau.

---

## 2. Giải Thích Nâng Cao Cho Người Chuyên Ngành

### Phân Loại Graph

**Theo hướng:**
- **Undirected Graph:** Edge (u, v) = (v, u). Ma trận kề đối xứng. Ví dụ: mạng bạn bè.
- **Directed Graph (Digraph):** Edge (u, v) ≠ (v, u). Ví dụ: web links, Twitter follow.

**Theo trọng số:**
- **Unweighted:** Tất cả edges có weight = 1 (hoặc không quan tâm weight)
- **Weighted:** Mỗi edge có giá trị số thực (distance, cost, capacity)

**Theo chu trình:**
- **Acyclic:** Không có chu trình. DAG (Directed Acyclic Graph) rất quan trọng.
- **Cyclic:** Có ít nhất một chu trình

**Theo kết nối:**
- **Connected (undirected):** Từ bất kỳ node nào cũng đến được bất kỳ node nào khác
- **Strongly Connected (directed):** Từ bất kỳ u, v nào: u → v và v → u đều có đường
- **Weakly Connected (directed):** Connected nếu bỏ qua hướng

### Sparse vs Dense Graph

- **Sparse graph:** E << V². Ví dụ: road networks (mỗi giao lộ có ~4 con đường)
- **Dense graph:** E ≈ V². Ví dụ: social network cliques, complete graphs
- **Quy tắc thumb:** E > V log V → dense. E < V log V → sparse.

### Memory Model

**Adjacency Matrix:**
```
graph[u][v] = weight (hoặc 1/0 cho unweighted)
```
- Space: O(V²) — lãng phí cho sparse graph
- Access edge (u,v): O(1) — random access array
- Iterate neighbors of u: O(V) — phải scan cả row
- Add edge: O(1)
- Add vertex: O(V²) — phải resize matrix

**Adjacency List:**
```
graph[u] = [(v1, w1), (v2, w2), ...]
```
- Space: O(V + E) — hiệu quả cho sparse graph
- Access edge (u,v): O(degree(u)) — phải scan list
- Iterate neighbors of u: O(degree(u)) — chỉ duyệt neighbors thực sự
- Add edge: O(1)
- Add vertex: O(1)

### Edge List Representation

```
edges = [(u1, v1, w1), (u2, v2, w2), ...]
```
- Space: O(E)
- Tốt cho Kruskal MST (cần sort edges)
- Kém cho BFS/DFS (cần iterate neighbors nhanh)

---

## 3. Định Nghĩa Chính Xác

**Graph** G = (V, E) trong đó:
- V là tập hữu hạn các **vertices** (đỉnh/nodes)
- E ⊆ V × V là tập các **edges** (cạnh)

**Degree:**
- **Undirected:** `deg(v)` = số edges incident với v. Σ deg(v) = 2|E|.
- **Directed:** `in-deg(v)` = số edges đến v, `out-deg(v)` = số edges đi từ v.

**Path:** Dãy các vertices v₁, v₂, ..., vₖ sao cho (vᵢ, vᵢ₊₁) ∈ E.

**Cycle:** Path trong đó v₁ = vₖ (điểm đầu = điểm cuối).

**Connected Component:** Maximal subgraph mà mọi cặp vertices đều có path.

---

## 4. Bảng Độ Phức Tạp Đầy Đủ

### 4.1 Graph Traversal

| Thuật toán | Time | Space | Điều kiện / Notes |
|---|---|---|---|
| **BFS** | O(V + E) | O(V) | Queue + visited set. V: mỗi node enqueue 1 lần. E: mỗi edge check 1-2 lần |
| **DFS (iterative)** | O(V + E) | O(V) | Stack + visited set |
| **DFS (recursive)** | O(V + E) | O(V) call stack | Risk: stack overflow với deep graph |
| **Topological Sort (DFS)** | O(V + E) | O(V) | Chỉ valid với DAG |
| **Topological Sort (BFS/Kahn)** | O(V + E) | O(V) | In-degree based, detect cycle |

### 4.2 Shortest Path

| Thuật toán | Time | Space | Điều kiện |
|---|---|---|---|
| **BFS** (unweighted) | O(V + E) | O(V) | Chỉ unweighted. Tìm số edges tối thiểu. |
| **Dijkstra** (binary heap) | O((V + E) log V) | O(V + E) | Non-negative weights only. Fail với negative edges. |
| **Dijkstra** (Fibonacci heap) | O(V log V + E) | O(V + E) | Lý thuyết tốt hơn, practice thường dùng binary heap |
| **Bellman-Ford** | O(V × E) | O(V) | Hỗ trợ negative weights. Detect negative cycle. |
| **Floyd-Warshall** (all-pairs) | O(V³) | O(V²) | All-pairs shortest path. Hỗ trợ negative (không negative cycle). |
| **SPFA** (Shortest Path Faster) | O(VE) worst, O(E) avg | O(V) | Bellman-Ford với queue optimization. Không guaranteed tốt hơn. |

### 4.3 Minimum Spanning Tree

| Thuật toán | Time | Space | Best Case | Điều kiện |
|---|---|---|---|---|
| **Kruskal** | O(E log E) | O(V + E) | O(E α(V)) với Union-Find | Sort edges + Union-Find. Tốt cho sparse graph. |
| **Prim** (binary heap) | O((V + E) log V) | O(V + E) | O(E log V) | Priority queue. Tốt cho dense graph. |
| **Prim** (Fibonacci heap) | O(V log V + E) | O(V + E) | — | Lý thuyết optimal |
| **Borůvka** | O(E log V) | O(V + E) | — | Parallelizable |

### 4.4 Connected Components

| Thuật toán | Time | Space | Notes |
|---|---|---|---|
| **BFS/DFS components** (undirected) | O(V + E) | O(V) | Count disconnected components |
| **Kosaraju's SCC** (directed) | O(V + E) | O(V) | 2 DFS passes |
| **Tarjan's SCC** (directed) | O(V + E) | O(V) | 1 DFS pass, lower constant |
| **Union-Find** | O(E × α(V)) | O(V) | α(V) ≈ 5 thực tế → near O(1) |

### 4.5 Bipartite Check

| Thuật toán | Time | Space |
|---|---|---|
| **BFS 2-coloring** | O(V + E) | O(V) |
| **DFS 2-coloring** | O(V + E) | O(V) |

---

## 5. Code Mẫu Python

```python
from collections import defaultdict, deque
from typing import List, Dict, Optional, Tuple
import heapq

# ============================================================
# PHẦN 1: Graph Representations
# ============================================================

class Graph:
    """Đồ thị có hướng, có trọng số — dùng Adjacency List"""
    
    def __init__(self, directed: bool = True):
        self.adj: Dict[int, List[Tuple[int, int]]] = defaultdict(list)
        self.directed = directed
    
    def add_edge(self, u: int, v: int, weight: int = 1) -> None:
        """Thêm edge. Undirected thì thêm cả chiều ngược"""
        self.adj[u].append((v, weight))
        if not self.directed:
            self.adj[v].append((u, weight))
    
    def neighbors(self, u: int) -> List[Tuple[int, int]]:
        """Trả về danh sách (neighbor, weight) của u"""
        return self.adj[u]


# Adjacency Matrix (cho dense graph)
def create_adj_matrix(n: int) -> List[List[int]]:
    """n nodes, matrix n×n. 0 = không có edge"""
    return [[0] * n for _ in range(n)]


# ============================================================
# PHẦN 2: BFS và DFS
# ============================================================

def bfs(graph: Graph, start: int) -> List[int]:
    """
    BFS — O(V + E) time, O(V) space
    Trả về thứ tự duyệt và khoảng cách từ start
    """
    visited = set()
    queue = deque([start])
    visited.add(start)
    order = []
    
    while queue:
        node = queue.popleft()
        order.append(node)
        
        for neighbor, _ in graph.neighbors(node):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return order


def bfs_shortest_path(graph: Graph, start: int, end: int) -> Optional[List[int]]:
    """
    BFS tìm đường đi ngắn nhất (unweighted) — O(V + E)
    Trả về path hoặc None nếu không tồn tại
    """
    if start == end:
        return [start]
    
    visited = {start}
    queue = deque([(start, [start])])  # (node, path_so_far)
    
    while queue:
        node, path = queue.popleft()
        
        for neighbor, _ in graph.neighbors(node):
            if neighbor == end:
                return path + [neighbor]
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, path + [neighbor]))
    
    return None  # Không có đường đi


def dfs_iterative(graph: Graph, start: int) -> List[int]:
    """DFS iterative — O(V + E) time, O(V) space (stack)"""
    visited = set()
    stack = [start]
    order = []
    
    while stack:
        node = stack.pop()  # LIFO — khác với BFS dùng deque.popleft()
        if node not in visited:
            visited.add(node)
            order.append(node)
            # Thêm neighbors theo thứ tự ngược để duyệt đúng thứ tự
            for neighbor, _ in reversed(graph.neighbors(node)):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return order


def dfs_recursive(graph: Graph, start: int, visited: Optional[set] = None) -> List[int]:
    """DFS recursive — O(V + E) time, O(V) call stack"""
    if visited is None:
        visited = set()
    
    visited.add(start)
    result = [start]
    
    for neighbor, _ in graph.neighbors(start):
        if neighbor not in visited:
            result.extend(dfs_recursive(graph, neighbor, visited))
    
    return result


# ============================================================
# PHẦN 3: Detect Cycle
# ============================================================

def has_cycle_directed(graph: Graph, n: int) -> bool:
    """
    Detect cycle trong directed graph dùng DFS + coloring
    Color: 0=white(unvisited), 1=gray(in-progress), 2=black(done)
    """
    color = [0] * n
    
    def dfs(node: int) -> bool:
        color[node] = 1  # Gray: đang duyệt
        
        for neighbor, _ in graph.neighbors(node):
            if color[neighbor] == 1:  # Back edge → cycle!
                return True
            if color[neighbor] == 0 and dfs(neighbor):
                return True
        
        color[node] = 2  # Black: đã xong
        return False
    
    return any(dfs(i) for i in range(n) if color[i] == 0)


# ============================================================
# PHẦN 4: Dijkstra's Algorithm
# ============================================================

def dijkstra(graph: Graph, start: int, n: int) -> List[float]:
    """
    Dijkstra — O((V + E) log V) với binary heap
    Trả về mảng dist[v] = shortest distance từ start đến v
    
    QUAN TRỌNG: Chỉ đúng với non-negative weights!
    """
    INF = float('inf')
    dist = [INF] * n
    dist[start] = 0
    
    # (distance, node)
    heap = [(0, start)]
    
    while heap:
        d, u = heapq.heappop(heap)
        
        # Lazy deletion: bỏ qua nếu đã tìm được đường ngắn hơn
        if d > dist[u]:
            continue
        
        for v, weight in graph.neighbors(u):
            new_dist = dist[u] + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                heapq.heappush(heap, (new_dist, v))
    
    return dist


def dijkstra_with_path(graph: Graph, start: int, end: int, n: int):
    """Dijkstra + reconstruct path"""
    INF = float('inf')
    dist = [INF] * n
    prev = [-1] * n  # prev[v] = node trước v trong đường đi tốt nhất
    dist[start] = 0
    heap = [(0, start)]
    
    while heap:
        d, u = heapq.heappop(heap)
        if d > dist[u]:
            continue
        for v, weight in graph.neighbors(u):
            new_dist = dist[u] + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                prev[v] = u
                heapq.heappush(heap, (new_dist, v))
    
    # Reconstruct path
    if dist[end] == INF:
        return INF, []
    
    path = []
    cur = end
    while cur != -1:
        path.append(cur)
        cur = prev[cur]
    return dist[end], path[::-1]


# ============================================================
# PHẦN 5: Bellman-Ford
# ============================================================

def bellman_ford(edges: List[Tuple[int, int, int]], n: int, start: int):
    """
    Bellman-Ford — O(V × E) time, O(V) space
    Hỗ trợ negative weights, detect negative cycle
    
    edges: list of (u, v, weight)
    """
    INF = float('inf')
    dist = [INF] * n
    dist[start] = 0
    
    # Relax V-1 lần
    for _ in range(n - 1):
        for u, v, w in edges:
            if dist[u] != INF and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    
    # Kiểm tra negative cycle: nếu lần thứ V vẫn relaxed → negative cycle
    for u, v, w in edges:
        if dist[u] != INF and dist[u] + w < dist[v]:
            return None  # Negative cycle detected!
    
    return dist


# ============================================================
# PHẦN 6: Union-Find (Disjoint Set Union)
# ============================================================

class UnionFind:
    """
    Union-Find với Path Compression + Union by Rank
    Near O(1) per operation amortized (O(α(n)) — inverse Ackermann)
    """
    
    def __init__(self, n: int):
        self.parent = list(range(n))  # parent[i] = i (mỗi node tự là root)
        self.rank = [0] * n            # rank = upper bound của chiều cao
        self.components = n            # Số connected components
    
    def find(self, x: int) -> int:
        """Path compression: làm phẳng cây, mọi node trỏ thẳng vào root"""
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Recursive compression
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        """
        Union by rank: gắn cây thấp hơn vào cây cao hơn
        Trả về True nếu merge thành công (x, y khác components)
        """
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False  # Đã cùng component → có cycle nếu thêm edge này
        
        # Merge cây thấp vào cây cao
        if self.rank[rx] < self.rank[ry]:
            rx, ry = ry, rx  # Đảm bảo rank[rx] >= rank[ry]
        
        self.parent[ry] = rx  # ry gắn vào rx
        if self.rank[rx] == self.rank[ry]:
            self.rank[rx] += 1  # Tăng rank khi 2 cây cùng chiều cao
        
        self.components -= 1
        return True
    
    def connected(self, x: int, y: int) -> bool:
        return self.find(x) == self.find(y)


# ============================================================
# PHẦN 7: Kruskal's MST
# ============================================================

def kruskal_mst(n: int, edges: List[Tuple[int, int, int]]):
    """
    Kruskal — O(E log E) time
    edges: list of (weight, u, v)
    Trả về (total_weight, mst_edges)
    """
    edges_sorted = sorted(edges, key=lambda x: x[0])  # Sort by weight
    uf = UnionFind(n)
    mst_edges = []
    total_weight = 0
    
    for weight, u, v in edges_sorted:
        if uf.union(u, v):  # Nếu merge thành công (không tạo cycle)
            mst_edges.append((u, v, weight))
            total_weight += weight
            if len(mst_edges) == n - 1:  # MST có đúng V-1 edges
                break
    
    return total_weight, mst_edges


# ============================================================
# PHẦN 8: Topological Sort (Kahn's Algorithm — BFS)
# ============================================================

def topological_sort_kahn(graph: Graph, n: int) -> Optional[List[int]]:
    """
    Kahn's Algorithm (BFS-based) — O(V + E)
    Trả về topological order hoặc None nếu có cycle
    
    Ứng dụng: Course scheduling, build systems, dependency resolution
    """
    # Tính in-degree của mỗi node
    in_degree = [0] * n
    for u in range(n):
        for v, _ in graph.neighbors(u):
            in_degree[v] += 1
    
    # Khởi tạo queue với các node có in-degree = 0
    queue = deque(u for u in range(n) if in_degree[u] == 0)
    result = []
    
    while queue:
        u = queue.popleft()
        result.append(u)
        
        for v, _ in graph.neighbors(u):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)
    
    # Nếu không xử lý đủ V nodes → có cycle
    return result if len(result) == n else None


# ============================================================
# PHẦN 9: Demo
# ============================================================

# Tạo graph đơn giản
g = Graph(directed=False)
g.add_edge(0, 1, 4)
g.add_edge(0, 2, 1)
g.add_edge(2, 1, 2)
g.add_edge(1, 3, 1)
g.add_edge(2, 3, 5)

print("BFS từ 0:", bfs(g, 0))
print("DFS từ 0:", dfs_iterative(g, 0))
print("Dijkstra từ 0:", dijkstra(g, 0, 4))
# [0, 1, 3, 4] — dist 0→0=0, 0→1=3, 0→2=1, 0→3=4
```

---

## 6. Khi Nào Dùng / Không Dùng

### Nên Dùng Graph Khi:

- **Quan hệ phức tạp giữa các entities** không thể model bằng cây hay mảng
- **Tìm đường đi:** GPS, mạng máy tính, game pathfinding
- **Dependency resolution:** Build systems (Make, Gradle), package managers
- **Social network analysis:** Bạn chung, communities, influencers
- **Phát hiện chu trình:** Circular imports, deadlock detection
- **Network flow:** Max flow, bipartite matching

### Không Nên Dùng (hoặc Cần Cẩn Thận) Khi:

- **Dữ liệu phân cấp đơn giản:** Cây đủ rồi, graph phức tạp hơn không cần thiết
- **Memory-constrained environment:** Graph có thể tốn nhiều bộ nhớ với pointer/dict overhead
- **Edge list cực lớn:** Cần xem xét distributed graph processing (GraphX, Pregel)

---

## 7. So Sánh Các Thuật Toán Tìm Đường Đi Ngắn Nhất

| Thuật toán | Time | Space | Negative weights | Negative cycle | Single/All-pairs |
|---|---|---|---|---|---|
| **BFS** | O(V+E) | O(V) | Không (unweighted only) | N/A | Single source |
| **Dijkstra** | O((V+E) log V) | O(V) | Không | Không detect | Single source |
| **Bellman-Ford** | O(VE) | O(V) | Có | Có detect | Single source |
| **Floyd-Warshall** | O(V³) | O(V²) | Có | Có detect | All pairs |
| **Johnson's** | O(V² log V + VE) | O(V²) | Có | N/A | All pairs |

### So Sánh MST Algorithms

| Thuật toán | Time | Space | Tốt khi | Implement |
|---|---|---|---|---|
| **Kruskal** | O(E log E) | O(V+E) | Sparse graph, E << V² | Dùng Union-Find, sort edges |
| **Prim** | O((V+E) log V) | O(V) | Dense graph, E ≈ V² | Dùng priority queue |
| **Borůvka** | O(E log V) | O(V+E) | Parallel/distributed | Ít dùng trong practice |

---

## 8. Common Pitfalls

### Pitfall 1: Quên visited set khi duyệt graph (khác với tree)

```python
# SAI: Không có visited set → vòng lặp vô hạn nếu có cycle
def bfs_wrong(graph, start):
    queue = deque([start])
    while queue:
        node = queue.popleft()
        for neighbor, _ in graph.neighbors(node):
            queue.append(neighbor)  # Loop vô hạn!

# ĐÚNG: Luôn dùng visited set
def bfs_correct(graph, start):
    visited = {start}
    queue = deque([start])
    while queue:
        node = queue.popleft()
        for neighbor, _ in graph.neighbors(node):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### Pitfall 2: Dùng Dijkstra với negative edges

```python
# NGUY HIỂM: Dijkstra fail silently với negative weights
# Không báo lỗi nhưng trả về kết quả sai!
# Dùng Bellman-Ford hoặc SPFA khi có negative edges
```

### Pitfall 3: Nhầm lẫn connected vs strongly connected

```python
# Undirected graph: "connected" có nghĩa là mọi cặp node có path
# Directed graph: "strongly connected" = mọi cặp u,v: u→v VÀ v→u đều có path
# "weakly connected" directed graph = connected nếu bỏ qua hướng
```

### Pitfall 4: Stack overflow với DFS recursive trên graph lớn

```python
import sys
sys.setrecursionlimit(100000)  # Không phải cách tốt — dùng iterative DFS thay thế

# TỐTHƠN: Luôn implement DFS iterative với explicit stack cho production code
```

### Pitfall 5: Off-by-one trong Bellman-Ford

```python
# Phải relax V-1 lần (không phải V lần) trong phase chính
# Lần thứ V là để detect negative cycle
for i in range(n - 1):   # ĐÚNG: V-1 lần
    for u, v, w in edges:
        # relax...
```

### Pitfall 6: Build adjacency list sai cho undirected graph

```python
# SAI: Chỉ thêm một chiều
adj[u].append(v)  # Thiếu adj[v].append(u)

# ĐÚNG: Thêm cả hai chiều
adj[u].append(v)
adj[v].append(u)
```

---

## 9. Câu Hỏi Phỏng Vấn Hay Gặp

**Q1: BFS vs DFS — khi nào dùng cái nào?**
> **BFS:** Tìm đường đi ngắn nhất (unweighted), level-order traversal, bipartite check, khoảng cách từ source. BFS tìm đường ngắn nhất vì explore theo từng level.
> **DFS:** Detect cycle, topological sort, SCC, maze solving, backtracking. DFS đi sâu vào một nhánh trước.

**Q2: Tại sao Dijkstra không hoạt động với negative edges?**
> Dijkstra dựa trên greedy assumption: "Khi extract node u với dist[u], đường đi này là optimal và không thể cải thiện thêm." Với negative edges, sau khi "finalize" node u, có thể tìm được đường khác đi qua negative edge cho kết quả nhỏ hơn. Dijkstra không xét lại node đã finalize.

**Q3: Giải thích Union-Find với path compression và union by rank?**
> Path compression: Khi find(x), làm cho mọi node trên đường từ x đến root trỏ thẳng vào root (flatten tree). Union by rank: Luôn gắn cây thấp hơn vào cây cao hơn, tránh skewed tree. Kết hợp cả hai: O(α(n)) ≈ O(1) amortized. α là inverse Ackermann function, tăng cực kỳ chậm (α(n) ≤ 4 với mọi n thực tế).

**Q4: Number of Islands (LeetCode 200) — approach?**
> BFS hoặc DFS từ mỗi '1' chưa visited, đánh dấu toàn bộ island. Đếm số lần BFS/DFS bắt đầu. Hoặc dùng Union-Find: connect adjacent '1', đếm số components. DFS thường cleaner nhất.

**Q5: Detect cycle trong directed vs undirected graph?**
> **Directed:** DFS + 3-color marking (white/gray/black). Back edge → cycle. Hoặc Kahn's + check if topological sort includes all nodes.
> **Undirected:** DFS + visited set. Nếu gặp visited neighbor không phải parent → cycle. Hoặc Union-Find: nếu union(u,v) trả về False → cycle.

**Q6: Kruskal vs Prim — khi nào dùng cái nào?**
> **Kruskal:** Sort edges + Union-Find. O(E log E). Tốt với sparse graph (E << V²). Dễ implement.
> **Prim:** Priority queue, grow từ một node. O((V+E) log V). Tốt với dense graph (E ≈ V²). Tốt hơn Kruskal khi E >> V.

**Q7: Topological sort ứng dụng trong thực tế?**
> 1. Build systems: xác định thứ tự compile files (Makefile, Gradle). 2. Course scheduling: môn học có prerequisite. 3. Package managers: install dependencies đúng thứ tự. 4. Spreadsheet recalculation: cell dependencies. 5. Critical path analysis trong project management.
