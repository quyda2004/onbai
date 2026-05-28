# DSA Roadmap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            DSA LEARNING PATH                            │
│                   Mũi tên = cần học trước (prerequisite)                │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  📊 01 · Big O      │
                        │     Notation        │
                        └──────────┬──────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  📋 02 · Array &    │
                        │       String        │
                        └─┬──┬───┬───┬───┬───┘
                          │  │   │   │   │
           ┌──────────────┘  │   │   │   └────────────────┐
           │     ┌───────────┘   │   └──────────┐         │
           │     │               │              │         │
   ┌───────▼──┐ ┌▼──────────┐ ┌─▼──────────┐ ┌▼───────┐ ┌▼────────────┐
   │ 🗺️  03   │ │  👉 04    │ │   🪟 05    │ │ ➕ 06  │ │  🔍 14      │
   │ HashMap  │ │  Two      │ │  Sliding   │ │ Prefix │ │  Binary     │
   │ HashSet  │ │  Pointers │ │  Window    │ │ Sum    │ │  Search     │
   └───┬──────┘ └─┬─────────┘ └────────────┘ └────────┘ └─────────────┘
       │          │
       └────┬─────┘
            │
   ┌────────▼────────┐         ┌──────────────────┐
   │  🔄 07 ·        │         │  ↕️  15 · Sorting │
   │  Recursion      │         │  (song song)      │
   └───┬─────┬───────┘         └──────────────────┘
       │     │
  ┌────▼──┐ ┌▼──────────┐
  │ 📚 08 │ │  🔗 09    │
  │ Stack │ │  Linked   │
  │ Queue │ │  List     │
  └───┬───┘ └──┬────────┘
      │        │
      └───┬────┘
          │
 ┌────────▼────────────┐
 │  🌳 10 · Tree / BST │
 └──────┬──────┬───────┘
        │      │
  ┌─────▼──┐ ┌─▼──────┐
  │ ⛰️ 11  │ │ 🔤 13  │
  │  Heap  │ │  Trie  │
  └───┬────┘ └────────┘
      │
 ┌────▼──────────────┐
 │  🕸️ 12 · Graph   │
 └────┬──────────────┘
      │
 ┌────▼──────────────┐
 │  🌊 16 · DFS/BFS  │
 └────┬──────────────┘
      │
      ├────────────────────────┐
      │                        │
 ┌────▼────────────┐   ┌───────▼──────────┐
 │  📐 17 ·        │   │  💰 18 · Greedy  │
 │  Topological    │   └───────┬──────────┘
 │  Sort           │           │
 └─────────────────┘   ┌───────▼──────────┐
                        │  🧮 19 · Dynamic │
                        │  Programming     │
                        └───────┬──────────┘
                                │
                        ┌───────▼──────────┐
                        │  ↩️ 20 ·         │
                        │  Backtracking    │
                        └──────────────────┘
