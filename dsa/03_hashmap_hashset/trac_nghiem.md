# Trắc nghiệm — HashMap & HashSet

> **Tổng số câu:** 22  
> **Mức độ:** Cơ bản (7) · Trung bình (9) · Nâng cao (6)

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Time complexity trung bình của thao tác `get`, `put`, `delete` trên HashMap là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n log n)

> **Đáp án: A**  
> **Giải thích:** HashMap dùng hash function map key sang bucket, average O(1). Worst case O(n) khi mọi key collide vào cùng bucket (rare nếu hash function tốt). Java 8+: bucket dài chuyển sang RB tree → worst O(log n).

---

**Câu 2:** Sự khác biệt then chốt giữa HashMap và HashSet?

- A. HashMap lưu cặp (key, value); HashSet chỉ lưu key (giá trị unique)
- B. HashMap nhanh hơn
- C. HashSet dùng ít memory hơn
- D. Không có khác biệt

> **Đáp án: A**  
> **Giải thích:** HashMap: ánh xạ key → value. HashSet: tập hợp các key, kiểm tra "có thuộc tập không" — thực chất implement bằng HashMap với value dummy (vd Java: backed by HashMap, value = Boolean.TRUE).

---

**Câu 3:** Khi nào HashMap thắng BST cho thao tác lookup?

- A. Khi cần in-order traversal
- B. Khi chỉ cần lookup theo key, không cần thứ tự
- C. Khi cần range query
- D. Không bao giờ

> **Đáp án: B**  
> **Giải thích:** HashMap O(1) avg vs BST O(log n). Nhưng HashMap không hỗ trợ ordered iteration hoặc range query — đó là chỗ BST/TreeMap thắng.

---

**Câu 4:** "Collision" trong HashMap nghĩa là?

- A. 2 key khác nhau map ra cùng 1 hash bucket
- B. Key bị mất
- C. HashMap đầy không insert được nữa
- D. Hash function trả về null

> **Đáp án: A**  
> **Giải thích:** Theo pigeonhole, với hash table kích thước m và số key > m, ít nhất 2 key sẽ collide. Có 2 cách xử lý chính: chaining (linked list/tree trong bucket) và open addressing (probe sang bucket khác).

---

**Câu 5:** Bài Two Sum (mảng chưa sort) dùng HashMap có complexity?

- A. O(1)
- B. O(n)
- C. O(n log n)
- D. O(n²)

> **Đáp án: B**  
> **Giải thích:** Duyệt mảng 1 lần, mỗi phần tử check `target - nums[i]` trong HashMap O(1). Tổng O(n) time, O(n) space. So với two pointers O(n log n) (cần sort trước).

---

**Câu 6:** Để dùng object làm key trong HashMap (Python dict / Java HashMap), object cần phải:

- A. Là số nguyên
- B. Hashable (immutable + có hash function ổn định)
- C. Có size cố định
- D. Có thuộc tính `id`

> **Đáp án: B**  
> **Giải thích:** Python: list/dict không hashable (mutable) → không làm key được; tuple, frozenset hashable. Java: object cần override `hashCode()` và `equals()` đúng quy tắc.

---

**Câu 7:** Khi HashMap đầy quá ngưỡng (load factor > 0.75 trong Java), HashMap sẽ?

- A. Báo lỗi
- B. Tự động resize (gấp đôi capacity) và rehash tất cả phần tử
- C. Bỏ qua insert mới
- D. Convert sang BST

> **Đáp án: B**  
> **Giải thích:** Resize đảm bảo load factor ổn định → giữ O(1) amortized. Rehash tốn O(n) tại lần resize, nhưng amortized O(1)/insert qua nhiều insert.

---

## Phần 2 — Trung bình (câu 8–16)

**Câu 8:** Worst case complexity của HashMap với hash function tệ là?

- A. O(1)
- B. O(log n)
- C. O(n)
- D. O(n²)

> **Đáp án: C**  
> **Giải thích:** Mọi key collide vào 1 bucket → bucket thành linked list dài n → lookup O(n). Java 8+ convert bucket dài thành RB tree → O(log n). Nhưng vẫn chậm hơn O(1) đáng kể.

---

**Câu 9:** Bài "Group Anagrams" (gom các string là anagram của nhau) dùng HashMap với key là gì?

- A. Hash của chính string
- B. String đã sort các ký tự (vd "eat" và "tea" cùng sort thành "aet")
- C. Length của string
- D. First character

