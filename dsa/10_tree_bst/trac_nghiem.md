# Trắc nghiệm — Binary Tree & BST

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** "Cây nhị phân" (binary tree) nghĩa là:

- A. Cây có đúng 2 tầng
- B. Mỗi node có tối đa 2 con
- C. Mỗi node có đúng 2 con
- D. Cây có tổng node là số chẵn

> **Đáp án: B**  
> **Giải thích:** "Tối đa 2 con" là định nghĩa. Một node có thể có 0, 1 hoặc 2 con. "Đúng 2" mới là full binary tree (đặc biệt hơn).

---

**Câu 2:** Quy tắc BST property là:

- A. Mọi node trái < node, mọi node phải > node
- B. Mọi node trong subtree trái < node, mọi node trong subtree phải > node
- C. Subtree trái và phải có cùng số node
- D. Tổng các giá trị bên trái = tổng bên phải

> **Đáp án: B**  
> **Giải thích:** Định nghĩa BST nghiêm ngặt: toàn bộ subtree (kể cả các node sâu) phải tuân thủ, không chỉ con trực tiếp. Đây là nguồn của lỗi validate phổ biến (chọn A là sai).

---

**Câu 3:** Inorder traversal trên BST cho ra:

- A. Dãy bất kỳ
- B. Dãy theo thứ tự chèn
- C. Dãy sorted tăng dần
- D. Dãy sorted giảm dần

> **Đáp án: C**  
> **Giải thích:** Inorder = LNR (left, node, right). Trên BST, left < node < right ở mọi node → đệ quy cho ra sorted tăng dần. Đây là tính chất nhận diện BST.

---

**Câu 4:** Time complexity tìm kiếm trong BST cân bằng là:

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: B**  
> **Giải thích:** Cây cân bằng có height O(log n), search đi từ root xuống tối đa h bước → O(log n).

---

**Câu 5:** Worst case search trong BST chưa cân bằng (insert theo sorted order) là:

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: C**  
> **Giải thích:** Insert tăng dần → cây suy biến thành linked list, height = n-1, search O(n).

---

**Câu 6:** Node không có con nào trong cây gọi là:

- A. Root
- B. Internal node
- C. Leaf
- D. Branch

> **Đáp án: C**  
> **Giải thích:** Leaf = node có 0 con. Root = node trên cùng (không có cha). Internal = có ít nhất 1 con.

---

**Câu 7:** Cấu trúc nào sau đây CHẮC CHẮN không phải binary tree?

- A. Cây có 1 node duy nhất
- B. Cây rỗng
- C. Node có 3 con
- D. Cây bị nghiêng hoàn toàn về 1 bên

> **Đáp án: C**  
> **Giải thích:** Binary tree giới hạn ≤ 2 con/node. Cây rỗng, 1 node, cây skewed đều hợp lệ. C vi phạm định nghĩa.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Cây sau có phải BST không?
```
      10
     /  \
    5    15
        /  \
       6    20
```

- A. Có
- B. Không, vì 6 < 10 nhưng nằm ở subtree phải của 10
- C. Có, vì mọi node con trực tiếp đều thỏa BST property
- D. Có, nhưng không cân bằng

> **Đáp án: B**  
> **Giải thích:** Node 6 nằm trong subtree phải của 10 → phải > 10. Nhưng 6 < 10 → vi phạm. Đây là lỗi validate phổ biến nếu chỉ check parent-child.

---

**Câu 9:** Xóa node có 2 con trong BST, thường thay bằng:

- A. Node lớn nhất trong subtree trái (inorder predecessor) hoặc node nhỏ nhất trong subtree phải (inorder successor)
- B. Node root
- C. Node ngẫu nhiên
- D. Node sâu nhất

> **Đáp án: A**  
> **Giải thích:** Predecessor hoặc successor là 2 node "kề" trong inorder → thay xong vẫn giữ được BST property. Sau khi copy giá trị, xóa successor/predecessor (luôn rơi vào case ≤ 1 con — đệ quy đơn giản).

