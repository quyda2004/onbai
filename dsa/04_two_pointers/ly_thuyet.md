# Two Pointers — Kỹ thuật Hai Con Trỏ

## 1. Giải thích cho người mới hoàn toàn

Tưởng tượng bạn có một dãy ghế dài xếp hàng ngang, mỗi ghế ghi một con số. Bạn cần tìm hai chiếc ghế mà tổng số trên chúng bằng đúng 10.

**Cách "ngây thơ":** Bạn đứng ở ghế đầu tiên, hỏi từng ghế còn lại "tổng với tôi có bằng 10 không?". Xong rồi sang ghế thứ hai, lại hỏi tiếp từng ghế phía sau. Nếu có 1000 ghế, bạn phải hỏi gần một triệu lần.

**Cách Two Pointers:** Nếu dãy ghế đã được sắp xếp tăng dần từ trái sang phải, bạn cử **hai người**:
- Người **L** đứng ở ghế đầu (số nhỏ nhất).
- Người **R** đứng ở ghế cuối (số lớn nhất).

Cộng hai số lại:
- Nếu tổng **lớn hơn 10** → bảo người R lùi sang trái một bước (giảm tổng xuống).
- Nếu tổng **nhỏ hơn 10** → bảo người L tiến sang phải một bước (tăng tổng lên).
- Nếu tổng **đúng bằng 10** → tìm thấy.

Hai người dần dần tiến lại gần nhau. Khi gặp nhau mà chưa tìm được → kết luận không có. Cách này chỉ tốn khoảng 1000 bước thay vì 1 triệu.

**Bản chất:** Thay vì duyệt mọi cặp, ta dùng **tính chất sắp xếp** để loại bỏ hàng loạt khả năng trong một bước.

### Một ví dụ khác — Đảo ngược chuỗi

Bạn muốn đảo ngược chuỗi `"hello"` thành `"olleh"`. Đặt một ngón tay vào ký tự đầu (`h`), một ngón tay vào ký tự cuối (`o`). Tráo đổi hai ký tự, rồi cả hai ngón tay tiến vào giữa một bước. Lặp lại đến khi gặp nhau. Bạn vừa dùng two pointers.

---

## 2. Giải thích nâng cao cho người chuyên ngành

**Two Pointers** là một họ kỹ thuật (technique family) chứ không phải một thuật toán đơn lẻ. Đặc điểm chung: duy trì 2 chỉ số (hoặc 2 con trỏ) trên cùng một mảng/chuỗi/danh sách liên kết, và di chuyển chúng theo một quy tắc giúp giảm complexity từ O(n²) xuống O(n) (hoặc O(n log n) nếu phải sort trước).

### Phân loại biến thể chính

**1. Opposite-direction (đối hướng / converging pointers)**
- Hai con trỏ bắt đầu ở 2 đầu, di chuyển vào giữa.
- Điều kiện áp dụng: mảng **đã sắp xếp** hoặc bài toán có tính đối xứng (palindrome, reverse, container with most water).
- Ví dụ kinh điển: Two Sum II (sorted array), 3Sum, Container With Most Water, Trapping Rain Water (variant).

**2. Same-direction (cùng hướng / slow-fast pointers)**
- Hai con trỏ cùng đi từ trái sang phải nhưng với tốc độ/vai trò khác nhau.
- Bao gồm:
  - **In-place modification**: `slow` đánh dấu vị trí ghi, `fast` đánh dấu vị trí đọc (Remove Duplicates from Sorted Array, Move Zeroes).
  - **Floyd's Cycle Detection (Tortoise and Hare)**: `slow` đi 1 bước, `fast` đi 2 bước, dùng để phát hiện chu trình trong linked list hoặc trong functional graph.
  - **Sliding Window** thực chất là biến thể two pointers cùng hướng — xem chuyên đề riêng `05_sliding_window`.

**3. Two arrays (hai mảng riêng)**
- Mỗi con trỏ duyệt một mảng khác nhau.
- Ví dụ: Merge hai sorted array, Intersection of Two Arrays, Merge step trong Merge Sort.

### Trade-off quan trọng

- **Yêu cầu tiền điều kiện**: Đa số biến thể opposite-direction yêu cầu mảng đã sorted. Nếu mảng chưa sort, phải trả thêm O(n log n) — khi đó có thể HashMap O(n) thắng. Cân nhắc:
  - **Sorted + Two Pointers**: O(n log n) time, O(1) auxiliary space.
  - **HashMap**: O(n) time, O(n) space.
  - Two Pointers thắng khi memory hạn chế, hoặc khi mảng đã có sẵn theo sorted form.

- **Không trùng lặp (no duplicates) trong 3Sum**: phải skip các giá trị bằng nhau khi di chuyển con trỏ — đây là common pitfall.

- **In-place vs out-of-place**: Two pointers thường cho phép sửa tại chỗ với O(1) extra space — ưu thế lớn so với approach copy-mới.

### Khi nào Two Pointers thực sự "đẹp"