> **Đáp án: B**  
> **Giải thích:** 2 string là anagram khi có cùng multiset ký tự → sort thì giống nhau. Dùng làm key trong HashMap. Time O(N × K log K) với K = max length. Phương án khác: tuple count 26 ký tự O(N × K).

---

**Câu 10:** Phương án tốt nhất cho "Top K Frequent Elements"?

- A. Sort theo frequency O(n log n)
- B. HashMap đếm + Heap kích thước K O(n log K)
- C. HashMap đếm + Bucket sort O(n)
- D. Cả B và C đều tốt; C nhanh hơn nhưng dùng nhiều space hơn

> **Đáp án: D**  
> **Giải thích:** Đếm freq O(n). Heap min size K duy trì top K → O(n log K). Bucket sort tạo n+1 bucket theo frequency → O(n) time nhưng O(n) extra space. Cả hai đều thắng A.

---

**Câu 11:** Set operations: union/intersection/difference của 2 HashSet độ lớn m, n có complexity tốt nhất là?

- A. O(m × n)
- B. O((m + n) log(m + n))
- C. O(m + n) avg
- D. O(m × log n)

> **Đáp án: C**  
> **Giải thích:** Iterate set nhỏ, lookup từng phần tử trong set lớn O(1) avg → O(min(m, n)) cho intersection. Union/difference tương tự O(m + n).

---

**Câu 12:** LRU Cache implement bằng cấu trúc nào để đạt O(1) cho cả get và put?

- A. HashMap thuần
- B. HashMap + Doubly Linked List
- C. Array sorted
- D. Heap

> **Đáp án: B**  
> **Giải thích:** HashMap cho lookup O(1). DLL cho phép move-to-front O(1) (cần prev pointer để unlink), evict tail O(1). HashMap thuần không có khái niệm "least recent".

---

**Câu 13:** Subarray Sum Equals K (mảng có số âm) tối ưu dùng?

- A. Sliding window
- B. Prefix sum + HashMap đếm số lần xuất hiện của (prefix_sum - K)
- C. Two pointers
- D. Sort

> **Đáp án: B**  
> **Giải thích:** Số âm phá monotonicity của sliding window. Prefix sum + HashMap O(n): với mỗi prefix sum P[i], số subarray kết thúc tại i với sum = K = số lần P[i] - K đã xuất hiện trong các prefix trước.

---

**Câu 14:** Open addressing (linear probing) vs Chaining trong HashMap implementation:

- A. Open addressing tốt cho cache locality, chaining đơn giản hơn và ít bị clustering
- B. Cả hai giống hệt nhau
- C. Chaining luôn nhanh hơn
- D. Open addressing không xử lý được collision

> **Đáp án: A**  
> **Giải thích:** Open addressing lưu mọi entry trong array → cache friendly hơn. Chaining (linked list/tree trong bucket) đơn giản hơn để code, không bị primary/secondary clustering. Tùy use case mà chọn.

---

**Câu 15:** "Hash" trong HashMap là?

- A. Phép sắp xếp
- B. Hàm map key sang một số nguyên cố định (hash code), từ đó tính bucket index = hash % capacity
- C. Phép so sánh
- D. Phép mã hóa bảo mật

> **Đáp án: B**  
> **Giải thích:** Hash function phải: (1) deterministic — cùng key cho cùng hash; (2) distribute uniform — tránh collision; (3) nhanh tính. Bảo mật là cryptographic hash, khác mục đích.

---

**Câu 16:** Bài "Longest Consecutive Sequence" (mảng số nguyên không sort) tối ưu O(n)?

- A. Sort rồi đếm O(n log n)
- B. HashSet: với mỗi số x, nếu x-1 không có trong set (x là start của 1 chuỗi) thì đếm chuỗi từ x đi lên
- C. Two pointers
- D. Heap

> **Đáp án: B**  
> **Giải thích:** Bỏ vào HashSet O(n). Mỗi số chỉ đếm trong chuỗi nếu là start (x-1 không có) → mỗi chuỗi chỉ đếm 1 lần. Tổng số bước trong tất cả chuỗi = n → O(n) amortized.

---

## Phần 3 — Nâng cao (câu 17–22)

**Câu 17:** Phân tích đoạn code, nó giải bài gì?

```python
def f(s):
    seen = {}
    left = 0
    best = 0
    for right, ch in enumerate(s):
        if ch in seen and seen[ch] >= left:
            left = seen[ch] + 1
        seen[ch] = right
        best = max(best, right - left + 1)
    return best
```

