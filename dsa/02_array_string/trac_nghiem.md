# Array & String — Trắc Nghiệm

---

## Phần 1: Cơ Bản (Câu 1–6)

---

**Câu 1.** Tại sao truy cập phần tử mảng theo index (ví dụ `arr[5]`) là O(1)?

A. Vì mảng luôn có ít hơn 100 phần tử  
B. Vì CPU biết địa chỉ bộ nhớ chính xác: `base_address + index × element_size`  
C. Vì hệ điều hành cache toàn bộ mảng  
D. Vì index là số nguyên, tra cứu integer nhanh hơn  

**Đáp án: B**

> **Giải thích:** Mảng lưu các phần tử **liên tiếp trong bộ nhớ**. Địa chỉ của `arr[i]` = `base_address + i × sizeof(element)`. CPU tính địa chỉ này trong 1 phép tính (multiply-add), không cần duyệt qua các phần tử khác.
> - A sai: hoàn toàn sai — O(1) với mảng triệu phần tử
> - C sai: OS không cache array — đó là L1/L2 cache của CPU, hoạt động khác
> - D sai: type của index không phải nguyên nhân

---

**Câu 2.** Thao tác nào sau đây trên Python list có độ phức tạp O(1)?

A. `lst.insert(0, x)` — thêm vào đầu  
B. `lst.pop(0)` — xóa phần tử đầu  
C. `lst.append(x)` — thêm vào cuối  
D. `lst[2:5]` — lấy slice  

**Đáp án: C**

> **Giải thích:** `append()` thêm vào cuối — O(1) amortized (đôi khi O(n) khi resize nhưng amortized O(1)).
> - A sai: insert vào đầu cần shift TẤT CẢ phần tử sang phải — O(n)
> - B sai: pop từ đầu cần shift tất cả sang trái — O(n)
> - D sai: slice tạo list mới, copy k phần tử — O(k)

---

**Câu 3.** Tại sao Python strings là immutable (bất biến)?

A. Vì Python không hỗ trợ mutable strings  
B. Vì lý do thread safety, hashability, và string interning — cho phép tối ưu bộ nhớ  
C. Vì bộ nhớ cố định khi tạo string  
D. Vì quy định của PEP 8  

**Đáp án: B**

> **Giải thích:** Ba lý do chính: (1) **Thread safety** — nhiều thread có thể dùng cùng string object mà không cần lock. (2) **Hashability** — string có thể làm dict key vì hash không đổi. (3) **String interning** — Python có thể reuse cùng object cho string giống nhau.
> - A sai: Python CÓ `bytearray` và `io.StringIO` là mutable alternatives
> - C sai: dynamic allocation vẫn xảy ra khi tạo string
> - D sai: PEP 8 là style guide, không liên quan đến mutability

---

**Câu 4.** Cách nào sau đây nối n chuỗi HIỆU QUẢ NHẤT?

A. `result = ""; for s in strings: result += s`  
B. `result = "".join(strings)`  
C. `result = reduce(lambda a,b: a+b, strings)`  
D. `result = str(strings)`  

**Đáp án: B**

> **Giải thích:** `"".join(strings)` là O(n) — Python tính tổng length trước, cấp phát 1 buffer, copy từng string vào 1 lần. Option A là O(n²) vì mỗi `+=` tạo string mới. Option C dùng `+` lặp lại — tương đương A, O(n²). Option D cho `['a','b','c']` → `"['a', 'b', 'c']"`.
> - A sai: O(n²) vì string immutable
> - C sai: `reduce` với `+` vẫn O(n²)
> - D sai: tạo string representation của list object

---

**Câu 5.** Độ phức tạp của `arr.insert(0, x)` trên Python list?

A. O(1)  
B. O(log n)  
C. O(n)  
D. O(n²)  

**Đáp án: C**

> **Giải thích:** Insert vào index 0 yêu cầu shift TẤT CẢ n phần tử hiện có sang phải một vị trí để nhường chỗ. n phần tử × O(1) mỗi shift = O(n). Nếu cần thường xuyên insert/delete ở đầu, dùng `collections.deque`.

---

**Câu 6.** Đoạn code sau tạo ra kết quả gì và tại sao?

```python
matrix = [[0] * 3] * 3
matrix[0][1] = 5
print(matrix)
```

A. `[[0,5,0], [0,0,0], [0,0,0]]` — chỉ hàng đầu thay đổi  
B. `[[0,5,0], [0,5,0], [0,5,0]]` — tất cả hàng thay đổi  
C. `[[5,0,0], [5,0,0], [5,0,0]]` — cột đầu thay đổi  
D. `[[0,0,0], [0,0,0], [0,0,0]]` — không thay đổi  

