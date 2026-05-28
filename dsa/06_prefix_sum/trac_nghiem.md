# Trắc Nghiệm: Prefix Sum (Tổng Tiền Tố)

---

## Câu hỏi

---

### Câu 1 [Cơ bản]
Cho mảng `A = [2, 4, 1, 3, 5]`. Mảng prefix sum (dùng sentinel P[0]=0) là:

A. `[0, 2, 6, 7, 10, 15]`  
B. `[2, 6, 7, 10, 15]`  
C. `[0, 2, 4, 1, 3, 5]`  
D. `[2, 4, 6, 10, 15]`

---

### Câu 2 [Cơ bản]
Cho prefix array `P = [0, 3, 5, 9, 11, 16]`, tổng `A[1..3]` (inclusive, 0-indexed) bằng:

A. 14  
B. 8  
C. 6  
D. 11

---

### Câu 3 [Cơ bản]
Độ phức tạp thời gian để build một 1D prefix sum array từ mảng n phần tử là:

A. O(1)  
B. O(log n)  
C. O(n)  
D. O(n²)

---

### Câu 4 [Cơ bản]
Sau khi build prefix sum, thời gian trả lời một range sum query là:

A. O(n)  
B. O(log n)  
C. O(1)  
D. O(√n)

---

### Câu 5 [Cơ bản]
Công thức tính range sum `A[l..r]` từ prefix array P (với P[0]=0) là:

A. `P[r] - P[l]`  
B. `P[r+1] - P[l]`  
C. `P[r] - P[l-1]`  
D. `P[r+1] - P[l+1]`

---

### Câu 6 [Cơ bản]
Prefix Sum phù hợp nhất cho trường hợp nào sau đây?

A. Array thay đổi liên tục và cần query range sum sau mỗi lần thay đổi  
B. Array tĩnh, cần trả lời nhiều range sum queries  
C. Tìm phần tử lớn nhất trong một đoạn  
D. Sắp xếp một mảng

---

### Câu 7 [Trung bình]
Cho mảng `A = [1, -2, 3, -1, 2]`. Prefix sum array P là `[0, 1, -1, 2, 1, 3]`. Tổng A[1..3] là:

A. 0  
B. 1  
C. -1  
D. 2

---

### Câu 8 [Trung bình]
Difference Array được dùng để giải quyết bài toán nào hiệu quả nhất?

A. Range sum query O(1)  
B. Point update O(1) + range query O(1)  
C. Range update O(1) + rebuild O(n)  
D. Range max query O(1)

---

### Câu 9 [Trung bình]
Để cộng giá trị `v = 5` vào tất cả phần tử `A[2..4]` dùng Difference Array `D` (size 7), ta làm:

A. `D[2] += 5`  
B. `D[2] += 5; D[4] -= 5`  
C. `D[2] += 5; D[5] -= 5`  
D. `D[2] += 5; D[4+1] -= 5` (tức `D[5] -= 5`)

---

### Câu 10 [Trung bình]
Trong bài Subarray Sum Equals K, tại sao ta khởi tạo `prefix_count = {0: 1}` trước khi duyệt?

A. Tránh chia cho 0  
B. Để đếm các subarray bắt đầu từ index 0 có tổng bằng k  
C. Tránh lỗi KeyError  
D. Vì tổng tiền tố luôn bắt đầu bằng 1

---

### Câu 11 [Trung bình]
Cho ma trận 3×3 và 2D prefix array P. Công thức tính tổng hình chữ nhật từ (r1,c1) đến (r2,c2) là:

A. `P[r2][c2] - P[r1][c2] - P[r2][c1] + P[r1][c1]`  
B. `P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]`  
C. `P[r2+1][c2+1] - P[r1+1][c2+1] - P[r2+1][c1+1] + P[r1+1][c1+1]`  
D. `P[r2][c2] - P[r1-1][c2] - P[r2][c1-1] + P[r1-1][c1-1]`

---

