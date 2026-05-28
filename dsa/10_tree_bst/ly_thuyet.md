# Binary Tree & BST — Cây Nhị Phân và Cây Tìm Kiếm Nhị Phân

## 1. Giải thích cho người mới hoàn toàn

### Cây nhị phân (Binary Tree)

Tưởng tượng một **cây gia phả lộn ngược**: ở trên cùng là ông tổ (gốc cây, gọi là **root**). Ông tổ có tối đa 2 đứa con. Mỗi đứa con cũng có tối đa 2 đứa con. Cứ thế xuống dưới.

- Cụ tổ trên cùng = **root**.
- Mỗi người (kể cả không có con) = **node**.
- Người không có con nào = **leaf** (lá).
- Mối liên kết cha → con = **edge** (cạnh).

"Nhị phân" nghĩa là **mỗi người chỉ có tối đa 2 con** (con trái và con phải). Khác với cây gia phả thật có thể có 3, 5 hay 10 đứa con.

### Cây tìm kiếm nhị phân (BST)

BST là một **dạng đặc biệt** của cây nhị phân với một quy tắc: **mọi đứa con bên trái nhỏ hơn cha, mọi đứa con bên phải lớn hơn cha**.

Ví dụ:
```
        8
       / \
      3   10
     / \    \
    1   6    14
```

Quy tắc: 3 < 8, 10 > 8. 1 < 3, 6 > 3. 14 > 10.  
Ngoài ra: tất cả node ở subtree trái (1, 3, 6) đều < 8; tất cả node ở subtree phải (10, 14) đều > 8.

### BST giải quyết bài toán gì?

Bạn có 1 triệu số. Bạn muốn:
- Hỏi: số 12345 có trong tập không?
- Thêm số mới vào.
- Xóa số.

**Mảng**: tìm số mất O(n) (1 triệu phép so sánh).  
**Mảng sorted**: tìm O(log n) (~20 phép) bằng binary search, nhưng chèn 1 số mới mất O(n) vì phải dịch.  
**BST cân bằng**: tìm O(log n), chèn O(log n), xóa O(log n). **Đầy đủ cả 3 thao tác**.

→ BST là cấu trúc "phù hợp với việc thay đổi liên tục" mà vẫn tìm kiếm nhanh.

---

## 2. Giải thích nâng cao cho người chuyên ngành

### Phân loại cây nhị phân

1. **Full binary tree**: mỗi node có 0 hoặc 2 con (không có node nào có 1 con).
2. **Complete binary tree**: tất cả tầng đều đầy trừ tầng cuối, tầng cuối lấp từ trái sang phải. Heap chính là complete binary tree → có thể lưu trong array.
3. **Perfect binary tree**: tất cả internal node đều có 2 con và tất cả leaf cùng tầng.
4. **Balanced binary tree**: chênh lệch chiều cao giữa subtree trái-phải tại mọi node ≤ 1 (định nghĩa AVL) → đảm bảo height O(log n).
5. **Degenerate (skewed) tree**: như linked list, mỗi node chỉ có 1 con → height O(n).

### BST và các biến thể self-balancing

- **BST cơ bản**: insert theo thứ tự sorted → suy biến thành linked list, mọi operation về O(n).
- **AVL Tree**: cân bằng nghiêm ngặt (|height_left - height_right| ≤ 1). Insert/delete O(log n), rotation phức tạp. Search rất nhanh nhờ cây thấp.
- **Red-Black Tree**: cân bằng lỏng hơn AVL. Insert/delete ít rotation hơn → tốt cho workload write-heavy. Dùng trong `std::map` (C++), `TreeMap` (Java), epoll Linux.
- **B-Tree / B+ Tree**: cây cân bằng nhiều con (n-ary), tối ưu cho disk I/O. Dùng làm index trong database (MySQL InnoDB, PostgreSQL).
- **Treap, Splay Tree, Scapegoat Tree**: các biến thể khác.

### Traversals (duyệt cây)

Cho cây nhị phân:
```
    A
   / \
  B   C
 / \
D   E
```

- **Inorder (LNR — Left, Node, Right)**: D, B, E, A, C → **Trên BST, inorder cho dãy sorted tăng dần** — đây là tính chất then chốt để chứng minh BST.
- **Preorder (NLR — Node, Left, Right)**: A, B, D, E, C → dùng để **serialize/clone** cây.
- **Postorder (LRN — Left, Right, Node)**: D, E, B, C, A → dùng để **xóa cây** (xóa con trước cha), tính kích thước subtree.
- **Level-order (BFS)**: A, B, C, D, E → dùng queue, hữu ích cho tìm node theo tầng, shortest path.