**Đáp án: B**

> **Giải thích:** `[[0]*3]*3` tạo 3 reference đến **cùng một list** `[0,0,0]`. Khi sửa `matrix[0][1]`, ta sửa list gốc → tất cả 3 hàng đều thay đổi vì cùng trỏ đến 1 object. Fix: `[[0]*3 for _ in range(3)]` tạo 3 list riêng biệt.

---

## Phần 2: Trung Bình (Câu 7–14)

---

**Câu 7.** Cần xóa duplicate khỏi mảng SORTED in-place với O(1) space. Approach nào đúng?

A. Dùng set để track các phần tử đã thấy — O(n) space  
B. Sort lại rồi dùng set — O(n log n) time  
C. Two pointers: slow pointer ghi vị trí phần tử unique, fast pointer duyệt qua — O(n) time, O(1) space  
D. Nested loop so sánh từng cặp — O(n²) time  

**Đáp án: C**

> **Giải thích:** Với mảng sorted, các duplicate đứng cạnh nhau. Dùng hai pointers: `slow` chỉ vị trí cuối mảng unique, `fast` duyệt qua. Khi `arr[fast] != arr[slow]`, tăng slow và copy `arr[fast]` vào `arr[slow]`. O(n) time, O(1) space — in-place.
> - A sai: O(n) space — không đáp ứng yêu cầu
> - B sai: mảng đã sorted rồi, không cần sort lại
> - D sai: O(n²) không cần thiết

---

**Câu 8.** Tại sao `arr[::-1]` và hàm `reverse_inplace()` khác nhau?

A. Không khác nhau — cả hai đều đảo ngược mảng  
B. `arr[::-1]` tạo list mới (O(n) space), `reverse_inplace` modify chính arr (O(1) space)  
C. `arr[::-1]` nhanh hơn vì dùng C internally  
D. `reverse_inplace` chỉ work với sorted array  

**Đáp án: B**

> **Giải thích:** `arr[::-1]` là slice operation — tạo **list object mới** với các phần tử theo thứ tự ngược. Original `arr` không thay đổi. In-place reverse dùng two pointers swap trực tiếp trên arr gốc, không cấp phát bộ nhớ mới.
> - A sai: kết quả giống nhau nhưng mechanism và side effects khác
> - C sai: cả hai đều dùng C; tốc độ tương đương, trade-off là memory
> - D sai: reverse không cần sorted

---

**Câu 9.** Prefix sum array giúp giải quyết bài toán gì hiệu quả nhất?

A. Tìm phần tử lớn nhất trong mảng — O(n) → O(log n)  
B. Tính tổng subarray bất kỳ `arr[l..r]` trong O(1) sau O(n) preprocessing  
C. Sort mảng nhanh hơn — O(n log n) → O(n)  
D. Tìm kiếm phần tử trong O(1)  

**Đáp án: B**

> **Giải thích:** Prefix sum: `p[i] = arr[0] + ... + arr[i-1]`. Tổng `arr[l..r]` = `p[r+1] - p[l]` — O(1). Build O(n). Khi có nhiều range queries (Q queries trên array n phần tử), tổng O(n + Q) thay vì O(n×Q) nếu mỗi query duyệt lại.
> - A sai: max tìm trong O(n) và không cần prefix sum
> - C sai: prefix sum không liên quan đến sorting
> - D sai: prefix sum không tìm kiếm phần tử

---

**Câu 10.** Cho mảng `[1, 2, 3, 4, 5]`, sau khi `rotate_array(arr, 2)`, kết quả là gì?

A. `[3, 4, 5, 1, 2]` — rotate left 2  
B. `[4, 5, 1, 2, 3]` — rotate right 2  
C. `[2, 1, 4, 3, 5]` — swap pairs  
D. `[5, 4, 1, 2, 3]` — partial reverse  

**Đáp án: B**

> **Giải thích:** Rotate right k=2 có nghĩa 2 phần tử cuối `[4,5]` chuyển ra đầu. Kỹ thuật 3 lần reverse: (1) reverse toàn bộ → `[5,4,3,2,1]`, (2) reverse k=2 phần đầu → `[4,5,3,2,1]`, (3) reverse phần còn lại → `[4,5,1,2,3]`.
> - A sai: đó là rotate left
> - C sai: swap pairs khác với rotate
> - D sai: đó là partial reverse, không phải rotate đúng