- A. Longest substring without repeating characters
- B. Đếm số ký tự distinct
- C. Min window substring
- D. Anagram check

> **Đáp án: A**  
> **Giải thích:** Sliding window + HashMap lưu vị trí cuối cùng của mỗi ký tự. Khi gặp lặp trong window hiện tại → nhảy left qua. Tracking độ dài window lớn nhất. O(n) time, O(min(n, charset)) space.

---

**Câu 18:** Tại sao Python set là hash table chứ không phải BST?

- A. Vì Python team chọn vậy
- B. Vì lookup O(1) avg quan trọng hơn ordered iteration trong common case; Python có `sorted()` riêng nếu cần
- C. Vì Python không hỗ trợ tree
- D. Vì set rất nhỏ thường

> **Đáp án: B**  
> **Giải thích:** Use case phổ biến của set là membership test O(1). Nếu cần ordered set → có `sorted()` hoặc bisect/SortedContainers. Java có cả `HashSet` và `TreeSet` để chọn.

---

**Câu 19:** Birthday Paradox cảnh báo về HashMap rằng:

- A. Mỗi HashMap có "sinh nhật" cần reset
- B. Với hash space size N, collision đầu tiên xảy ra ở khoảng √N keys — collision thực tế phổ biến hơn ta tưởng
- C. HashMap chỉ hoạt động vào ngày sinh nhật
- D. Không liên quan đến HashMap

> **Đáp án: B**  
> **Giải thích:** Với 23 người, xác suất 2 người trùng sinh nhật > 50%. Với hash 32-bit (~4B values), chỉ cần ~65K keys là có 50% collision. Đây là lý do hash table cần xử lý collision tốt; cũng là cơ sở của một số tấn công bảo mật (HashDoS).

---

**Câu 20:** "Consistent Hashing" được dùng trong hệ thống phân tán để:

- A. Giảm số rehash khi thêm/xóa node trong cluster — chỉ K/N keys di chuyển thay vì gần toàn bộ
- B. Tăng tốc HashMap đơn lẻ
- C. Bảo mật key
- D. Giảm memory

> **Đáp án: A**  
> **Giải thích:** Phân tán key trên N node theo hash ring. Khi thêm/xóa node, chỉ ~K/N key cần reassign (K = tổng keys), thay vì gần như tất cả như naive hash mod N. Dùng trong CDN, distributed cache (Memcached, Cassandra ring).

---

**Câu 21:** Bài "4Sum II" (cho 4 mảng A, B, C, D, đếm số bộ (i,j,k,l) sao cho A[i]+B[j]+C[k]+D[l] = 0). Phương án tối ưu?

- A. 4 vòng for, O(n⁴)
- B. HashMap đếm tổng A[i]+B[j], sau đó tìm -(C[k]+D[l]) trong map → O(n²)
- C. Sort + 2-pointer → O(n³)
- D. Cả 4 mảng cùng sort

> **Đáp án: B**  
> **Giải thích:** Chia 4 mảng thành 2 cặp. Tổng A+B có n² giá trị, lưu trong HashMap. Với mỗi C[k]+D[l], cộng số lần -(C+D) xuất hiện trong map → O(n²) time và space. Classic "meet in the middle".

---

**Câu 22:** Khi nào Python `dict` collision có thể bị khai thác cho DoS attack?

- A. Khi attacker biết hash function và craft các key collide tất cả vào 1 bucket — biến mọi insert thành O(n), tổng O(n²) cho n inserts
- B. Khi dict quá lớn
- C. Khi key có Unicode
- D. Không bao giờ

> **Đáp án: A**  
> **Giải thích:** HashDoS attack. Python 3.4+ dùng random hash seed (PYTHONHASHSEED) mặc định để chống attack này. Frameworks web cũ (PHP, Java cũ) từng có CVE liên quan. Tránh dùng deterministic hash với input untrusted.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | A      | 12  | B      |
| 2   | A      | 13  | B      |
| 3   | B      | 14  | A      |
| 4   | A      | 15  | B      |
| 5   | B      | 16  | B      |
| 6   | B      | 17  | A      |
| 7   | B      | 18  | B      |
| 8   | C      | 19  | B      |
| 9   | B      | 20  | A      |
| 10  | D      | 21  | B      |
| 11  | C      | 22  | A      |