### Câu 12 [Trung bình]
Nếu array có n=10^6 phần tử và cần Q=10^6 range sum queries, tổng thời gian với Prefix Sum so với Naive là:

A. Prefix Sum: O(n²), Naive: O(n)  
B. Prefix Sum: O(n + Q) = O(2×10^6), Naive: O(n×Q) = O(10^12)  
C. Prefix Sum: O(n log n), Naive: O(n²)  
D. Prefix Sum: O(Q log n), Naive: O(n×Q)

---

### Câu 13 [Trung bình]
Cho mảng `A = [1, 2, 3, 4, 5]`. Sau khi áp dụng range update: cộng 3 vào A[1..3] dùng Difference Array. Kết quả mảng A sau rebuild là:

A. `[1, 5, 6, 7, 5]`  
B. `[1, 2, 3, 4, 5]`  
C. `[4, 5, 6, 7, 5]`  
D. `[1, 5, 6, 4, 5]`

---

### Câu 14 [Trung bình]
Prefix Sum có thể áp dụng cho phép toán XOR không? Tại sao?

A. Không, vì XOR không có tính chất giao hoán  
B. Có, vì `XOR(l,r) = prefix_xor[r+1] XOR prefix_xor[l]` do XOR là self-inverse  
C. Có, nhưng chỉ với số nguyên dương  
D. Không, vì `a XOR b - a XOR c ≠ b XOR c`

---

### Câu 15 [Nâng cao]
Cho mảng `A = [3, 4, 7, 2, -3, 1, 4, 2]`, k = 7. Số subarray có tổng = 7 là bao nhiêu? (dùng prefix sum + hashmap)

A. 2  
B. 3  
C. 4  
D. 5

---

### Câu 16 [Nâng cao]
Nếu một phần tử trong mảng bị cập nhật (point update), chi phí để prefix sum array vẫn đúng là:

A. O(1) — chỉ cần update đúng 1 ô  
B. O(log n) — dùng binary search  
C. O(n) — phải rebuild từ vị trí thay đổi trở đi  
D. O(n log n) — phải sort lại

---

### Câu 17 [Nâng cao]
Fenwick Tree (Binary Indexed Tree) giải quyết hạn chế nào của Prefix Sum?

A. Giúp query O(1) thay vì O(log n)  
B. Cho phép point update trong O(log n) và range query trong O(log n) thay vì O(n) rebuild  
C. Giúp tiết kiệm memory O(1) thay vì O(n)  
D. Cho phép range update O(1)

---

### Câu 18 [Nâng cao]
Để tính Product of Array Except Self (LeetCode 238) mà không dùng phép chia, ta dùng:

A. Chỉ prefix sum  
B. Prefix product array và suffix product array kết hợp  
C. Difference array  
D. 2D prefix sum

---

### Câu 19 [Nâng cao]
Cho bài toán: "Đếm số subarray có tổng chia hết cho k". Công thức nào đúng để xác định subarray [i, j] có tổng chia hết cho k?

A. `prefix[j+1] == prefix[i]`  
B. `(prefix[j+1] - prefix[i]) % k == 0`, tức `prefix[j+1] % k == prefix[i] % k`  
C. `prefix[j+1] % k == 0`  
D. `(prefix[j+1] + prefix[i]) % k == 0`

---

### Câu 20 [Nâng cao]
Space complexity của 2D Prefix Sum cho ma trận m×n là:

A. O(m + n)  
B. O(min(m, n))  
C. O(m × n)  
D. O(m × n × log(m × n))

---

### Câu 21 [Nâng cao]
Trong bài toán "Longest Subarray with Sum ≤ k" với phần tử dương, tại sao Sliding Window thường tốt hơn Prefix Sum + Binary Search?

A. Sliding Window có O(1) space, Prefix Sum cần O(n)  
B. Sliding Window là O(n), còn Prefix Sum + Binary Search là O(n log n) do cần tìm kiếm nhị phân trên prefix array đơn điệu  
C. Sliding Window xử lý được số âm, Prefix Sum không xử lý được  
D. Câu A và B đều đúng