---

**Câu 11.** Khi nào `collections.deque` tốt hơn `list`?

A. Luôn luôn — deque tổng quát hơn  
B. Khi cần thường xuyên insert/delete ở cả hai đầu — O(1) vs O(n) của list  
C. Khi cần sort — deque sort nhanh hơn  
D. Khi kích thước array nhỏ hơn 100  

**Đáp án: B**

> **Giải thích:** `deque.appendleft()` và `deque.popleft()` là O(1), trong khi `list.insert(0, x)` và `list.pop(0)` là O(n). Nhưng `deque` không có O(1) random access — `deque[i]` là O(n). Chọn: cần index? Dùng list. Cần queue/deque operations? Dùng deque.
> - A sai: list có O(1) random access, deque thì không
> - C sai: deque không có phương thức sort built-in hiệu quả
> - D sai: size không quyết định

---

**Câu 12.** Tính complexity của hàm kiểm tra palindrome sau:

```python
def is_palindrome(s):
    return s == s[::-1]
```

A. O(1) — chỉ 1 dòng  
B. O(n) — tạo reverse string + so sánh  
C. O(n²) — vì có string comparison  
D. O(log n) — binary approach  

**Đáp án: B**

> **Giải thích:** `s[::-1]` tạo string mới (O(n) time + O(n) space). Sau đó `s == s[::-1]` so sánh từng ký tự (O(n) worst case). Tổng: O(n) time, O(n) space. Two-pointer approach cũng O(n) time nhưng O(1) space — tốt hơn về memory.
> - A sai: 1 dòng code không đồng nghĩa O(1)
> - C sai: string comparison là O(n), không phải O(n²)
> - D sai: không có pattern chia đôi

---

**Câu 13.** Bạn cần tìm subarray có tổng bằng k. Approach nào có complexity tốt nhất?

A. O(n³) — 3 nested loops: choose l, r, sum  
B. O(n²) — 2 loops: fix l, dùng prefix sum cho r  
C. O(n) — 1 pass với hashmap lưu prefix sum đã thấy  
D. O(n log n) — sort rồi binary search  

**Đáp án: C**

> **Giải thích:** Dùng hashmap `{prefix_sum: count}`. Tại mỗi vị trí i, `prefix[i]` là tổng từ đầu đến i. Nếu `prefix[i] - k` đã xuất hiện trước đó, ta có subarray tổng = k. O(n) time, O(n) space.
> - A sai: O(n³) là cách ngây thơ nhất
> - B sai: O(n²) tốt hơn A nhưng không tối ưu
> - D sai: sorting không giúp ích trực tiếp cho subarray sum

---

**Câu 14.** Memory layout nào ĐÚNG cho Python list `[1, 2, 3]`?

A. 3 integers lưu liên tiếp: `[int_1_bytes][int_2_bytes][int_3_bytes]`  
B. 3 pointers liên tiếp, mỗi pointer trỏ đến Python int object riêng: `[ptr→1][ptr→2][ptr→3]`  
C. 1 header + 3 integers packed như C array  
D. 3 Python objects ngẫu nhiên trong heap, không liên tiếp  

**Đáp án: B**

> **Giải thích:** Python list lưu array of **PyObject pointers** (8 bytes/pointer trên 64-bit). Mỗi pointer trỏ đến Python int object riêng biệt trong heap (mỗi object ~28 bytes). `[1,2,3]` thực sự tốn ~56 bytes overhead (list object) + 3×8 bytes pointers + 3×28 bytes int objects ≈ 164 bytes, so với 12 bytes của C int array.
> - A sai: Python không lưu raw int values trong list
> - C sai: Python list không pack integers
> - D sai: pointers liên tiếp trong list object (chứ không phải objects)

---

## Phần 3: Nâng Cao (Câu 15–22)

---

**Câu 15.** Hàm sau có bug gì?

```python
def remove_element(arr, val):
    for i in range(len(arr)):
        if arr[i] == val:
            arr.pop(i)
    return arr
```

A. Không có bug — hàm chạy đúng  
B. Bug: khi pop(i), các phần tử phía sau shift trái, có thể skip phần tử tiếp theo  
C. Bug: pop(i) là O(1) nhưng cần O(n)  
D. Bug: range(len(arr)) không work với list  

**Đáp án: B**

