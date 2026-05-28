# Trắc nghiệm — Greedy Algorithm (Thuật toán Tham lam)

> **Tổng số câu:** 20  
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)  
> Mỗi câu có **4 đáp án** (A/B/C/D), ghi rõ đáp án đúng và **giải thích tại sao**.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Thuật toán Greedy hoạt động theo nguyên tắc nào?

- A. Thử tất cả các tổ hợp và chọn tổ hợp tốt nhất
- B. Tại mỗi bước, chọn lựa chọn tối ưu cục bộ mà không xem xét lại
- C. Chia bài toán thành các bài con và lưu kết quả trung gian
- D. Dùng đệ quy để duyệt toàn bộ không gian tìm kiếm

> **Đáp án: B**  
> **Giải thích:** Greedy luôn chọn "locally optimal choice" tại mỗi bước và không bao giờ quay lui. Đây là điểm phân biệt với Backtracking (A/D) và Dynamic Programming (C).

---

**Câu 2:** Điều kiện nào bắt buộc phải thỏa mãn để Greedy cho nghiệm tối ưu toàn cục?

- A. Bài toán có thể chia thành các bài con độc lập
- B. Bài toán có Greedy Choice Property và Optimal Substructure
- C. Bài toán có thể giải bằng đệ quy
- D. Input phải được sắp xếp trước

> **Đáp án: B**  
> **Giải thích:** Hai điều kiện cần thiết: (1) Greedy Choice Property — lựa chọn tham lam luôn là một phần của nghiệm tối ưu; (2) Optimal Substructure — nghiệm tối ưu chứa nghiệm tối ưu của bài con. Thiếu một trong hai → Greedy có thể sai.

---

**Câu 3:** Với bài toán Activity Selection, tiêu chí sắp xếp đúng để áp dụng Greedy là gì?

- A. Sắp xếp theo thời gian bắt đầu tăng dần
- B. Sắp xếp theo thời lượng (duration) tăng dần
- C. Sắp xếp theo thời gian kết thúc tăng dần
- D. Sắp xếp theo thời gian kết thúc giảm dần

> **Đáp án: C**  
> **Giải thích:** Chọn hoạt động kết thúc sớm nhất giúp "giải phóng" timeline sớm nhất, cho phép chọn được nhiều hoạt động tiếp theo hơn. Sort theo start (A) hoặc duration (B) đều cho kết quả sai trong trường hợp tổng quát.

---

**Câu 4:** Cho coins = [1, 5, 10, 25] và amount = 41. Greedy (chọn đồng lớn nhất trước) trả về bao nhiêu đồng?

- A. 4
- B. 5
- C. 3
- D. 6

> **Đáp án: A**  
> **Giải thích:** 25 + 10 + 5 + 1 = 41, dùng 4 đồng. Với hệ US coins (1, 5, 10, 25), Greedy luôn cho kết quả đúng vì mỗi đồng là bội số của đồng nhỏ hơn.

---

**Câu 5:** Greedy Choice Property có nghĩa là gì?

- A. Luôn có thể giải bài toán bằng đệ quy
- B. Lựa chọn tham lam tại mỗi bước là một phần của nghiệm tối ưu
- C. Bài toán có thể chia đôi như Binary Search
- D. Cần lưu tất cả trạng thái trung gian

> **Đáp án: B**  
> **Giải thích:** Greedy Choice Property đảm bảo rằng ta không bao giờ "bỏ lỡ" nghiệm tối ưu khi chọn tham lam. Không cần xét lại vì lựa chọn hiện tại luôn có thể đưa đến nghiệm tốt nhất.

---

**Câu 6:** Đâu là đặc điểm phân biệt chính giữa Greedy và Dynamic Programming?

- A. DP nhanh hơn Greedy
- B. Greedy lưu kết quả trung gian, DP không lưu
- C. Greedy không xét lại quyết định, DP xét tất cả các khả năng
- D. Greedy chỉ dùng được với mảng đã sắp xếp