### Các thao tác BST chi tiết

**Insert**: đi từ root, so sánh với mỗi node, đi trái/phải đến khi tìm vị trí null → gắn node mới.

**Search**: tương tự insert nhưng dừng khi tìm thấy hoặc đi đến null.

**Delete** (3 case):
- Node là leaf → xóa thẳng.
- Node có 1 con → thay node bằng con duy nhất.
- Node có 2 con → tìm **inorder successor** (node nhỏ nhất ở subtree phải), copy giá trị, xóa successor (đệ quy, successor luôn rơi vào case 1 hoặc 2).

**Lowest Common Ancestor (LCA)**:
- Trên BST: từ root, nếu cả 2 < root đi trái, cả 2 > root đi phải, ngược lại root là LCA. O(h).
- Trên cây nhị phân tổng quát: đệ quy, tìm 2 node ở 2 subtree con → node hiện tại là LCA.

### Hệ quả về height

- **n node, height h**: n ≤ 2^(h+1) - 1, do đó h ≥ log₂(n+1) - 1.
- **Balanced tree**: h = O(log n).
- **Skewed tree**: h = O(n).

Tất cả operation BST có complexity O(h) → cân bằng cây là then chốt.

### Morris Traversal

Inorder traversal không cần stack/đệ quy bằng cách tạm thời sửa con trỏ (threading). O(n) time, O(1) space — khác với recursive/stack O(n) space.

---

## 3. Định nghĩa chính xác

**Binary Tree**: Cấu trúc dữ liệu phân cấp trong đó mỗi node có tối đa 2 con (left, right). Cây rỗng cũng là cây nhị phân hợp lệ.

**Binary Search Tree (BST)**: Binary tree thỏa mãn BST property: với mọi node N, tất cả giá trị trong subtree trái < N.val, tất cả giá trị trong subtree phải > N.val (định nghĩa nghiêm ngặt; có biến thể cho phép trùng).

**Height của cây**: Số cạnh dài nhất từ root tới một leaf. Cây 1 node có height 0; cây rỗng có height -1 (quy ước).

**Depth của node**: Số cạnh từ root tới node đó. Root có depth 0.

**Balanced BST**: BST trong đó |height(left) - height(right)| ≤ k cho mọi node, với k là hằng số nhỏ (thường = 1 trong AVL).

**Subtree**: Toàn bộ phần cây bắt nguồn từ một node bất kỳ.

---

## 4. Bảng Độ phức tạp đầy đủ

### Binary Tree (tổng quát)

| Thao tác | Best | Average | Worst | Auxiliary Space | Ghi chú |
|----------|------|---------|-------|-----------------|---------|
| Traversal (any) | O(n) | O(n) | O(n) | O(h) recur / O(n) iter | h = height |
| Tìm node theo value | O(1) | O(n) | O(n) | O(h) | Phải duyệt toàn cây worst case |
| Insert | O(1) | O(1) | O(1) | O(1) | Khi có pointer tới vị trí |
| Compute height | O(n) | O(n) | O(n) | O(h) | Đệ quy hậu thứ tự |
| Level-order (BFS) | O(n) | O(n) | O(n) | O(w) | w = max width của cây |

### BST (chưa cân bằng)

| Thao tác | Best | Average | Worst | Auxiliary Space | Ghi chú |
|----------|------|---------|-------|-----------------|---------|
| Search | O(1) | O(log n) | O(n) | O(h) | Best: ở root; Worst: skewed |
| Insert | O(log n) | O(log n) | O(n) | O(h) | Worst khi cây skewed |
| Delete | O(log n) | O(log n) | O(n) | O(h) | Tìm successor là phần chính |
| Min/Max | O(log n) | O(log n) | O(n) | O(1) | Đi sát biên trái/phải |
| Inorder (sorted output) | O(n) | O(n) | O(n) | O(h) | |
| LCA | O(log n) | O(log n) | O(n) | O(h) | |
| Kth smallest | O(k) | O(k+h) | O(n) | O(h) | Inorder cắt sớm |

### Balanced BST (AVL / Red-Black)

| Thao tác | Best | Average | Worst | Auxiliary Space | Ghi chú |
|----------|------|---------|-------|-----------------|---------|
| Search | O(log n) | O(log n) | O(log n) | O(log n) | |
| Insert | O(log n) | O(log n) | O(log n) | O(log n) | AVL: ≤ 2 rotation; RB: ≤ 3 rotation |
| Delete | O(log n) | O(log n) | O(log n) | O(log n) | AVL: O(log n) rotation; RB: ≤ 3 |
| Min/Max | O(log n) | O(log n) | O(log n) | O(1) | |
| Range query | O(log n + k) | O(log n + k) | O(log n + k) | O(log n) | k = số node trong range |

