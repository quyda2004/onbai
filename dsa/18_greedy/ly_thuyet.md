# Greedy Algorithm (Thuật toán Tham lam)

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đang đi mua sắm với một túi tiền cố định và muốn mua được nhiều món đồ nhất có thể. Chiến lược "tham lam" là: **luôn chọn món rẻ nhất trước**, rồi mới đến món tiếp theo — cứ thế cho đến khi hết tiền.

Bạn không ngồi tính toán mọi tổ hợp có thể. Bạn chỉ nhìn vào hiện tại và chọn cái tốt nhất **ngay lúc đó**.

Đó chính là Greedy: **tại mỗi bước, chọn lựa chọn tốt nhất có thể trong tầm mắt, không nhìn lại, không hối hận**.

Ví dụ khác từ cuộc sống:
- Trả tiền bằng tờ tiền có mệnh giá lớn nhất có thể trước (bài toán đổi tiền).
- Chọn lên taxi nào đến trước (không đợi taxi "tốt hơn" có thể đến sau).

---

## Giải thích cho người đã biết lập trình (nâng cao)

Greedy là một **paradigm thuật toán** (không phải một thuật toán cụ thể) mà tại mỗi bước ra quyết định, chọn **locally optimal choice** với hy vọng dẫn đến **globally optimal solution**.

### Hai tính chất bắt buộc để Greedy đúng

1. **Greedy Choice Property**: Lựa chọn tham lam tại mỗi bước là một phần của nghiệm tối ưu toàn cục. Tức là: "không có gì ta bỏ lỡ bằng cách chọn tham lam".
2. **Optimal Substructure**: Nghiệm tối ưu của bài toán chứa nghiệm tối ưu của các bài toán con. (Chung với Dynamic Programming — nhưng Greedy không cần lưu trạng thái)

### Tại sao Greedy thường nhanh hơn DP?

DP duyệt **tất cả tổ hợp con** và lưu memo → O(n²) hoặc O(n·W). Greedy chỉ duyệt **một lần** với một chiến lược cố định → thường O(n log n) do sorting.

### Khi nào Greedy KHÔNG đúng?

Bài toán 0/1 Knapsack: chọn vật có tỉ lệ value/weight cao nhất trước **không cho nghiệm đúng** vì vật không chia nhỏ được. Phải dùng DP.

### Các bài Greedy kinh điển trong phỏng vấn

| Bài toán | Chiến lược tham lam | Độ phức tạp |
|----------|--------------------|----|
| Activity Selection | Chọn job kết thúc sớm nhất | O(n log n) |
| Fractional Knapsack | Chọn tỉ lệ value/weight cao nhất | O(n log n) |
| Huffman Coding | Merge 2 node tần suất thấp nhất | O(n log n) |
| Dijkstra | Chọn node có dist nhỏ nhất chưa xử lý | O((V+E) log V) |
| Prim / Kruskal (MST) | Chọn edge nhỏ nhất không tạo cycle | O(E log E) |
| Jump Game | Cập nhật max reach tại mỗi bước | O(n) |
| Interval Scheduling / Merge Intervals | Sort theo start, merge overlap | O(n log n) |
| Assign Cookies | Sort cả 2, dùng 2 pointers | O(n log n) |

---

## Định nghĩa chính xác

**Greedy Algorithm** là một lớp thuật toán giải bài toán tối ưu hóa bằng cách xây dựng nghiệm từng bước. Tại mỗi bước, thuật toán chọn lựa chọn tối ưu cục bộ (locally optimal) mà không xem xét lại quyết định đã đưa ra. Thuật toán cho nghiệm tối ưu toàn cục nếu và chỉ nếu bài toán thỏa mãn **Greedy Choice Property** và **Optimal Substructure**.

---

## Độ phức tạp (Time & Space Complexity)

Greedy không có độ phức tạp cố định — phụ thuộc vào từng bài. Dưới đây là các pattern phổ biến:

| Bài toán / Thao tác | Best Case | Average Case | Worst Case | Auxiliary Space | Ghi chú |
|---------------------|-----------|--------------|------------|-----------------|---------|
| Activity Selection (sau sort) | O(n) | O(n) | O(n) | O(1) | Sau khi đã sort O(n log n) |
| Bước sort ban đầu | O(n log n) | O(n log n) | O(n log n) | O(log n) | Call stack của sort |
| Fractional Knapsack | O(n log n) | O(n log n) | O(n log n) | O(1) | Bottleneck là sort |
| Huffman Coding (với min-heap) | O(n log n) | O(n log n) | O(n log n) | O(n) | Lưu heap n node |
| Jump Game | O(1) | O(n) | O(n) | O(1) | Best: bước đầu reach = n |
| Coin Change (denominations đặc biệt) | O(n/max_coin) | O(n/max_coin) | O(n/max_coin) | O(1) | Chỉ đúng với US coins, không tổng quát |
| Merge Intervals | O(n log n) | O(n log n) | O(n log n) | O(n) | Output có thể n intervals |
| Dijkstra (với binary heap) | O(E log V) | O(E log V) | O(E log V) | O(V + E) | V đỉnh, E cạnh |

**Lưu ý quan trọng:**
- Greedy thường bắt đầu bằng **sorting** → O(n log n) là đặc trưng
- Sau sorting, phần xử lý chính thường chỉ O(n) → tổng vẫn O(n log n)
- Auxiliary Space thường O(1) nếu không dùng heap/output array

---

## Pseudocode / Code mẫu

### 1. Activity Selection Problem (Chọn nhiều hoạt động nhất không chồng lấp)

```python
def activity_selection(activities):
    """
    activities: list of (start, end)
    Trả về số lượng hoạt động có thể chọn tối đa
    """
    # Sắp xếp theo thời gian kết thúc — đây là greedy choice
    activities.sort(key=lambda x: x[1])
    
    count = 1
    last_end = activities[0][1]  # kết thúc của hoạt động đầu tiên được chọn
    
    for i in range(1, len(activities)):
        start, end = activities[i]
        # Chỉ chọn nếu không chồng lấp với hoạt động đã chọn cuối cùng
        if start >= last_end:
            count += 1
            last_end = end
    
    return count

# Test
acts = [(1, 4), (3, 5), (0, 6), (5, 7), (3, 9), (5, 9), (6, 10), (8, 11), (8, 12), (2, 14), (12, 16)]
print(activity_selection(acts))  # 4
```

### 2. Fractional Knapsack

```python
def fractional_knapsack(capacity, items):
    """
    items: list of (value, weight)
    Cho phép lấy một phần của vật
    """
    # Sắp xếp theo tỉ lệ value/weight giảm dần — greedy choice
    items.sort(key=lambda x: x[0] / x[1], reverse=True)
    
    total_value = 0.0
    remaining = capacity
    
    for value, weight in items:
        if remaining <= 0:
            break
        # Lấy toàn bộ hoặc lấy một phần
        take = min(weight, remaining)
        total_value += take * (value / weight)
        remaining -= take
    
    return total_value

# Test
items = [(60, 10), (100, 20), (120, 30)]  # (value, weight)
capacity = 50
print(fractional_knapsack(capacity, items))  # 240.0
```

### 3. Jump Game (LeetCode 55)

```python
def can_jump(nums):
    """
    nums[i] = số bước tối đa có thể nhảy từ i
    Kiểm tra có thể đến cuối mảng không
    """
    max_reach = 0
    
    for i in range(len(nums)):
        # Nếu vị trí hiện tại vượt quá max_reach → bị kẹt
        if i > max_reach:
            return False
        # Cập nhật reach xa nhất có thể đến
        max_reach = max(max_reach, i + nums[i])
    
    return True

# Test
print(can_jump([2, 3, 1, 1, 4]))  # True
print(can_jump([3, 2, 1, 0, 4]))  # False
```

### 4. Merge Intervals (LeetCode 56)

```python
def merge_intervals(intervals):
    """
    Merge các interval chồng lấp nhau
    """
    if not intervals:
        return []
    
    # Sort theo start time
    intervals.sort(key=lambda x: x[0])
    
    merged = [intervals[0]]
    
    for start, end in intervals[1:]:
        last_end = merged[-1][1]
        # Chồng lấp → mở rộng interval cuối
        if start <= last_end:
            merged[-1][1] = max(last_end, end)
        else:
            merged.append([start, end])
    
    return merged

# Test
print(merge_intervals([[1,3],[2,6],[8,10],[15,18]]))  # [[1,6],[8,10],[15,18]]
```

### 5. Coin Change — Greedy (chỉ đúng với một số hệ coin đặc biệt)