> **Đáp án: C**  
> **Giải thích:** Greedy ra quyết định một lần duy nhất tại mỗi bước (irrevocable choice). DP lưu memo và xét tất cả bài con để đảm bảo tối ưu toàn cục. DP thường chậm hơn nhưng đúng với nhiều bài hơn.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Tại sao Greedy KHÔNG cho nghiệm đúng với bài Coin Change khi coins = [1, 3, 4] và amount = 6?

- A. Greedy chọn 4 + 1 + 1 = 3 đồng, nhưng tối ưu là 3 + 3 = 2 đồng
- B. Greedy không thể giải bài Coin Change
- C. Amount = 6 quá nhỏ để Greedy hoạt động
- D. Cần sort coins theo thứ tự tăng dần

> **Đáp án: A**  
> **Giải thích:** Greedy chọn 4 (lớn nhất ≤ 6), còn 2 → chọn 1+1 → tổng 3 đồng. Nhưng 3+3 = 6 chỉ cần 2 đồng. Đây là counterexample kinh điển chứng minh Greedy không đúng tổng quát với Coin Change → phải dùng DP.

---

**Câu 8:** Độ phức tạp thời gian của bài Activity Selection Problem là bao nhiêu?

- A. O(n)
- B. O(n log n)
- C. O(n²)
- D. O(2ⁿ)

> **Đáp án: B**  
> **Giải thích:** Bước sort mất O(n log n), bước duyệt chọn hoạt động mất O(n). Tổng là O(n log n) do bottleneck là bước sort. Sau khi sort, toàn bộ xử lý chỉ cần một lần duyệt tuyến tính.

---

**Câu 9:** Với Jump Game (LeetCode 55), nums = [3, 2, 1, 0, 4]. Kết quả là gì và tại sao?

- A. True — luôn có thể đến cuối
- B. False — vì nums[3] = 0 tạo ra "tường chắn" không vượt qua được
- C. False — vì mảng có độ dài 5 quá ngắn
- D. True — vì nums[0] = 3 nhảy được 3 bước

> **Đáp án: B**  
> **Giải thích:** Từ index 0 (giá trị 3), max_reach = 3. Từ index 1 (giá trị 2), max_reach = max(3, 1+2) = 3. Từ index 2 (giá trị 1), max_reach = 3. Từ index 3 (giá trị 0), max_reach = 3. Index 4 > max_reach (3) → không thể đến → False.

---

**Câu 10:** Cho intervals = [[1,3],[2,6],[8,10],[15,18]]. Sau khi Merge Intervals, kết quả là gì?

- A. [[1,6],[8,10],[15,18]]
- B. [[1,3],[2,6],[8,10],[15,18]]
- C. [[1,10],[15,18]]
- D. [[1,18]]

> **Đáp án: A**  
> **Giải thích:** Sort theo start: đã sort. [1,3] và [2,6]: 2 ≤ 3 → merge thành [1,6]. [1,6] và [8,10]: 8 > 6 → không merge. [8,10] và [15,18]: 15 > 10 → không merge. Kết quả: [[1,6],[8,10],[15,18]].

---

**Câu 11:** Fractional Knapsack khác 0/1 Knapsack ở điểm gì, và tại sao Greedy đúng với Fractional?

- A. Fractional cho phép lấy phần nhỏ của vật; Greedy đúng vì có thể luôn tận dụng hết capacity
- B. Fractional chỉ có một vật; Greedy luôn đúng
- C. 0/1 cho phép lấy nhiều vật giống nhau; Fractional thì không
- D. Không có sự khác biệt, cả hai đều giải được bằng Greedy

> **Đáp án: A**  
> **Giải thích:** Fractional: mỗi vật có thể lấy một phần (0 đến weight). Greedy (sort theo value/weight desc) đúng vì nếu ta bỏ qua vật có tỉ lệ cao để lấy vật khác, ta chỉ có thể giảm tổng value. 0/1: không chia nhỏ → quyết định lấy/không lấy phụ thuộc vào sự kết hợp → phải dùng DP.

---

**Câu 12:** Thuật toán Dijkstra là ví dụ của Greedy vì lý do gì?