Mỗi con trỏ chỉ duyệt mảng tối đa một lần → tổng số bước ≤ 2n → O(n). Điều này gọi là **amortized analysis** — dù trong mỗi vòng lặp có thể có nhiều thao tác con, tổng số lần con trỏ di chuyển bị chặn bởi n.

---

## 3. Định nghĩa chính xác

**Two Pointers Technique**: Một paradigm thuật toán sử dụng hai chỉ số (indices) hoặc con trỏ (pointers) duyệt qua cấu trúc dữ liệu tuần tự (thường là array, string hoặc linked list) theo một quy tắc di chuyển có điều kiện, nhằm tìm cặp/dãy phần tử thỏa mãn một thuộc tính nào đó với complexity tốt hơn brute force.

**Tortoise and Hare (Floyd's Algorithm)**: Trường hợp đặc biệt của two pointers cùng hướng, slow di chuyển 1 bước/lần, fast di chuyển 2 bước/lần. Dùng phát hiện chu trình; nếu có chu trình, hai con trỏ chắc chắn gặp nhau trong O(n).

---

## 4. Bảng Độ phức tạp đầy đủ

### Các bài toán điển hình

| Bài toán | Best | Average | Worst | Auxiliary Space | Ghi chú |
|----------|------|---------|-------|-----------------|---------|
| Two Sum trên sorted array | O(1) | O(n) | O(n) | O(1) | Best khi cặp ở ngay 2 đầu |
| Two Sum chưa sort + sort trước | O(n log n) | O(n log n) | O(n log n) | O(log n) hoặc O(n) | Space do sort algorithm |
| 3Sum | O(n²) | O(n²) | O(n²) | O(log n) | Phải sort trước |
| Container With Most Water | O(n) | O(n) | O(n) | O(1) | Single pass |
| Trapping Rain Water (2-pointer) | O(n) | O(n) | O(n) | O(1) | Tối ưu hơn DP O(n) space |
| Remove Duplicates Sorted Array | O(n) | O(n) | O(n) | O(1) | In-place |
| Floyd Cycle Detection | O(n) | O(n) | O(n) | O(1) | n = độ dài cycle + tail |
| Reverse String | O(n) | O(n) | O(n) | O(1) | n/2 swap |
| Merge 2 Sorted Arrays | O(m+n) | O(m+n) | O(m+n) | O(m+n) | Hoặc O(1) nếu merge từ cuối in-place |
| Palindrome Check | O(1) | O(n) | O(n) | O(1) | Best khi ký tự đầu ≠ cuối |

**Quy tắc bất biến (invariant)**: Trong opposite-direction, không gian kiếm tìm còn lại luôn nằm giữa hai con trỏ. Mỗi bước hợp lệ thu hẹp không gian này ít nhất 1 đơn vị → đảm bảo O(n).

---

## 5. Code mẫu

### Two Sum trên sorted array (opposite direction)

```python
def two_sum_sorted(arr, target):
    """
    arr: list số nguyên đã sort tăng dần
    target: tổng cần tìm
    return: tuple (i, j) chỉ số, hoặc None nếu không có
    """
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return (left, right)
        elif s < target:
            left += 1   # tổng quá nhỏ → tăng phía trái
        else:
            right -= 1  # tổng quá lớn → giảm phía phải
    return None

print(two_sum_sorted([1, 3, 4, 5, 7, 11], 9))  # (1, 4) -> 3+? Sai, để xem: 3+7=10. 1+7=8. 4+5=9 → (2,3)
```

### Remove Duplicates from Sorted Array (same direction)

```python
def remove_duplicates(arr):
    """
    arr đã sort. Sửa tại chỗ sao cho các phần tử unique nằm ở đầu.
    return: số phần tử unique k. arr[:k] chứa các giá trị unique.
    """
    if not arr:
        return 0
    slow = 0  # vị trí ghi tiếp theo
    for fast in range(1, len(arr)):
        if arr[fast] != arr[slow]:
            slow += 1
            arr[slow] = arr[fast]
    return slow + 1

a = [1, 1, 2, 2, 2, 3, 4, 4]
k = remove_duplicates(a)
print(k, a[:k])  # 4 [1, 2, 3, 4]
```

### 3Sum (kết hợp sort + two pointers)

```python
def three_sum(nums):
    """
    Tìm tất cả triplet (a, b, c) sao cho a+b+c = 0, không lặp.
    """
    nums.sort()
    result = []
    n = len(nums)
    for i in range(n - 2):
        if i > 0 and nums[i] == nums[i-1]:
            continue  # skip duplicate cho vị trí cố định
        left, right = i + 1, n - 1
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                # skip duplicate cho left và right
                while left < right and nums[left] == nums[left+1]:
                    left += 1
                while left < right and nums[right] == nums[right-1]:
                    right -= 1
                left += 1
                right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1
    return result

print(three_sum([-1, 0, 1, 2, -1, -4]))
# [[-1, -1, 2], [-1, 0, 1]]
```

### Floyd's Cycle Detection

```python
def has_cycle(head):
    """
    head: node đầu của linked list
    return: True nếu có chu trình
    """
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```

### Trapping Rain Water (two pointers)

```python
def trap(height):
    """
    Tính lượng nước đọng giữa các cột bar.
    """
    if not height:
        return 0
    left, right = 0, len(height) - 1
    left_max = right_max = 0
    water = 0
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    return water

print(trap([0,1,0,2,1,0,1,3,2,1,2,1]))  # 6
```

---

## 6. Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Mảng/chuỗi đã sort, hoặc bài toán có cấu trúc đối xứng/đơn điệu.
- Tìm cặp, triplet hoặc subarray thỏa mãn một điều kiện cộng/đếm.
- Cần in-place modification với O(1) extra space.
- Phát hiện chu trình trong linked list hoặc functional graph.
- Merge nhiều mảng đã sort.
- Bài toán palindrome.

**Không dùng khi:**
- Mảng không sort và việc sort phá vỡ thông tin gốc cần dùng (ví dụ cần giữ thứ tự chỉ số gốc cho Two Sum cơ bản → dùng HashMap).
- Bài toán yêu cầu mọi cặp/triplet (không có điều kiện loại bỏ) — vẫn là O(n²) hoặc O(n³).
- Dữ liệu không tuần tự (cây tổng quát, đồ thị) — dùng DFS/BFS thay thế.
- Cần truy cập ngẫu nhiên nhiều lần không theo thứ tự.

---

## 7. So sánh với các kỹ thuật liên quan

| Tiêu chí | Two Pointers | Sliding Window | HashMap | Brute Force |
|----------|--------------|----------------|---------|-------------|
| Yêu cầu sorted | Thường có | Không nhất thiết | Không | Không |
| Time | O(n) hoặc O(n log n) | O(n) | O(n) | O(n²) trở lên |
| Space | O(1) | O(k) (k = window) | O(n) | O(1) |
| In-place | Có | Có | Không | Tùy |
| Bài toán điển hình | Two Sum sorted, 3Sum | Subarray sum, max k chars | Two Sum unsorted | Tham chiếu so sánh |

**Two Pointers vs Sliding Window**: Sliding window là two pointers cùng hướng đặc thù cho bài subarray/substring liên tục. Two pointers tổng quát hơn — có thể đối hướng, có thể trên 2 mảng khác nhau.

**Two Pointers vs Binary Search**: Cả hai đều khai thác tính sắp xếp. Binary search O(log n) cho mỗi truy vấn đơn lẻ; Two Pointers O(n) cho cả quá trình quét toàn bộ. Khi cần duyệt tất cả cặp thỏa mãn → Two Pointers. Khi cần tìm 1 giá trị cụ thể → Binary Search.

---

## 8. Lỗi thường gặp (Common Pitfalls)

1. **Quên điều kiện dừng `left < right`** — viết `left <= right` gây vòng lặp tiếp tục khi 2 con trỏ trùng nhau, có thể đếm cùng 1 phần tử 2 lần.

2. **Không skip duplicate trong 3Sum** — kết quả chứa các bộ ba trùng lặp.

3. **Di chuyển con trỏ sai chiều** — sorted tăng dần, tổng nhỏ → phải tăng `left`, không phải giảm `right`. Nhầm logic này khi làm sorted giảm dần.

4. **Floyd Cycle: kiểm tra `fast and fast.next`** — nếu chỉ check `fast`, gọi `fast.next.next` có thể null pointer.

5. **Mảng chưa sort nhưng dùng opposite-direction** — kết quả sai. Phải sort trước hoặc đổi sang HashMap.

6. **Sửa mảng đang duyệt** — Move Zeroes hay Remove Element: nếu vừa đọc vừa xóa phần tử (kiểu `del`), index sẽ trượt → dùng slow/fast in-place thay vì xóa.

7. **Off-by-one ở `len(arr) - 1`** — quên `-1` cho right pointer, gây index out of bounds.

8. **In-place sort phá thứ tự gốc** — nếu cần trả về index gốc (như LeetCode Two Sum I), phải lưu mapping trước khi sort.

---

## 9. Câu hỏi phỏng vấn hay gặp

- Khi nào Two Pointers tốt hơn HashMap cho Two Sum? Trade-off cụ thể?
- Tại sao Floyd's algorithm đảm bảo gặp nhau nếu có cycle? Chứng minh.
- Làm sao tìm node bắt đầu cycle sau khi đã detect được? (Đặt lại 1 con trỏ về head, di chuyển cùng tốc độ với con trỏ trong cycle.)
- Container With Most Water: tại sao luôn di chuyển con trỏ ở cột thấp hơn?
- 3Sum: phân tích complexity O(n²) chi tiết. Tại sao không phải O(n³)?
- Dạng tổng quát kSum: cách giải đệ quy + two pointers ở đáy. Complexity O(n^(k-1)).
- So sánh Trapping Rain Water bằng DP vs Two Pointers — cùng O(n) time nhưng khác space.
- Move Zeroes giữ relative order: viết bằng two pointers in-place.