---

**Câu 10:** Cho cây BST gồm các giá trị `[5, 3, 7, 2, 4, 6, 8]` insert theo thứ tự đó. Inorder traversal cho ra?

- A. `[5, 3, 7, 2, 4, 6, 8]`
- B. `[2, 3, 4, 5, 6, 7, 8]`
- C. `[8, 7, 6, 5, 4, 3, 2]`
- D. `[5, 3, 2, 4, 7, 6, 8]`

> **Đáp án: B**  
> **Giải thích:** Inorder của BST = sorted tăng. Insert order không quan trọng cho output inorder (chỉ ảnh hưởng cấu trúc cây).

---

**Câu 11:** Level-order traversal dùng cấu trúc dữ liệu nào?

- A. Stack
- B. Queue
- C. Priority Queue
- D. Set

> **Đáp án: B**  
> **Giải thích:** BFS dùng queue FIFO để đảm bảo thăm hết tầng k trước khi sang tầng k+1.

---

**Câu 12:** Recursive traversal có auxiliary space complexity là?

- A. O(1)
- B. O(h) — h = height của cây
- C. O(n)
- D. O(n log n)

> **Đáp án: B**  
> **Giải thích:** Recursion stack sâu nhất bằng height. Cây cân bằng O(log n), cây skewed O(n). Đáp án chuẩn dạng O(h).

---

**Câu 13:** LCA (Lowest Common Ancestor) trên BST có complexity tốt nhất là?

- A. O(1)
- B. O(log n) cho BST cân bằng (tận dụng property), O(h) tổng quát
- C. O(n) bất kể cấu trúc
- D. O(n²)

> **Đáp án: B**  
> **Giải thích:** Trên BST: đi từ root, so sánh p và q với root.val. Nếu cả 2 nhỏ hơn → đi trái; cả 2 lớn hơn → đi phải; tách 2 phía → root là LCA. O(h). Cây cân bằng → O(log n). Trên binary tree tổng quát (không có thứ tự) phải O(n).

---

**Câu 14:** Số node tối đa trong binary tree height h là?

- A. h
- B. 2h
- C. 2^(h+1) - 1
- D. h²

> **Đáp án: C**  
> **Giải thích:** Mỗi tầng i (i từ 0 đến h) có tối đa 2^i node. Tổng = 1 + 2 + 4 + ... + 2^h = 2^(h+1) - 1. (Lưu ý quy ước height: cây 1 node có h=0.)

---

**Câu 15:** Validate BST đúng nhất bằng cách:

- A. Kiểm tra mỗi node so với 2 con trực tiếp
- B. Kiểm tra mỗi node nằm trong bounds (low, high) được truyền đệ quy từ trên xuống
- C. Đếm số node
- D. Kiểm tra height

> **Đáp án: B**  
> **Giải thích:** Cách B tương đương với "inorder phải sorted". Cách A là cách sai phổ biến (đã giải thích trong câu 8).

---

**Câu 16:** Phương án nào đảm bảo BST có worst-case O(log n) cho mọi thao tác?

- A. Insert dữ liệu theo thứ tự ngẫu nhiên
- B. Dùng self-balancing BST: AVL, Red-Black, Treap, Splay
- C. Sắp xếp lại cây sau mỗi insert
- D. Dùng array thay vì pointer

> **Đáp án: B**  
> **Giải thích:** Insert ngẫu nhiên cho expected O(log n) nhưng không guarantee. Self-balancing dùng rotation duy trì invariant cân bằng → guarantee O(log n).

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích đoạn code:

```python
def f(root, k):
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

Code này làm gì với BST?

- A. Tìm node sâu nhất
- B. Tìm kth smallest element
- C. Đếm số node
- D. Tìm LCA

> **Đáp án: B**  
> **Giải thích:** Đây là inorder iterative cắt sớm tại lần pop thứ k. Trên BST, inorder = sorted tăng → phần tử thứ k = kth smallest.

---

**Câu 18:** AVL Tree so với Red-Black Tree:

- A. AVL cân bằng nghiêm ngặt hơn (|height_diff| ≤ 1), search nhanh hơn nhưng insert/delete tốn nhiều rotation hơn
- B. Red-Black luôn nhanh hơn AVL
- C. AVL và Red-Black giống hệt nhau
- D. AVL chỉ dùng cho dữ liệu nhỏ

> **Đáp án: A**  
> **Giải thích:** AVL cân bằng tight → cây thấp hơn ~ log₂(n), search nhanh. Red-Black tolerant hơn → ít rotation cho insert/delete. AVL ưu cho read-heavy, RB ưu cho write-heavy. Đó là lý do `std::map`, `TreeMap` chọn RB.

---

**Câu 19:** Morris Inorder Traversal đặc biệt vì:

- A. O(n) time, O(1) space (không dùng stack/recursion)
- B. O(log n) time
- C. Chỉ duyệt được cây cân bằng
- D. Cần dùng heap

> **Đáp án: A**  
> **Giải thích:** Morris dùng "threading" — tạm thời gắn predecessor.right = current, đi xuống xong khôi phục. Mỗi cạnh được visit tối đa 3 lần → O(n) time. Không stack → O(1) auxiliary space (không tính output).

---

**Câu 20:** Cho cây nhị phân (không phải BST). Maximum Path Sum (đường đi giá trị lớn nhất giữa 2 node bất kỳ, có thể đi qua root hoặc không) tối ưu bằng:

- A. BFS từ mỗi node, O(n²)
- B. DFS đệ quy hậu thứ tự: với mỗi node tính max gain từ trái và phải, cập nhật best = node.val + l_gain + r_gain. Trả về node.val + max(l_gain, r_gain) cho cha. O(n)
- C. Floyd-Warshall, O(n³)
- D. Dijkstra, O(n log n)

> **Đáp án: B**  
> **Giải thích:** Mỗi node là "đỉnh" tiềm năng của path (path đi xuyên qua nó với 2 nhánh con). Tính max gain (≥ 0) từ trái, phải; cập nhật answer; trả về cho cha chỉ 1 nhánh (vì đường đi qua cha chỉ chọn 1 trong 2 nhánh con của node hiện tại). O(n) classic.

---

**Câu 21:** Convert sorted array `[1, 2, 3, 4, 5, 6, 7]` thành balanced BST. Sau khi build, root sẽ là?

- A. 1
- B. 4
- C. 7
- D. Bất kỳ

> **Đáp án: B**  
> **Giải thích:** Pattern divide-and-conquer: lấy phần tử giữa làm root, đệ quy build subtree trái từ nửa đầu, subtree phải từ nửa sau. Với mảng 7 phần tử, giữa = index 3 = giá trị 4. Đảm bảo cây cân bằng O(log n) height.

---

**Câu 22:** Recover BST khi 2 node đã bị swap (cây gần như BST trừ 2 node sai vị trí). Cách O(n) time, O(1) space (không tính stack inorder):

- A. Sort toàn bộ rồi build lại
- B. Inorder traversal, tìm 2 anomaly (vị trí mà prev > curr), swap chúng. Morris inorder cho O(1) extra
- C. Brute force thử mọi cặp swap
- D. Bài này không giải được

> **Đáp án: B**  
> **Giải thích:** Inorder của BST đúng phải sorted. Khi swap 2 node, có 2 vị trí vi phạm thứ tự (hoặc 1 nếu 2 node kề nhau). Bắt 2 node tại các anomaly đó, swap value → BST đúng lại. Morris cho O(1) extra space. Đây là LeetCode classic.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | B      | 13  | B      |
| 3   | C      | 14  | C      |
| 4   | B      | 15  | B      |
| 5   | C      | 16  | B      |
| 6   | C      | 17  | B      |
| 7   | C      | 18  | A      |
| 8   | B      | 19  | A      |
| 9   | A      | 20  | B      |
| 10  | B      | 21  | B      |
| 11  | B      | 22  | B      |