- A. Luôn chọn cạnh có trọng số nhỏ nhất trong toàn đồ thị
- B. Tại mỗi bước, chọn đỉnh chưa xử lý có khoảng cách nhỏ nhất (locally optimal)
- C. Sắp xếp tất cả cạnh theo trọng số trước khi xử lý
- D. Thử tất cả đường đi và chọn ngắn nhất

> **Đáp án: B**  
> **Giải thích:** Dijkstra duy trì tập các đỉnh đã xử lý. Tại mỗi bước, greedy chọn đỉnh có dist[] nhỏ nhất trong tập chưa xử lý. Điều này đúng với non-negative weights vì đỉnh đã chọn không thể có đường ngắn hơn qua đỉnh chưa xử lý.

---

**Câu 13:** Tại sao Dijkstra không hoạt động với cạnh có trọng số âm?

- A. Không thể sort cạnh âm
- B. Greedy choice không còn đúng: đỉnh đã "chốt" có thể được cập nhật lại qua cạnh âm
- C. Min-heap không hỗ trợ số âm
- D. Vì thuật toán sẽ lặp vô tận

> **Đáp án: B**  
> **Giải thích:** Khi Dijkstra "chốt" một đỉnh, giả định rằng đường ngắn nhất đến đỉnh đó đã được tìm ra. Với cạnh âm, đường đi qua cạnh âm sau đó có thể tạo ra đường ngắn hơn → vi phạm Greedy Choice Property. Bellman-Ford xử lý được trường hợp này.

---

**Câu 14:** Cho bài Non-overlapping Intervals (LeetCode 435): xóa ít interval nhất để không còn overlap. Chiến lược Greedy nào đúng?

- A. Xóa interval có thời lượng dài nhất trước
- B. Xóa interval có thời gian bắt đầu sớm nhất trước
- C. Giữ lại interval kết thúc sớm nhất (tương đương Activity Selection)
- D. Sort theo start, giữ lại interval đầu tiên

> **Đáp án: C**  
> **Giải thích:** Bài này tương đương Activity Selection: tối đa hóa số interval giữ lại = tối thiểu hóa số interval xóa. Chiến lược: sort theo end, dùng greedy giữ interval kết thúc sớm nhất khi không overlap. Số cần xóa = n - (số interval giữ được).

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Phân tích đoạn code sau. Kết quả trả về là gì với input nums = [2,3,1,1,4]?

```python
def jump(nums):
    jumps = 0
    current_end = 0
    farthest = 0
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == current_end:
            jumps += 1
            current_end = farthest
    return jumps
```

- A. 1
- B. 2
- C. 3
- D. 4

> **Đáp án: B**  
> **Giải thích:** i=0: farthest=2, i==current_end(0) → jumps=1, current_end=2. i=1: farthest=max(2,4)=4. i=2: farthest=max(4,3)=4, i==current_end(2) → jumps=2, current_end=4. Loop kết thúc (i chỉ đến len-2=3). Trả về 2. Đây là Jump Game II — tìm số bước nhảy ít nhất.

---

**Câu 16:** Với Gas Station (LeetCode 134): gas=[1,2,3,4,5], cost=[3,4,5,1,2]. Greedy tìm điểm xuất phát thế nào?

- A. Thử mọi điểm bắt đầu O(n²)
- B. Nếu tổng gas >= tổng cost, điểm xuất phát là nơi bắt đầu sau đoạn âm cuối cùng
- C. Luôn bắt đầu từ trạm có gas lớn nhất
- D. Bắt đầu từ trạm có (gas - cost) lớn nhất

> **Đáp án: B**  
> **Giải thích:** Nếu sum(gas) >= sum(cost), luôn tồn tại đúng một nghiệm. Duyệt một lần: nếu tank < 0 tại trạm i, reset tank=0 và đặt start=i+1. Điểm start cuối cùng là đáp án. Logic: nếu từ A không đến được B, thì mọi trạm giữa A và B cũng không thể là điểm bắt đầu đến B.

---