```python
def coin_change_greedy(coins, amount):
    """
    Chỉ đúng với coins như [1, 5, 10, 25] (US coins)
    KHÔNG đúng tổng quát — dùng DP cho bài tổng quát
    """
    coins.sort(reverse=True)
    count = 0
    for coin in coins:
        while amount >= coin:
            amount -= coin
            count += 1
    return count if amount == 0 else -1
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Bài toán có thể chứng minh thỏa mãn Greedy Choice Property
- Cần tối ưu về thời gian (O(n log n) thay vì O(n²) hay O(n·W) của DP)
- Bài toán liên quan đến: scheduling, interval, coin (hệ đặc biệt), spanning tree, shortest path
- Bài toán Fractional (được phép lấy một phần) — Greedy thường đúng
- Các bài "maximize số lượng" với constraint không chồng lấp

**Không dùng khi:**
- Bài toán 0/1 (không chia nhỏ được) → dùng DP
- Cần xem xét tất cả tổ hợp để chắc chắn (backtracking/DP)
- Coin change tổng quát (đồng xu tùy ý) → DP
- Bài toán Knapsack 0/1 → DP
- Không thể chứng minh Greedy Choice Property → dùng DP hoặc backtracking

---

## So sánh với các thuật toán liên quan

| Tiêu chí | Greedy | Dynamic Programming | Backtracking |
|----------|--------|--------------------|----|
| Ra quyết định | Cục bộ, không xét lại | Toàn cục, lưu mọi trạng thái | Thử mọi khả năng |
| Độ phức tạp | O(n log n) thường | O(n²) ~ O(n·W) | O(2ⁿ) worst |
| Tính đúng đắn | Chỉ đúng với một số bài | Luôn đúng (nếu có optimal substructure) | Luôn đúng |
| Lưu trạng thái | Không | Có (memo/table) | Implicit (call stack) |
| Triển khai | Đơn giản | Trung bình | Phức tạp |
| Ví dụ | Activity Selection | 0/1 Knapsack | N-Queens |
| Khi nào dùng | Greedy choice property | Tất cả tổ hợp con | Tìm tất cả nghiệm |

---

## Lỗi thường gặp (Common Pitfalls)

- **Áp dụng Greedy mà không chứng minh**: Coin change với đồng xu tùy ý — greedy cho kết quả sai. Luôn thử counterexample trước.
- **Nhầm Fractional với 0/1 Knapsack**: Fractional → Greedy đúng. 0/1 Knapsack → phải dùng DP.
- **Quên sort**: Hầu hết Greedy cần sort trước. Không sort → kết quả sai.
- **Sort nhầm tiêu chí**: Activity Selection cần sort theo `end`, không phải `start` hay `duration`.
- **Off-by-one trong Merge Intervals**: Điều kiện `start <= last_end` (overlap) vs `start < last_end` (strict overlap) — cần kiểm tra đề bài kỹ.
- **Coin Change Greedy trap**: Với coins = [1, 3, 4], amount = 6: Greedy chọn 4+1+1 = 3 coins; DP cho 3+3 = 2 coins (tốt hơn).
- **Không handle edge case**: Mảng rỗng, một phần tử, tất cả overlap, không thể reach đích.

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích Greedy Choice Property và tại sao nó cần thiết để Greedy cho nghiệm đúng.
- Coin Change: tại sao Greedy không đúng tổng quát? Cho ví dụ counterexample.
- LeetCode 55 — Jump Game: giải bằng Greedy, tại sao đúng?
- LeetCode 45 — Jump Game II: tìm số bước nhảy ít nhất — Greedy như thế nào?
- LeetCode 56 — Merge Intervals: sort theo tiêu chí gì? Tại sao?
- LeetCode 435 — Non-overlapping Intervals: bài Activity Selection biến thể.
- LeetCode 763 — Partition Labels: Greedy với last occurrence.
- LeetCode 134 — Gas Station: tại sao tổng gas >= tổng cost thì luôn có nghiệm?
- So sánh Greedy vs DP: khi nào chọn cái nào?
- Tại sao Dijkstra là Greedy? Tại sao không dùng được với negative weight?
- Huffman Coding: tại sao merge 2 node nhỏ nhất cho cây tối ưu?
- LeetCode 621 — Task Scheduler: tại sao Greedy (đặt task phổ biến nhất trước) cho nghiệm đúng?