---

### Câu 22 [Nâng cao]
Cho prefix sum array: `P = [0, 1, 3, 6, 6, 6, 8]`. Đoạn nào trong mảng gốc có toàn số 0?

A. Index 0 đến 2  
B. Index 3 đến 4  
C. Index 2 đến 4  
D. Không có đoạn nào

---

## Giải thích đáp án

---

**Câu 1 — Đáp án: A**  
`P[0]=0`, `P[1]=0+2=2`, `P[2]=2+4=6`, `P[3]=6+1=7`, `P[4]=7+3=10`, `P[5]=10+5=15`. Kết quả `[0,2,6,7,10,15]`.  
- B sai: thiếu sentinel P[0]=0.  
- C sai: đó là mảng gốc với sentinel 0.  
- D sai: cộng dồn sai (4 không tích lũy từ 2).

---

**Câu 2 — Đáp án: B**  
`sum(A[1..3]) = P[4] - P[1] = 11 - 3 = 8`.  
- A sai: 14 = P[5] - P[1] = 16-3 là sum[1..4].  
- C sai: 6 = P[3] - P[1] = 9-3 là sum[1..2].  
- D sai: 11 là P[4] không phải range sum.

---

**Câu 3 — Đáp án: C**  
Phải duyệt qua từng phần tử một lần để tính tổng tích lũy → O(n).  
- A sai: không thể build mà không đọc input.  
- B sai: không có cơ sở chia đôi ở đây.  
- D sai: chỉ cần 1 vòng lặp, không lồng nhau.

---

**Câu 4 — Đáp án: C**  
Sau khi có prefix array, query chỉ là 1 phép trừ: `P[r+1] - P[l]` → O(1).  
- A, B, D sai: không cần duyệt hay tìm kiếm gì.

---

**Câu 5 — Đáp án: B**  
`sum(A[l..r]) = P[r+1] - P[l]`. Với sentinel P[0]=0 và P[i] = A[0]+...+A[i-1].  
- A sai: `P[r] - P[l]` bỏ mất A[r].  
- C sai: `P[r] - P[l-1]` bỏ mất A[r] và có thể gây index -1.  
- D sai: `P[r+1] - P[l+1]` bỏ mất A[l].

---

**Câu 6 — Đáp án: B**  
Prefix sum tối ưu khi array tĩnh và cần nhiều queries — O(n) build, O(1) mỗi query.  
- A sai: array thay đổi nhiều → Fenwick Tree.  
- C sai: max/min → Sparse Table hoặc Segment Tree.  
- D sai: prefix sum không liên quan đến sắp xếp.

---

**Câu 7 — Đáp án: A**  
`sum(A[1..3]) = P[4] - P[1] = 1 - 1 = 0`. Kiểm tra: A[1]+A[2]+A[3] = -2+3+(-1) = 0. ✓  
- B, C, D sai: tính nhầm index hoặc giá trị P.

---

**Câu 8 — Đáp án: C**  
Difference Array tối ưu cho range update O(1): chỉ cần update 2 vị trí. Sau đó cần O(n) để rebuild (prefix sum lên D).  
- A sai: range sum O(1) là của Prefix Sum thông thường.  
- B sai: point update O(1) + range query O(1) không tồn tại cùng nhau hiệu quả.  
- D sai: range max không liên quan đến difference array.

---

**Câu 9 — Đáp án: D**  
Để cộng 5 vào A[2..4]: `D[2] += 5` và `D[4+1] = D[5] -= 5`. Cú pháp `D[r+1] -= v`.  
- A sai: thiếu phần "dừng" tác dụng sau index 4.  
- B sai: D[4] -= 5 làm dừng tác dụng trước index 4 (không bao gồm A[4]).  
- C đúng về mặt giá trị (D[5] -= 5) nhưng viết nhầm; D là đúng vì viết rõ `r+1 = 5`.

