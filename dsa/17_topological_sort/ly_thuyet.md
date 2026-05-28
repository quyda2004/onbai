# Topological Sort — Sắp xếp Tô-pô

## 1. Giải thích cho người mới hoàn toàn

### Ví dụ từ cuộc sống: Mặc quần áo buổi sáng

Khi mặc quần áo, bạn phải theo một thứ tự nhất định:
- Mặc đồ lót **trước khi** mặc quần
- Mặc quần **trước khi** mặc giày
- Mặc áo lót **trước khi** mặc áo khoác

Topological Sort giải quyết bài toán: **Sắp xếp các việc sao cho mọi điều kiện "phải làm trước" đều được thỏa mãn**.

### Ví dụ khác: Học các môn ở đại học

```
Đại số tuyến tính → Xác suất thống kê → Machine Learning
Lập trình cơ bản → Cấu trúc dữ liệu → Thiết kế thuật toán
```

Topological Sort sẽ cho ra thứ tự học hợp lệ: đảm bảo bạn học môn tiên quyết trước khi học môn phụ thuộc.

### Điều kiện quan trọng: KHÔNG được có vòng tròn phụ thuộc

Nếu A phụ thuộc B, B phụ thuộc C, và C lại phụ thuộc A → **vô lý!** (không bao giờ bắt đầu được). Đây gọi là **cycle** và topological sort **không tồn tại** nếu có cycle.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Định nghĩa hình thức

Topological Sort của một **DAG (Directed Acyclic Graph)** là một thứ tự tuyến tính của tất cả đỉnh sao cho: với mọi cạnh có hướng `u → v`, đỉnh `u` xuất hiện trước đỉnh `v` trong thứ tự đó.

**Quan trọng:** Topological sort **không duy nhất** — một DAG có thể có nhiều thứ tự topological hợp lệ.

### Tại sao cần DAG?

Giả sử có cycle: A → B → C → A. Trong bất kỳ thứ tự tuyến tính nào, giả sử A đứng trước B và B đứng trước C. Vì có cạnh C → A, ta cần C đứng trước A — mâu thuẫn! Proof by contradiction cho thấy cycle → không tồn tại topological order.

### Ứng dụng production

1. **Build systems**: GNU Make, Bazel, Gradle phân tích dependency giữa targets, build theo topological order
2. **Package managers**: npm, pip, apt giải quyết dependency tree (thực ra là DAG) trước khi cài đặt
3. **Task scheduling**: Apache Airflow, Luigi sắp xếp thứ tự các job/task
4. **Course prerequisite systems**: Hệ thống đăng ký môn học kiểm tra và sắp xếp
5. **Spreadsheet evaluation**: Excel tính toán cells theo topological order của dependency
6. **Compiler**: Thứ tự biên dịch các module, header files

### So sánh hai thuật toán

**Kahn's Algorithm (BFS-based):**
- Bắt đầu từ các đỉnh không có in-degree = 0 (không có tiên quyết)
- Lần lượt loại bỏ đỉnh và giảm in-degree của neighbor
- Nếu kết quả cuối cùng có ít hơn n đỉnh → graph có cycle

**DFS-based:**
- Chạy DFS, ghi nhận finish time
- Reverse postorder = topological order
- Cần 3 trạng thái để detect cycle: unvisited (white), visiting (gray), visited (black)
- Gray → gray edge = back edge = cycle

---

## 3. Định nghĩa chính xác

**DAG (Directed Acyclic Graph)**: Đồ thị có hướng không chứa chu trình.

**Topological Ordering**: Với DAG G=(V,E), topological ordering là một ánh xạ φ: V → {1,...,|V|} sao cho với mọi cạnh (u,v) ∈ E, ta có φ(u) < φ(v).

**In-degree** của đỉnh v: Số cạnh đi vào v, ký hiệu `indeg(v)`.

**Out-degree** của đỉnh v: Số cạnh đi ra từ v, ký hiệu `outdeg(v)`.

**Source vertex**: Đỉnh có in-degree = 0. Topological sort bắt đầu từ các source vertices.

**Sink vertex**: Đỉnh có out-degree = 0. Topological sort kết thúc ở sink vertices.

**Kahn's Algorithm invariant**: Luôn giữ queue chứa các đỉnh có in-degree = 0 hiện tại.

---

## 4. Bảng Độ phức tạp đầy đủ

### Kahn's Algorithm (BFS-based)

| Thao tác | Best Case | Average Case | Worst Case | Điều kiện |
|----------|-----------|--------------|------------|-----------|
| Khởi tạo in-degree array | O(V+E) | O(V+E) | O(V+E) | Duyệt toàn bộ adjacency list |
| Tìm source vertices ban đầu | O(V) | O(V) | O(V) | Scan in-degree array |
| BFS main loop | O(V+E) | O(V+E) | O(V+E) | Mỗi đỉnh xử lý 1 lần, mỗi cạnh xét 1 lần |
| **Tổng** | **O(V+E)** | **O(V+E)** | **O(V+E)** | |

