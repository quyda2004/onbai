# Search Algorithms trong AI

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đang tìm đường từ nhà đến trường trong một thành phố mà bạn chưa biết rõ. Bạn có thể:

- **Đi thử từng ngõ một** (DFS — Depth-First Search): cứ đi thẳng vào một con đường đến khi bí mới quay lại.
- **Mở rộng vòng tròn dần dần** (BFS — Breadth-First Search): thử hết tất cả ngõ gần nhà trước, rồi mới đi xa hơn.
- **Đi theo ngõ rẻ tiền nhất** (UCS — Uniform Cost Search): chọn con đường tốn ít chi phí nhất ở mỗi bước.
- **Dùng bản năng + kinh nghiệm** (A* Search): kết hợp chi phí đã đi + ước tính khoảng cách còn lại để đi đường khôn nhất.

Đây là nền tảng của AI: máy tính "tìm kiếm" trong không gian các khả năng để tìm ra lời giải tốt nhất.

---

## Giải thích cho người đã biết lập trình (nâng cao)

Search algorithms trong AI được chia thành hai nhóm lớn:

### Uninformed Search (Tìm kiếm mù)
Không có thông tin về đích đến — chỉ biết cấu trúc đồ thị/cây trạng thái.

- **BFS**: Dùng queue (FIFO). Đảm bảo tìm được đường ngắn nhất (số bước) trong đồ thị không có trọng số. Space complexity O(b^d) — vấn đề lớn với branching factor cao.
- **DFS**: Dùng stack (LIFO hoặc đệ quy). Tốn ít bộ nhớ O(b*m) nhưng không đảm bảo tối ưu, có thể bị vòng lặp vô hạn.
- **UCS (Dijkstra generalized)**: Dùng priority queue theo g(n) — chi phí thực từ start đến n. Optimal và complete với điều kiện chi phí dương. Tương đương Dijkstra's algorithm.

### Informed Search (Tìm kiếm có hướng dẫn)
Sử dụng **heuristic function h(n)** — ước lượng chi phí từ n đến goal.

- **Greedy Best-First**: Expand node có h(n) nhỏ nhất. Nhanh nhưng không optimal, không complete trong không gian vô hạn.
- **A\***: f(n) = g(n) + h(n). Vừa tối ưu, vừa complete khi h(n) là **admissible** (không overestimate).

### Admissible Heuristic
- h(n) <= h*(n) (chi phí thực tế) với mọi n.
- **Manhattan distance**: |x1-x2| + |y1-y2| — dùng cho grid 4-directional.
- **Euclidean distance**: sqrt((x1-x2)² + (y1-y2)²) — dùng cho không gian liên tục.
- **Consistent (Monotone)**: h(n) <= c(n, a, n') + h(n') — đảm bảo A* không expand lại node đã thăm.

### Iterative Deepening A* (IDA*)
- Kết hợp IDS + A*: dùng DFS với giới hạn f-value thay vì depth.
- Space complexity: O(d) — tốt hơn A* rất nhiều trong bộ nhớ.
- Trade-off: tái tính toán nhiều lần, nhưng acceptable nếu heuristic tốt.

---

## Định nghĩa chính xác