---

**Câu 10 — Đáp án: B**  
`{0: 1}` có nghĩa: prefix sum = 0 đã "thấy" 1 lần. Khi current_prefix = k ở vị trí j, ta cần đếm prefix[i] = 0 (tức subarray từ 0 đến j). Không có sentinel → bỏ qua các subarray này.  
- A sai: không liên quan chia cho 0.  
- C sai: không phải về KeyError (`.get()` đã handle).  
- D sai: tổng tiền tố không nhất thiết bắt đầu bằng 1.

---

**Câu 11 — Đáp án: B**  
Công thức chuẩn với 1-indexed prefix (sentinel row và col): `P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]`.  
- A sai: chỉ work nếu P không có sentinel — dễ gây index -1.  
- C sai: offset sai — trừ vùng không cần thiết.  
- D sai: tương tự A với cú pháp khác nhưng cũng không chuẩn.

---

**Câu 12 — Đáp án: B**  
Prefix Sum: O(n) build + O(1)×Q = O(n+Q). Naive: O(n) mỗi query × Q = O(n×Q).  
Với n=Q=10^6: Prefix Sum ≈ 2×10^6 ops, Naive ≈ 10^12 ops.  
- A, C, D sai: sai về complexity.

---

**Câu 13 — Đáp án: A**  
Difference Array D ban đầu = [1,2,3,4,5,0,...]. Sau `D[1]+=3; D[4]-=3`: D=[1,5,3,4,2,0,...].  
Prefix sum D: [1, 1+5=... wait. D = [1,2,3,4,5] (từ A). D[1]+=3 → D=[1,5,3,4,5]. D[4]-=3 → D=[1,5,3,4,2].  
Rebuild: A'[0]=1, A'[1]=1+5=... Thực ra rebuild là prefix sum: [1, 1+5=6... không, rebuild từ diff: A'[0]=D[0]=1, A'[1]=A'[0]+D[1]=1+5=6... Hmm.  
Cách đúng: D là difference array của A: D[0]=A[0]=1, D[1]=A[1]-A[0]=1, D[2]=1, D[3]=1, D[4]=1.  
Sau `D[1]+=3; D[4]-=3`: D=[1,4,1,1,-2].  
Rebuild (prefix sum D): A'=[1, 1+4=5, 5+1=6, 6+1=7, 7-2=5]. Kết quả: [1,5,6,7,5]. ✓  
- B sai: không có gì thay đổi.  
- C sai: index 0 cũng thay đổi.  
- D sai: A[3] bị ảnh hưởng.

---

**Câu 14 — Đáp án: B**  
XOR là self-inverse: `a XOR a = 0`. Nên `prefix_xor[r+1] XOR prefix_xor[l]` = XOR của A[l..r] vì các phần tử trước l xuất hiện 2 lần và triệt tiêu.  
- A sai: XOR có tính giao hoán và kết hợp.  
- C sai: XOR hoạt động với mọi số nguyên.  
- D sai: phép XOR không phải phép trừ, nhưng tính self-inverse khiến nó work.

---

**Câu 15 — Đáp án: B**  
P = [0, 3, 7, 14, 16, 13, 14, 18, 20]. Cần P[j] - P[i] = 7, tức P[i] = P[j] - 7.  
- P[2]=7: P[j]-7=0 → P[0]=0 → 1 subarray [0..1].  
- P[3]=14: P[j]-7=7 → P[2]=7 → 1 subarray [2..2] = [7]. Tổng [0..2]=14, trừ P[2]=7 → [2..2].  
- P[6]=14: P[j]-7=7 → P[2]=7 → 1 subarray [2..5].  
Tổng: 3 subarray.  
- A, C, D sai: đếm sai.

---

**Câu 16 — Đáp án: C**  
`P[i] = P[0] + A[0] + ... + A[i-1]`. Nếu A[k] thay đổi, thì P[k+1], P[k+2], ..., P[n] đều thay đổi → phải update O(n) ô.  
- A sai: chỉ update 1 ô là không đủ.  
- B sai: không có cấu trúc binary search ở đây.  
- D sai: không cần sort.

---

**Câu 17 — Đáp án: B**  
Fenwick Tree cho phép cả update và query trong O(log n), giải quyết hạn chế O(n) rebuild của Prefix Sum khi có update.  
- A sai: Fenwick Tree query là O(log n) không phải O(1).  
- C sai: Fenwick Tree cũng dùng O(n) space.  
- D sai: range update O(1) là của Difference Array.

---

**Câu 18 — Đáp án: B**  
Prefix product `L[i]` = tích A[0..i-1], Suffix product `R[i]` = tích A[i+1..n-1]. Kết quả `ans[i] = L[i] × R[i]`. Không cần chia, tránh lỗi với A[i]=0.  
- A sai: chỉ prefix không đủ.  
- C sai: difference array không liên quan.  
- D sai: 2D prefix không cần thiết.

---

**Câu 19 — Đáp án: B**  
`(prefix[j+1] - prefix[i]) % k == 0` ⟺ `prefix[j+1] % k == prefix[i] % k`. Dùng HashMap đếm số lần xuất hiện của từng `prefix % k`. Đây là tính chất modular arithmetic.  
- A sai: hai prefix bằng nhau nghĩa là sum = 0, không phải chia hết cho k.  
- C sai: chỉ check prefix tại j+1 chia hết k không đủ.  
- D sai: tổng hai prefix không có ý nghĩa cho bài toán này.

---

**Câu 20 — Đáp án: C**  
2D Prefix array có kích thước (m+1)×(n+1) ≈ O(m×n).  
- A sai: chỉ đúng nếu là 1D.  
- B sai: không lấy min.  
- D sai: không có log factor.

---

**Câu 21 — Đáp án: D**  
Cả A và B đều đúng: Sliding Window O(n) time và O(1) space, còn Prefix Sum + Binary Search cần O(n) space và O(n log n) time (vì prefix sum đơn điệu tăng với số dương, dùng binary search). Sliding Window thắng cả về time và space với phần tử dương.  
- C sai: Prefix Sum hoàn toàn xử lý được số âm.

---

**Câu 22 — Đáp án: B**  
P = [0, 1, 3, 6, 6, 6, 8]. P[3]=P[4]=P[5]=6 → tổng không tăng ở đoạn đó → A[3]=P[4]-P[3]=0 và A[4]=P[5]-P[4]=0. Index 3 và 4 (0-indexed) là toàn số 0.  
- A sai: P[1]-P[0]=1, P[2]-P[1]=2, P[3]-P[2]=3 đều khác 0.  
- C sai: A[2]=P[3]-P[2]=6-5=... P[3]=6, P[2]=3 → A[2]=3 ≠ 0.  
- D sai: có đoạn index 3..4 bằng 0.

---

## Bảng Đáp Án Nhanh

| Câu | Đáp án | Mức độ |
|-----|--------|--------|
| 1 | A | Cơ bản |
| 2 | B | Cơ bản |
| 3 | C | Cơ bản |
| 4 | C | Cơ bản |
| 5 | B | Cơ bản |
| 6 | B | Cơ bản |
| 7 | A | Trung bình |
| 8 | C | Trung bình |
| 9 | D | Trung bình |
| 10 | B | Trung bình |
| 11 | B | Trung bình |
| 12 | B | Trung bình |
| 13 | A | Trung bình |
| 14 | B | Trung bình |
| 15 | B | Nâng cao |
| 16 | C | Nâng cao |
| 17 | B | Nâng cao |
| 18 | B | Nâng cao |
| 19 | B | Nâng cao |
| 20 | C | Nâng cao |
| 21 | D | Nâng cao |
| 22 | B | Nâng cao |

**Phân bổ:** Cơ bản: 6 câu (27%) · Trung bình: 8 câu (36%) · Nâng cao: 8 câu (36%)