> **Giải thích:** Khi `arr.pop(i)` xóa phần tử tại index i, tất cả phần tử sau shift trái 1 vị trí. Phần tử trước ở index `i+1` giờ ở index `i`, nhưng vòng lặp tăng i lên `i+1` → phần tử đó bị **skip**. Nếu có 2 `val` liên tiếp, phần tử thứ 2 sẽ không được kiểm tra. Fix: duyệt ngược, hoặc dùng list comprehension.
> - A sai: có bug thật
> - C sai: pop(i) là O(n) do shift, nhưng đó không phải bug logic
> - D sai: range(len(arr)) work nhưng len thay đổi khi pop

---

**Câu 16.** Tại sao numpy array nhanh hơn Python list cho numerical computation?

A. Numpy được viết bằng ngôn ngữ khác (Fortran)  
B. Numpy lưu raw data liên tiếp (không có Python object overhead), hỗ trợ SIMD vectorized operations  
C. Numpy chỉ work với số nguyên, còn list work với mọi type  
D. Numpy tự động cache toàn bộ array trong RAM  

**Đáp án: B**

> **Giải thích:** Python list lưu array of pointers đến Python objects (~28 bytes overhead/element). Numpy array lưu raw bytes (ví dụ float64: 8 bytes/element) liên tiếp — không có Python object overhead. Quan trọng hơn, numpy dùng **SIMD** (Single Instruction Multiple Data) vectorized operations: CPU xử lý 4-8 float64 cùng lúc với 1 instruction. Kết quả: numpy 10-100x nhanh hơn Python loop.
> - A sai: numpy viết bằng C, không phải Fortran
> - C sai: numpy hỗ trợ nhiều types (float32, int8, complex, ...)
> - D sai: không có automatic array caching

---

**Câu 17.** Cho code sau, time complexity là bao nhiêu?

```python
def find_anagram_groups(words):
    groups = {}
    for word in words:              # O(n)
        key = "".join(sorted(word)) # O(k log k)
        if key not in groups:
            groups[key] = []
        groups[key].append(word)    # O(1) amortized
    return list(groups.values())
```

A. O(n) — chỉ 1 vòng lặp  
B. O(n × k log k) — n words, mỗi word sort O(k log k)  
C. O(n²) — nested operations  
D. O(n log n) — vì có sort  

**Đáp án: B**

> **Giải thích:** n = số words, k = max length của 1 word. Với mỗi word: `sorted(word)` = O(k log k). Vòng ngoài n iterations → O(n × k log k). Nếu k là constant (ví dụ max word length cố định), thì O(n). Trong interview, thường viết O(n × k log k) là chuẩn nhất.
> - A sai: bỏ qua cost của sorted() bên trong
> - C sai: không có nested loops — operations trong loop là O(k log k)
> - D sai: O(n log n) quên mất k

---

**Câu 18.** Rotate array right k steps có thể dùng kỹ thuật 3-reverse với O(1) space. Kỹ thuật này hoạt động vì:

A. Reverse là idempotent  
B. Reverse 2 lần = identity; chia array thành 2 phần rồi reverse từng phần + reverse tổng = rotate  
C. Toán học số học modular  
D. Đây chỉ là kỹ thuật trick không có lý thuyết nền  

**Đáp án: B**

> **Giải thích:** Phân tích: rotate right k = đưa k phần tử cuối lên đầu. Gọi A = arr[0..n-k-1], B = arr[n-k..n-1]. Ta muốn [B, A] từ [A, B]. Nhận xét: reverse([A,B]) = [B^R, A^R]. Rồi reverse B^R = B, reverse A^R = A → ta được [B, A]. Ba lần reverse: reverse toàn bộ, reverse prefix k, reverse suffix n-k.
> - A sai: reverse(reverse(x)) = x (đây là involution, không phải idempotent)
> - C sai: modular arithmetic không trực tiếp giải thích kỹ thuật này
> - D sai: có lý thuyết toán học rõ ràng

---

**Câu 19.** Hàm `string_compression("aabcccdddd")` trả về gì và complexity?

A. `"a2b1c3d4"` — O(n) time, O(n) space (dùng list để tránh O(n²))  
B. `"a2b1c3d4"` — O(n²) time vì string concatenation  
C. `"aabcccdddd"` — vì string ngắn hơn compressed  
D. `"2a1b3c4d"` — count đứng trước  

**Đáp án: A**

> **Giải thích:** "a2b1c3d4" có 8 ký tự < 10 ký tự gốc → return compressed. Dùng list để accumulate parts thay vì `+=` → O(n). Join cuối cùng O(n). Total: O(n) time, O(n) space. Nếu dùng `result += ...` trong loop sẽ O(n²).
> - B sai: implementation đúng dùng list → O(n), không phải O(n²)
> - C sai: "a2b1c3d4" (8 chars) < "aabcccdddd" (10 chars)
> - D sai: convention là `char + count`, không phải `count + char`