| Thao tác | Space (Auxiliary) | Ghi chú |
|----------|-------------------|---------|
| In-degree array | O(V) | |
| Queue | O(V) | Tối đa V đỉnh trong queue |
| Result array | O(V) | |
| **Tổng space** | **O(V)** | Không tính adjacency list O(V+E) |

### DFS-based Topological Sort

| Thao tác | Best Case | Average Case | Worst Case | Điều kiện |
|----------|-----------|--------------|------------|-----------|
| DFS toàn bộ graph | O(V+E) | O(V+E) | O(V+E) | Mỗi đỉnh/cạnh xét đúng 1 lần |
| Reverse postorder | O(V) | O(V) | O(V) | Đảo list kết quả |
| **Tổng** | **O(V+E)** | **O(V+E)** | **O(V+E)** | |

| Thao tác | Space (Auxiliary) | Ghi chú |
|----------|-------------------|---------|
| Color/visited array | O(V) | 3 màu cho cycle detection |
| Call stack (đệ quy) | O(V) | Sâu tối đa V trong worst case (DAG dạng chain) |
| Result stack/list | O(V) | |
| **Tổng space** | **O(V)** | Không tính adjacency list |

### So sánh Kahn's vs DFS-based

| Tiêu chí | Kahn's Algorithm | DFS-based |
|----------|------------------|-----------|
| Time | O(V+E) | O(V+E) |
| Space | O(V) | O(V) |
| Cycle Detection | Tự nhiên: result.length < n | Cần 3 màu, phát hiện back edge |
| Thứ tự output | Theo BFS layers | Reverse postorder |
| Implementation | Dễ hơn | Cần cẩn thận 3 trạng thái |
| Uniqueness | Nếu luôn có đúng 1 source → unique | Phụ thuộc thứ tự DFS |
| Use case | Cycle detection rõ ràng | Đã có DFS infrastructure |

---

## 5. Code mẫu Python

### Kahn's Algorithm

```python
from collections import deque

def topological_sort_kahn(n, edges):
    """
    Kahn's Algorithm (BFS-based Topological Sort).

    Args:
        n: số đỉnh (0 đến n-1)
        edges: list of (u, v) — cạnh từ u đến v (u phải đứng trước v)

    Returns:
        list: thứ tự topological, hoặc [] nếu có cycle
    """
    # Bước 1: Xây dựng adjacency list và in-degree array
    adj = [[] for _ in range(n)]
    indegree = [0] * n

    for u, v in edges:
        adj[u].append(v)
        indegree[v] += 1  # v có thêm 1 tiên quyết

    # Bước 2: Thêm tất cả source vertices (in-degree = 0) vào queue
    queue = deque()
    for node in range(n):
        if indegree[node] == 0:
            queue.append(node)

    result = []

    # Bước 3: BFS — xử lý từng đỉnh
    while queue:
        node = queue.popleft()
        result.append(node)

        # "Loại bỏ" node này: giảm in-degree của tất cả neighbor
        for neighbor in adj[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                # neighbor không còn tiên quyết nào → có thể xử lý
                queue.append(neighbor)

    # Bước 4: Kiểm tra cycle
    if len(result) < n:
        return []  # Có cycle — không phải DAG

    return result


# Ví dụ: Course Prerequisites
# Môn: 0=Đại số, 1=Xác suất, 2=ML, 3=Lập trình, 4=CTDL, 5=Thuật toán
# Edges: (tiên quyết, môn học)
edges = [(0, 1), (1, 2), (3, 4), (4, 5), (0, 4)]
print(topological_sort_kahn(6, edges))
# Ví dụ output: [0, 3, 1, 4, 2, 5] (một thứ tự hợp lệ)
```

### Kahn's với Lexicographically Smallest Order

```python
import heapq
from collections import defaultdict

def topological_sort_lexical(n, edges):
    """
    Topological sort với thứ tự từ điển nhỏ nhất.
    Dùng min-heap thay vì queue thông thường.
    """
    adj = defaultdict(list)
    indegree = [0] * n

    for u, v in edges:
        adj[u].append(v)
        indegree[v] += 1

    # Min-heap để luôn chọn đỉnh có index nhỏ nhất
    heap = []
    for node in range(n):
        if indegree[node] == 0:
            heapq.heappush(heap, node)

    result = []

    while heap:
        node = heapq.heappop(heap)  # Lấy đỉnh nhỏ nhất
        result.append(node)
        for neighbor in adj[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                heapq.heappush(heap, neighbor)

    return result if len(result) == n else []
```