### Space của cây n node

- Lưu chỉ data: O(n).
- Lưu pointer trái/phải: O(n) (mỗi node 2 con trỏ).
- Tổng: O(n).

---

## 5. Code mẫu

### Định nghĩa node

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Traversals (recursive)

```python
def preorder(root):
    if not root:
        return
    print(root.val)
    preorder(root.left)
    preorder(root.right)

def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.val)
    inorder(root.right)

def postorder(root):
    if not root:
        return
    postorder(root.left)
    postorder(root.right)
    print(root.val)
```

### Traversals (iterative với stack)

```python
def inorder_iter(root):
    stack = []
    curr = root
    result = []
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        result.append(curr.val)
        curr = curr.right
    return result
```

### Level-order (BFS)

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    q = deque([root])
    result = []
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        result.append(level)
    return result
```

### BST: Search, Insert, Delete

```python
def bst_search(root, key):
    if not root or root.val == key:
        return root
    if key < root.val:
        return bst_search(root.left, key)
    return bst_search(root.right, key)

def bst_insert(root, key):
    if not root:
        return TreeNode(key)
    if key < root.val:
        root.left = bst_insert(root.left, key)
    elif key > root.val:
        root.right = bst_insert(root.right, key)
    return root

def bst_delete(root, key):
    if not root:
        return None
    if key < root.val:
        root.left = bst_delete(root.left, key)
    elif key > root.val:
        root.right = bst_delete(root.right, key)
    else:
        # Tìm thấy node cần xóa
        if not root.left:
            return root.right
        if not root.right:
            return root.left
        # 2 con: tìm successor (min của subtree phải)
        succ = root.right
        while succ.left:
            succ = succ.left
        root.val = succ.val
        root.right = bst_delete(root.right, succ.val)
    return root
```

### Validate BST

```python
def is_bst(root, low=float('-inf'), high=float('inf')):
    if not root:
        return True
    if root.val <= low or root.val >= high:
        return False
    return (is_bst(root.left, low, root.val) and
            is_bst(root.right, root.val, high))
```

### Height và Diameter

```python
def height(root):
    if not root:
        return -1
    return 1 + max(height(root.left), height(root.right))

def diameter(root):
    """Đường đi dài nhất giữa 2 leaf bất kỳ (đếm theo cạnh)."""
    best = [0]
    def depth(node):
        if not node:
            return 0
        l = depth(node.left)
        r = depth(node.right)
        best[0] = max(best[0], l + r)
        return 1 + max(l, r)
    depth(root)
    return best[0]
```

### Lowest Common Ancestor

```python
def lca_bst(root, p, q):
    if not root:
        return None
    if p.val < root.val and q.val < root.val:
        return lca_bst(root.left, p, q)
    if p.val > root.val and q.val > root.val:
        return lca_bst(root.right, p, q)
    return root  # tách 2 phía → LCA

def lca_binary_tree(root, p, q):
    if not root or root is p or root is q:
        return root
    l = lca_binary_tree(root.left, p, q)
    r = lca_binary_tree(root.right, p, q)
    if l and r:
        return root
    return l or r
```

### Kth smallest trong BST

```python
def kth_smallest(root, k):
    stack = []
    while stack or root:
        while root:
            stack.append(root)
            root = root.left
        root = stack.pop()
        k -= 1
        if k == 0:
            return root.val
        root = root.right
    return None
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng binary tree khi:**
- Dữ liệu có cấu trúc phân cấp tự nhiên (DOM, expression tree, file system, decision tree).
- Cần biểu diễn quan hệ cha-con (organizational chart).
- Là cấu phần của thuật toán khác (Huffman coding, syntax tree).

**Dùng BST khi:**
- Cần insert/delete/search động với tần suất cao trên dữ liệu sortable.
- Cần in-order traversal cho dữ liệu sorted.
- Cần range query, kth smallest/largest.
- Cần ordered set/map (`std::map`, `TreeMap`).

**Không dùng BST khi:**
- Dữ liệu cố định, không insert/delete sau khi build → mảng sorted + binary search đơn giản hơn.
- Không cần thứ tự, chỉ cần lookup → HashMap O(1) thắng O(log n).
- Workload write rất nặng và không cần ordered iteration → HashMap hoặc Skip List.
- Cần worst-case O(log n) bảo đảm → dùng BST cân bằng (AVL, Red-Black), không dùng BST naive.