**Câu 17:** Partition Labels (LeetCode 763): "ababcbacadefegdehijhklij". Tại sao Greedy đúng ở đây?

- A. Greedy sai với bài này, phải dùng DP
- B. Vì mỗi ký tự phải nằm trong đúng một partition, ta mở rộng partition đến last occurrence của mọi ký tự đã gặp
- C. Chỉ cần sort các ký tự theo thứ tự alphabet
- D. Chia đôi chuỗi cho đến khi không thể chia thêm

> **Đáp án: B**  
> **Giải thích:** Precompute last occurrence của mỗi ký tự. Duyệt từ trái: duy trì `end` = max(last[c] cho mọi c đã gặp). Khi i == end, ta đã "đóng" một partition an toàn (không ký tự nào trong partition này xuất hiện sau end). Greedy đúng vì ta luôn chọn partition nhỏ nhất có thể tại mỗi bước.

---

**Câu 18:** Task Scheduler (LeetCode 621): tasks = ["A","A","A","B","B","B"], n = 2. Công thức tính kết quả tối thiểu là gì?

- A. len(tasks)
- B. max(len(tasks), (max_freq - 1) * (n + 1) + count_of_max_freq)
- C. max_freq * n
- D. len(tasks) * n

> **Đáp án: B**  
> **Giải thích:** max_freq = 3 (A hoặc B), count_of_max_freq = 2. Công thức: max(6, (3-1)*(2+1)+2) = max(6, 8) = 8. Trực giác: sắp xếp A_BA_BA_B → 8 slots (2 idle). Công thức tính số slots tối thiểu cần thiết cho task phổ biến nhất, sau đó đảm bảo không nhỏ hơn tổng số task.

---

**Câu 19:** So sánh Kruskal và Prim để tìm Minimum Spanning Tree — cả hai đều là Greedy vì lý do gì?

- A. Cả hai đều sort tất cả cạnh
- B. Kruskal: greedy chọn cạnh nhỏ nhất không tạo cycle; Prim: greedy chọn cạnh nhỏ nhất nối vào cây đang xây
- C. Cả hai đều dùng BFS để duyệt đồ thị
- D. Chỉ Kruskal là Greedy; Prim dùng DP

> **Đáp án: B**  
> **Giải thích:** Kruskal: sort tất cả E cạnh, dùng Union-Find để tránh cycle, greedy chọn cạnh nhỏ nhất hợp lệ → O(E log E). Prim: duy trì min-heap các cạnh nối vào cây đang xây, greedy chọn cạnh nhỏ nhất → O(E log V). Cả hai đúng vì Cut Property của MST đảm bảo Greedy Choice Property.

---

**Câu 20:** Đoạn code dưới đây có bug gì?

```python
def activity_selection(activities):
    activities.sort(key=lambda x: x[0])  # sort by START
    count = 1
    last_end = activities[0][1]
    for i in range(1, len(activities)):
        if activities[i][0] >= last_end:
            count += 1
            last_end = activities[i][1]
    return count
```

- A. Không có bug, code đúng
- B. Bug: nên sort theo end time, không phải start time
- C. Bug: điều kiện nên là `>` thay vì `>=`
- D. Bug: nên khởi tạo count = 0

> **Đáp án: B**  
> **Giải thích:** Sort theo start time (A) không đảm bảo cho nghiệm tối ưu. Counterexample: [(0,10),(1,2),(2,3)] — sort by start chọn (0,10)+(2,3) = 2 hoạt động nhưng đáp án đúng là (1,2)+(2,3) = 2 hoạt động. Tuy nhiên với bài khác sort by start lại sai: [(0,100),(1,2),(3,4)] — sort by start chọn (0,100) trước rồi không chọn được gì thêm = 1, nhưng đáp án đúng là (1,2)+(3,4) = 2. **Phải sort theo end time.**

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 11  | A      |
| 2   | B      | 12  | B      |
| 3   | C      | 13  | B      |
| 4   | A      | 14  | C      |
| 5   | B      | 15  | B      |
| 6   | C      | 16  | B      |
| 7   | A      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | B      |
| 10  | A      | 20  | B      |