### DFS-based Topological Sort

```python
def topological_sort_dfs(n, edges):
    """
    DFS-based Topological Sort dùng reverse postorder.
    3 màu: 0=unvisited (white), 1=visiting (gray), 2=visited (black)
    """
    adj = [[] for _ in range(n)]
    for u, v in edges:
        adj[u].append(v)

    color = [0] * n  # 0: white, 1: gray, 2: black
    result = []
    has_cycle = [False]

    def dfs(node):
        if has_cycle[0]:
            return
        color[node] = 1  # Đánh dấu đang thăm (gray)

        for neighbor in adj[node]:
            if color[neighbor] == 1:
                # Back edge → cycle!
                has_cycle[0] = True
                return
            if color[neighbor] == 0:
                dfs(neighbor)

        color[node] = 2  # Đánh dấu đã xong (black)
        result.append(node)  # Thêm vào cuối khi XONG (postorder)

    for node in range(n):
        if color[node] == 0:
            dfs(node)

    if has_cycle[0]:
        return []

    # Reverse postorder = topological order
    return result[::-1]


# Test
edges = [(5, 2), (5, 0), (4, 0), (4, 1), (2, 3), (3, 1)]
print(topological_sort_dfs(6, edges))
# Output: [5, 4, 2, 3, 1, 0] hoặc tương đương hợp lệ
```

### Ứng dụng: Course Schedule (LeetCode 207, 210)

```python
def can_finish(num_courses, prerequisites):
    """
    LeetCode 207: Kiểm tra có thể hoàn thành tất cả course không.
    prerequisites[i] = [a, b]: phải học b trước a.
    """
    # Dùng Kahn's algorithm
    adj = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses

    for course, prereq in prerequisites:
        adj[prereq].append(course)
        indegree[course] += 1

    queue = deque([i for i in range(num_courses) if indegree[i] == 0])
    count = 0

    while queue:
        node = queue.popleft()
        count += 1
        for neighbor in adj[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return count == num_courses  # True nếu không có cycle


def find_order(num_courses, prerequisites):
    """
    LeetCode 210: Trả về thứ tự học hợp lệ.
    """
    adj = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses

    for course, prereq in prerequisites:
        adj[prereq].append(course)
        indegree[course] += 1

    queue = deque([i for i in range(num_courses) if indegree[i] == 0])
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in adj[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return result if len(result) == num_courses else []
```

### Phát hiện Cycle và Tìm Cycle

```python
def find_cycle_in_dag(n, edges):
    """
    Tìm và trả về các đỉnh trong cycle (nếu có).
    Dùng DFS với 3 màu.
    """
    adj = [[] for _ in range(n)]
    for u, v in edges:
        adj[u].append(v)

    color = [0] * n
    parent = [-1] * n
    cycle = []

    def dfs(node):
        color[node] = 1
        for neighbor in adj[node]:
            if color[neighbor] == 1:
                # Tìm thấy cycle, trace back để lấy cycle
                cycle_path = [neighbor, node]
                curr = node
                while parent[curr] != neighbor:
                    curr = parent[curr]
                    cycle_path.append(curr)
                cycle.extend(cycle_path)
                return True
            if color[neighbor] == 0:
                parent[neighbor] = node
                if dfs(neighbor):
                    return True
        color[node] = 2
        return False

    for node in range(n):
        if color[node] == 0:
            if dfs(node):
                break

    return cycle
```

---

## 6. Khi nào dùng / Không dùng

### Dùng Topological Sort khi:
- Có tập tác vụ với **dependency/prerequisite** quan hệ
- Cần xác định **thứ tự thực hiện** hợp lệ
- Cần **kiểm tra cycle** trong dependency graph
- Bài toán scheduling với ràng buộc "A phải trước B"
- Build system, compilation order
- Data pipeline orchestration

### Dùng Kahn's khi:
- Cần detect cycle một cách rõ ràng và đơn giản
- Cần xử lý theo layers (level-by-level)
- Muốn cài đặt iterative (không đệ quy)

### Dùng DFS-based khi:
- Đã có DFS infrastructure
- Cần postorder information cho mục đích khác
- Implement Kosaraju's SCC (vốn dựa trên DFS)

### Không dùng Topological Sort khi:
- Graph có cycle → không tồn tại topological order
- Graph là undirected → khái niệm "thứ tự" không áp dụng
- Không cần thứ tự tuyến tính, chỉ cần duyệt → dùng DFS/BFS thông thường

---

## 7. So sánh với các thuật toán liên quan