**Search Problem** được định nghĩa bởi:
- **State space S**: tập tất cả trạng thái có thể.
- **Initial state s₀**.
- **Goal test**: hàm kiểm tra trạng thái đích.
- **Actions A(s)**: tập hành động từ trạng thái s.
- **Transition model Result(s, a)**: trạng thái mới sau khi thực hiện action a từ s.
- **Step cost c(s, a, s')**: chi phí của bước chuyển.

**A\* Algorithm**: tìm đường đi từ start đến goal tối thiểu hóa f(n) = g(n) + h(n), trong đó g(n) là chi phí thực từ start, h(n) là heuristic ước lượng chi phí đến goal.

**Admissible heuristic**: h(n) ≤ h*(n) với mọi n, trong đó h*(n) là chi phí tối ưu thực sự.

---

## Bảng so sánh các thuật toán

| Thuật toán | Complete? | Optimal? | Time Complexity | Space Complexity | Yêu cầu |
|------------|-----------|----------|-----------------|------------------|---------|
| BFS | Có (nếu b hữu hạn) | Có (step cost đều nhau) | O(b^d) | O(b^d) | Không |
| DFS | Không (graph có thể có cycle) | Không | O(b^m) | O(b*m) | Không |
| UCS | Có (chi phí > 0) | Có | O(b^(1+C*/ε)) | O(b^(1+C*/ε)) | Chi phí dương |
| Greedy | Không | Không | O(b^m) | O(b^m) | Heuristic h(n) |
| A* | Có | Có (admissible h) | O(b^d) | O(b^d) | Admissible h |
| IDA* | Có | Có (admissible h) | O(b^d) | O(d) | Admissible h |

*b = branching factor, d = độ sâu solution, m = độ sâu tối đa của cây, C* = chi phí optimal, ε = chi phí nhỏ nhất*

### Sơ đồ A* — Cách hoạt động

```
Start ──→ [open list: priority queue theo f]
          |
          ↓ Pop node có f nhỏ nhất
          [expand neighbors]
          |
          ↓ Với mỗi neighbor n':
            g(n') = g(n) + c(n, a, n')
            f(n') = g(n') + h(n')
            Nếu n' chưa thăm hoặc f mới tốt hơn → add to open list
          |
          ↓ Nếu n' == goal → reconstruct path
```

---

## Code mẫu

```python
import heapq
from typing import List, Tuple, Dict, Optional

def heuristic_manhattan(a: Tuple[int, int], b: Tuple[int, int]) -> int:
    """
    Manhattan distance — admissible heuristic cho grid 4-directional.
    Không bao giờ overestimate vì mỗi bước chỉ di chuyển 1 ô.
    """
    return abs(a[0] - b[0]) + abs(a[1] - b[1])


def astar(
    grid: List[List[int]],
    start: Tuple[int, int],
    goal: Tuple[int, int]
) -> Optional[List[Tuple[int, int]]]:
    """
    A* pathfinding trên grid 2D.
    
    Args:
        grid: Ma trận 2D, 0 = free, 1 = obstacle
        start: (row, col) điểm bắt đầu
        goal: (row, col) điểm đích
    
    Returns:
        Danh sách các tọa độ (path) hoặc None nếu không có đường
    """
    rows, cols = len(grid), len(grid[0])
    
    # Priority queue: (f_score, g_score, node)
    open_set = []
    heapq.heappush(open_set, (0 + heuristic_manhattan(start, goal), 0, start))
    
    # Lưu trữ chi phí tốt nhất đến mỗi node
    g_score: Dict[Tuple, float] = {start: 0}
    
    # Lưu node cha để reconstruct path
    came_from: Dict[Tuple, Optional[Tuple]] = {start: None}
    
    # 4 hướng di chuyển: up, down, left, right
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    
    while open_set:
        f, g, current = heapq.heappop(open_set)
        
        # Tìm thấy đích!
        if current == goal:
            return reconstruct_path(came_from, current)
        
        # Bỏ qua nếu đây là bản cũ trong queue (outdated entry)
        if g > g_score.get(current, float('inf')):
            continue
        
        for dr, dc in directions:
            nr, nc = current[0] + dr, current[1] + dc
            neighbor = (nr, nc)
            
            # Kiểm tra bounds và obstacle
            if not (0 <= nr < rows and 0 <= nc < cols) or grid[nr][nc] == 1:
                continue
            
            tentative_g = g + 1  # Chi phí mỗi bước = 1
            
            # Chỉ cập nhật nếu tìm được đường tốt hơn
            if tentative_g < g_score.get(neighbor, float('inf')):
                g_score[neighbor] = tentative_g
                f_score = tentative_g + heuristic_manhattan(neighbor, goal)
                came_from[neighbor] = current
                heapq.heappush(open_set, (f_score, tentative_g, neighbor))
    
    return None  # Không tìm được đường


def reconstruct_path(
    came_from: Dict,
    current: Tuple
) -> List[Tuple[int, int]]:
    """Truy ngược lại path từ goal về start."""
    path = []
    while current is not None:
        path.append(current)
        current = came_from[current]
    return list(reversed(path))


# ===== Demo =====
if __name__ == "__main__":
    # 0 = free, 1 = wall
    grid = [
        [0, 0, 0, 0, 1],
        [1, 1, 0, 1, 0],
        [0, 0, 0, 0, 0],
        [0, 1, 1, 1, 0],
        [0, 0, 0, 1, 0],
    ]
    
    start = (0, 0)
    goal = (4, 4)
    
    path = astar(grid, start, goal)
    
    if path:
        print(f"Path found! Length: {len(path)} steps")
        print(f"Path: {path}")
        
        # Visualize trên grid
        for r in range(len(grid)):
            row_str = ""
            for c in range(len(grid[0])):
                if (r, c) == start:
                    row_str += "S "
                elif (r, c) == goal:
                    row_str += "G "
                elif (r, c) in path:
                    row_str += "* "
                elif grid[r][c] == 1:
                    row_str += "# "
                else:
                    row_str += ". "
            print(row_str)
    else:
        print("No path found!")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Cần tìm đường tối ưu trong không gian trạng thái (game AI, robot navigation, GPS).
- Có thể thiết kế được heuristic tốt (admissible).
- BFS/DFS khi không gian trạng thái nhỏ và không cần tối ưu chi phí.
- UCS khi chi phí các bước khác nhau nhưng không có heuristic.
- IDA* khi bộ nhớ là constraint lớn.

**Không dùng khi:**
- Không gian trạng thái quá lớn (dùng ML/heuristic learning thay thế).
- Bài toán cần real-time response với state space khổng lồ (chess endgame dùng MCTS).
- Chi phí bước là âm (dùng Bellman-Ford thay UCS/A*).
- Không có goal state rõ ràng (dùng optimization algorithms: hill climbing, simulated annealing).

---

## So sánh với các khái niệm liên quan

| | A* | Dijkstra | BFS | MCTS |
|-|----|-----------|----|------|
| Heuristic | Có (f=g+h) | Không (chỉ g) | Không | Simulation |
| Optimal? | Có (admissible h) | Có | Có (unweighted) | Probabilistic |
| Use case | Pathfinding, puzzle | Shortest path (weighted) | Unweighted graphs | Game tree search |
| Space | O(b^d) | O(V) | O(b^d) | O(iterations) |
| Real-time? | Không tốt | Không tốt | Không tốt | Có thể |

---

## Lỗi thường gặp (Common Pitfalls)

- **Heuristic không admissible**: Nếu h(n) > h*(n) thì A* không đảm bảo tìm được optimal path. Ví dụ: dùng Euclidean distance cho grid 4-directional nhưng không nhân với step cost thực tế.
- **Không kiểm tra closed set**: Expand lại node đã thăm gây vòng lặp hoặc tốn thêm thời gian. Với consistent heuristic, lần đầu expand là optimal.
- **Dùng DFS trong không gian vô hạn**: DFS có thể đi sâu vô tận. Phải dùng depth limit hoặc IDS.
- **Không handle duplicate nodes trong open list**: Cần kiểm tra g_score khi pop từ heap, bỏ qua các entry cũ.
- **Confuse BFS optimal với weighted graph**: BFS chỉ tối ưu khi tất cả bước có chi phí bằng nhau.

---

## Câu hỏi phỏng vấn hay gặp

- A* khác Dijkstra như thế nào? Khi nào h(n)=0 thì A* trở thành gì?
- Admissible heuristic là gì? Cho ví dụ một heuristic không admissible.
- Tại sao BFS đảm bảo tìm đường ngắn nhất (số bước) trong unweighted graph?
- IDA* tiết kiệm bộ nhớ như thế nào so với A*? Trade-off là gì?
- Nếu heuristic consistent (monotone), tại sao A* không cần expand lại node?
- Thiết kế heuristic admissible cho bài toán 8-puzzle.
- Giải thích tại sao Greedy Best-First không optimal — cho ví dụ cụ thể.