```

---

## Giải thích từng topic

| # | Topic | Mô tả ngắn | Tài liệu |
|---|-------|------------|----------|
| 01 | 📊 Big O Notation | Đo độ phức tạp thời gian & không gian — nền tảng để đánh giá mọi thuật toán | [📖 Lý thuyết](01_bigo/ly_thuyet.md) · [📝 Trắc nghiệm](01_bigo/trac_nghiem.md) |
| 02 | 📋 Array & String | Cấu trúc dữ liệu cơ bản nhất — index, slice, in-place, hai đầu mảng | [📖 Lý thuyết](02_array_string/ly_thuyet.md) · [📝 Trắc nghiệm](02_array_string/trac_nghiem.md) |
| 03 | 🗺️ HashMap / HashSet | Tra cứu O(1) bằng hash — đếm tần suất, kiểm tra tồn tại, nhóm phần tử | [📖 Lý thuyết](03_hashmap_hashset/ly_thuyet.md) · [📝 Trắc nghiệm](03_hashmap_hashset/trac_nghiem.md) |
| 04 | 👉 Two Pointers | Dùng 2 con trỏ đi từ 2 đầu hoặc cùng chiều — giảm O(n²) xuống O(n) | [📖 Lý thuyết](04_two_pointers/ly_thuyet.md) · [📝 Trắc nghiệm](04_two_pointers/trac_nghiem.md) |
| 05 | 🪟 Sliding Window | Cửa sổ trượt trên mảng — tìm subarray/substring thoả điều kiện | [📖 Lý thuyết](05_sliding_window/ly_thuyet.md) · [📝 Trắc nghiệm](05_sliding_window/trac_nghiem.md) |
| 06 | ➕ Prefix Sum | Tổng tiền tố — trả lời range query O(1) sau khi build O(n) | [📖 Lý thuyết](06_prefix_sum/ly_thuyet.md) · [📝 Trắc nghiệm](06_prefix_sum/trac_nghiem.md) |
| 07 | 🔄 Recursion | Hàm tự gọi chính nó — base case + recursive case, nền tảng của DFS/DP | [📖 Lý thuyết](07_recursion/ly_thuyet.md) · [📝 Trắc nghiệm](07_recursion/trac_nghiem.md) |
| 08 | 📚 Stack & Queue | LIFO vs FIFO — monotonic stack, deque, mô phỏng call stack | [📖 Lý thuyết](08_stack_queue/ly_thuyet.md) · [📝 Trắc nghiệm](08_stack_queue/trac_nghiem.md) |
| 09 | 🔗 Linked List | Node nối nhau bằng pointer — reverse, fast/slow pointer, cycle detection | [📖 Lý thuyết](09_linked_list/ly_thuyet.md) · [📝 Trắc nghiệm](09_linked_list/trac_nghiem.md) |
| 10 | 🌳 Tree / BST | Cấu trúc cây — traversal Pre/In/Post, BST O(log n), balanced tree | [📖 Lý thuyết](10_tree_bst/ly_thuyet.md) · [📝 Trắc nghiệm](10_tree_bst/trac_nghiem.md) |
| 11 | ⛰️ Heap | Min/Max Heap — Priority Queue, heapify O(n), top-K problems | [📖 Lý thuyết](11_heap/ly_thuyet.md) · [📝 Trắc nghiệm](11_heap/trac_nghiem.md) |
| 12 | 🕸️ Graph | Đỉnh + cạnh — adjacency list/matrix, directed/undirected, weighted | [📖 Lý thuyết](12_graph/ly_thuyet.md) · [📝 Trắc nghiệm](12_graph/trac_nghiem.md) |
| 14 | 🔍 Binary Search | Tìm kiếm nhị phân O(log n) — áp dụng với mảng đã sort và search space ẩn | [📖 Lý thuyết](14_binary_search/ly_thuyet.md) · [📝 Trắc nghiệm](14_binary_search/trac_nghiem.md) |
| 16 | 🌊 DFS / BFS | Duyệt đồ thị theo chiều sâu (stack) hoặc chiều rộng (queue) | [📖 Lý thuyết](16_dfs_bfs/ly_thuyet.md) · [📝 Trắc nghiệm](16_dfs_bfs/trac_nghiem.md) |
| 17 | 📐 Topological Sort | Sắp xếp DAG — phát hiện cycle, thứ tự compile/build dependencies | [📖 Lý thuyết](17_topological_sort/ly_thuyet.md) · [📝 Trắc nghiệm](17_topological_sort/trac_nghiem.md) |
| 18 | 💰 Greedy | Chọn tối ưu cục bộ ở mỗi bước — interval scheduling, huffman, coin change | [📖 Lý thuyết](18_greedy/ly_thuyet.md) · [📝 Trắc nghiệm](18_greedy/trac_nghiem.md) |
| 19 | 🧮 Dynamic Programming | Ghi nhớ kết quả subproblem — memoization (top-down) vs tabulation (bottom-up) | [📖 Lý thuyết](19_dynamic_programming/ly_thuyet.md) · [📝 Trắc nghiệm](19_dynamic_programming/trac_nghiem.md) |
| 20 | ↩️ Backtracking | Thử + quay lui — permutation, combination, N-Queens, Sudoku | [📖 Lý thuyết](20_backtracking/ly_thuyet.md) · [📝 Trắc nghiệm](20_backtracking/trac_nghiem.md) |

---

## Màu theo độ khó

```
  🔵 Nền tảng   →   Big O Notation
  🟢 Cơ bản     →   Array, HashMap, Two Pointers, Sliding Window, Prefix Sum
  🟡 Trung bình →   Recursion, Stack, Queue, Linked List, Tree, Heap, Trie, Binary Search, Sorting
  🔴 Nâng cao   →   Graph, DFS/BFS, Topological Sort, Greedy, DP, Backtracking
```

---

## Lộ trình theo tuần

```
  Tuần 1-2  ──▶  Big O  ──▶  Array  ──▶  HashMap  ──▶  Two Pointers
                                                              │
  Tuần 3-4  ◀────────────────────────────────────────────────┘
      │
      ▼
  Sliding Window  ──▶  Prefix Sum  ──▶  Binary Search  ──▶  Recursion
                                                                  │
  Tuần 5-6  ◀───────────────────────────────────────────────────┘
      │
      ▼
  Stack & Queue  ──▶  Linked List  ──▶  Tree/BST  ──▶  Heap  ──▶  Trie
                                                                      │
  Tuần 7-8  ◀───────────────────────────────────────────────────────┘
      │
      ▼
  Graph  ──▶  DFS/BFS  ──▶  Topological Sort  ──▶  Sorting
                │
  Tuần 9-12  ◀─┘
      │
      ▼
  Greedy  ──▶  Dynamic Programming  ──▶  Backtracking
                                               │
                                         ✅ DONE!
```