| Thuật toán | Input | Output | Cycle | Complexity |
|------------|-------|--------|-------|------------|
| Topological Sort (Kahn's) | DAG | Thứ tự tuyến tính | Detect nếu result < n | O(V+E) |
| Topological Sort (DFS) | DAG | Reverse postorder | Detect back edge | O(V+E) |
| DFS thông thường | Graph bất kỳ | Thứ tự thăm | Detect nhưng không xử lý | O(V+E) |
| BFS thông thường | Graph bất kỳ | Level-order | Không tự nhiên detect | O(V+E) |
| Kosaraju SCC | Directed graph | Các SCC | Không (tìm SCC) | O(V+E) |
| Dijkstra | Weighted graph | Shortest path | Không (cần non-negative) | O((V+E)logV) |

---

## 8. Common Pitfalls

### Pitfall 1: Áp dụng topological sort cho undirected graph
```python
# Topological sort chỉ có nghĩa với DIRECTED graph
# Undirected graph không có "thứ tự" cạnh
# Nếu convert undirected thành directed, sẽ có cycle ngay
```

### Pitfall 2: Nhầm điều kiện cycle trong Kahn's
```python
# ĐÚNG: so sánh với n (tổng số đỉnh)
if len(result) < n:
    return []  # Cycle detected

# SAI: kiểm tra queue rỗng — queue có thể rỗng mà vẫn chưa xử lý hết
# (xảy ra khi có cycle, các đỉnh trong cycle có in-degree > 0 không vào queue)
```

### Pitfall 3: Nhầm chiều cạnh khi xây dựng adjacency list
```python
# prerequisites = [(a, b)]: b là tiên quyết của a → cạnh đi từ b đến a
# b → a (không phải a → b!)

# SAI
for a, b in prerequisites:
    adj[a].append(b)  # Sai chiều!

# ĐÚNG
for a, b in prerequisites:
    adj[b].append(a)  # b phải đến trước a → cạnh b → a
    indegree[a] += 1
```

### Pitfall 4: Quên xử lý disconnected graph trong DFS-based
```python
# SAI: chỉ DFS từ một đỉnh
dfs(0)
return result[::-1]

# ĐÚNG: DFS từ mọi đỉnh chưa thăm
for node in range(n):
    if color[node] == 0:
        dfs(node)
```

### Pitfall 5: Topological sort không unique khi có nhiều sources
```python
# Khi queue có nhiều đỉnh, Kahn's có thể chọn bất kỳ
# → kết quả không deterministic nếu không sort
# Nếu cần unique/lexicographic: dùng min-heap thay queue
```

---

## 9. Câu hỏi phỏng vấn hay gặp

1. **Topological sort là gì và khi nào nó tồn tại?**
   Trả lời: Linear ordering của DAG sao cho với mọi cạnh u→v, u đứng trước v. Chỉ tồn tại khi graph là DAG (không có cycle).

2. **Giải thích Kahn's Algorithm từng bước.**
   Trả lời: (1) Tính in-degree tất cả đỉnh. (2) Thêm đỉnh in-degree=0 vào queue. (3) BFS: pop đỉnh, thêm vào result, giảm in-degree neighbor, thêm neighbor có in-degree=0 vào queue. (4) Nếu result.length < n → có cycle.

3. **Tại sao Kahn's Algorithm detect cycle dễ hơn DFS-based?**
   Trả lời: Đỉnh trong cycle luôn có in-degree > 0 sau khi loại bỏ non-cycle vertices. Chúng không bao giờ vào queue → result.length < n là điều kiện đơn giản. DFS cần phân biệt 3 trạng thái.

4. **DFS-based topological sort: tại sao lại cần reverse postorder?**
   Trả lời: Postorder = node được thêm vào list khi DFS xong subtree của nó. Node không có dependency xong trước (added first) → chúng cần đứng SAU trong topological order. Reverse → chúng đứng CUỐI, node source đứng ĐẦU.

5. **Có bao nhiêu topological ordering hợp lệ cho một DAG?**
   Trả lời: Không duy nhất. Tối thiểu 1 (nếu DAG là chain). Tối đa n! (nếu không có cạnh nào). Số lượng phụ thuộc vào cấu trúc DAG.

6. **Ứng dụng của topological sort trong build systems?**
   Trả lời: Build targets có dependencies. Topological sort đảm bảo thư viện được compile trước executable phụ thuộc vào nó. Make, CMake, Bazel đều dùng topological sort.

7. **Nếu có nhiều topological ordering hợp lệ, làm thế nào tìm thứ tự lexicographically smallest?**
   Trả lời: Thay queue thông thường bằng min-heap trong Kahn's algorithm. Luôn chọn đỉnh có index nhỏ nhất trong số các source hiện tại.

8. **Cycle detection trong directed graph: back edge là gì?**
   Trả lời: Trong DFS, back edge là cạnh từ đỉnh đang thăm (gray) đến tổ tiên của nó (cũng gray). Tồn tại back edge ↔ tồn tại cycle trong directed graph.