---

**Câu 20.** Tại sao `s.find(sub)` trong Python có average case O(n×m)?

A. Vì Python là interpreted language  
B. Naive substring matching: với mỗi position (n), so sánh đến m ký tự → O(n×m) worst case  
C. Vì string là immutable  
D. Vì hash computation  

**Đáp án: B**

> **Giải thích:** Naive approach: duyệt qua n-m+1 vị trí trong s, tại mỗi vị trí so sánh tối đa m ký tự với sub → O(n×m) worst case (ví dụ s="aaa...a", sub="aaa...ab"). CPython thực tế dùng **Two-Way algorithm** hoặc Boyer-Moore-Horspool, O(n) average case. Nhưng trong interview, nếu không nói rõ, assume O(n×m).
> - A sai: CPython thực sự dùng thuật toán tốt
> - C sai: immutability không ảnh hưởng đến search algorithm
> - D sai: `find()` không dùng hash (Rabin-Karp dùng hash, nhưng không phải CPython's default)

---

**Câu 21.** `memoryview` trong Python giải quyết vấn đề gì?

A. Cho phép Python array hoạt động như C array  
B. Cho phép truy cập buffer của object mà không copy data — zero-copy slicing  
C. Tự động convert bytes sang string  
D. Cache array trong GPU memory  

**Đáp án: B**

> **Giải thích:** `memoryview(b)` tạo view vào buffer của `b` mà **không copy**. Khi bạn slice `b[1:4]`, Python tạo bytes object mới (copy). `memoryview(b)[1:4]` chỉ tạo view object (pointer + offset) — O(1) thay vì O(k). Quan trọng với large binary data processing.
> - A sai: Python array đã có `array` module
> - C sai: memoryview không convert encoding
> - D sai: không liên quan đến GPU

---

**Câu 22.** Code sau có bug tinh vi không?

```python
def dutch_national_flag(arr, pivot):
    """Sort array thành [<pivot, =pivot, >pivot]"""
    low, mid, high = 0, 0, len(arr) - 1
    while mid <= high:
        if arr[mid] < pivot:
            arr[low], arr[mid] = arr[mid], arr[low]
            low += 1
            mid += 1
        elif arr[mid] == pivot:
            mid += 1
        else:
            arr[mid], arr[high] = arr[high], arr[mid]
            high -= 1
            # Không tăng mid!
    return arr
```

A. Có bug: cần tăng `mid` trong nhánh `else`  
B. Không có bug — đây là Dutch National Flag algorithm chuẩn  
C. Có bug: nên dùng `low < high` thay vì `mid <= high`  
D. Có bug: swap trong nhánh `<` sai  

**Đáp án: B**

> **Giải thích:** Đây là **Dijkstra's Dutch National Flag algorithm** chuẩn. Khi `arr[mid] > pivot`: swap với high và giảm high, **không tăng mid** vì phần tử từ high đưa về chưa được kiểm tra. Khi `arr[mid] < pivot`: swap với low, tăng cả low lẫn mid (phần tử từ low đã được processed). Khi `arr[mid] == pivot`: chỉ tăng mid.
> - A sai: không tăng mid trong else là CỐ Ý — phần tử mới chưa được kiểm tra
> - C sai: `mid <= high` là điều kiện dừng đúng
> - D sai: swap trong `<` là đúng

---

## Bảng Đáp Án Nhanh

| Câu | Đáp án | Chủ đề |
|-----|--------|--------|
| 1 | B | Contiguous memory, O(1) access |
| 2 | C | List operations complexity |
| 3 | B | String immutability |
| 4 | B | String join vs concatenation |
| 5 | C | Insert at head |
| 6 | B | Shallow copy pitfall |
| 7 | C | Two pointers in-place |
| 8 | B | Slice vs in-place |
| 9 | B | Prefix sum |
| 10 | B | Array rotation |
| 11 | B | deque vs list |
| 12 | B | Palindrome complexity |
| 13 | C | Subarray sum with hashmap |
| 14 | B | Python list memory layout |
| 15 | B | Modify-while-iterate bug |
| 16 | B | Numpy vs list internals |
| 17 | B | Anagram grouping complexity |
| 18 | B | 3-reverse technique |
| 19 | A | String compression |
| 20 | B | Substring search |
| 21 | B | memoryview |
| 22 | B | Dutch National Flag |