**Dùng heap (không BST) khi:**
- Chỉ cần min/max thường xuyên, không cần ordered traversal.
- Insert + extract-min/max là 2 thao tác chính.

---

## 7. So sánh với các cấu trúc liên quan

| | BST cơ bản | AVL | Red-Black | Heap | Hash Map | Skip List |
|-|-----------|-----|-----------|------|----------|-----------|
| Search | O(n) worst | O(log n) | O(log n) | O(n) | O(1) avg | O(log n) expected |
| Insert | O(n) worst | O(log n) | O(log n) | O(log n) | O(1) avg | O(log n) expected |
| Delete | O(n) worst | O(log n) | O(log n) | O(log n) | O(1) avg | O(log n) expected |
| Min/Max | O(log n) hoặc O(n) | O(log n) | O(log n) | O(1) min hoặc max | O(n) | O(1) min |
| Ordered traversal | Có | Có | Có | Không | Không | Có |
| Range query | Có | Có | Có | Không | Không | Có |
| Cân bằng tự động | Không | Strict | Loose | N/A | N/A | Probabilistic |
| Use case điển hình | Học thuật | Read-heavy ordered | Write-heavy ordered | Priority queue | Lookup nhanh | Redis sorted set |

**BST vs Hash Map**: BST O(log n), nhưng ordered + range query. Hash Map O(1) avg nhưng không ordered. Nếu chỉ cần lookup → Hash Map. Nếu cần "tất cả số trong khoảng [a, b]" → BST.

**Heap vs BST**: Heap chỉ đảm bảo root là min/max, không sorted toàn cục. Insert O(log n), extract-min O(log n), nhưng search bất kỳ O(n). BST cho phép search nhanh O(log n).

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **BST validate sai chỉ dựa vào parent**: kiểm tra `node.left.val < node.val and node.right.val > node.val` cho mỗi node không đủ. Phải truyền bounds (low, high) đệ quy xuống. Ví dụ:
   ```
       10
      /  \
     5    15
         /  \
        6    20
   ```
   Node 6 < 15 (parent), nhưng < 10 → vi phạm BST.

2. **Delete node có 2 con không tìm successor**: gây mất subtree.

3. **Insert duplicate**: cây không có quy ước rõ → có thể vô hạn loop hoặc tạo cây sai. Quyết định trước: bỏ qua duplicate, hoặc đếm số lượng, hoặc cho đi sang phải.

4. **Recursive depth lớn**: cây skewed n = 10000 → stack overflow Python. Dùng iterative hoặc tăng recursion limit.

5. **Inorder của BST không sorted** → cây không phải BST hợp lệ. Đây là cách kiểm tra BST đơn giản và đáng tin.

6. **LCA viết cho cây nhị phân áp vào BST không tận dụng property** — chậm hơn cần thiết (O(n) thay vì O(h)).

7. **Tính height nhầm depth**: height đếm từ node tới leaf xa nhất; depth đếm từ root tới node.

8. **Modify cây khi đang traverse** (đặc biệt iterative với stack) → state cũ trong stack không đồng bộ.

9. **Mistake `node.left` vs `node.right` trong delete**: hai trường hợp đối xứng, dễ copy-paste sai.

10. **Quên `return root` trong recursive insert/delete**: cây không được cập nhật, mất nhánh.

---

## 9. Câu hỏi phỏng vấn hay gặp

- Sự khác biệt giữa binary tree, BST, balanced BST, complete tree, full tree?
- Inorder traversal của BST cho ra gì? Vì sao?
- Viết hàm validate BST đúng (truyền bounds).
- 3 case xóa node trong BST. Vì sao chọn inorder successor (mà không lấy random)?
- LCA trên BST vs trên cây nhị phân — khác biệt code và complexity.
- Tại sao BST naive có worst-case O(n)? Cách khắc phục? (Self-balancing: AVL, RB.)
- AVL vs Red-Black — trade-off?
- Kth smallest trong BST — phương án inorder cắt sớm O(h + k).
- Serialize và Deserialize binary tree — phương án preorder + sentinel.
- Convert BST sang sorted doubly linked list in-place.
- Recover BST khi 2 node bị swap — phương án inorder + tìm 2 anomaly.
- Tính diameter của binary tree.
- Symmetric tree, Mirror tree.
- Maximum path sum (đường đi giá trị lớn nhất giữa 2 node bất kỳ).
- Convert sorted array thành balanced BST.
- Why heap không dùng BST (heap đơn giản hơn, locality tốt hơn nhờ array layout).
