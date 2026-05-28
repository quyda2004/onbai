# HashMap & HashSet — Bảng Băm

---

## 1. Vấn đề đặt ra

Bạn có danh sách 1 triệu sinh viên, mỗi sinh viên có mã số. Khi cần tìm thông tin của sinh viên mã số "SV12345", bạn phải duyệt qua tất cả 1 triệu bản ghi — O(n). Nếu có 10.000 truy vấn mỗi giây, đây là thảm họa.

Bài toán thực tế: **tìm kiếm nhanh theo "tên" (key) thay vì theo vị trí (index)**. Array giải quyết "cho tôi phần tử thứ 5", nhưng không giải quyết "cho tôi phần tử có tên là X" một cách hiệu quả. HashMap ra đời để làm đúng điều đó — tìm kiếm O(1) theo key bất kỳ.

---

## 2. Giải thích bằng hình ảnh đời thường

Hãy tưởng tượng một **tủ đựng hồ sơ** kiểu cũ ở văn phòng.

**Cách cũ (Array):** Hồ sơ được xếp theo số thứ tự: ngăn 1, ngăn 2, ngăn 3... Muốn tìm hồ sơ của "Nguyễn Văn An", bạn phải xem từng ngăn một.

**Cách mới (HashMap):** Bạn có một thư ký siêu giỏi. Khi nhận hồ sơ của "Nguyễn Văn An", thư ký tính một con số từ cái tên đó (ví dụ: cộng mã ASCII các chữ cái lại) và bỏ vào ngăn tương ứng. Khi cần tìm, thư ký tính lại con số từ tên, mở đúng ngăn đó — **không cần tìm kiếm**.

Thư ký này chính là **hash function**. Con số tính ra là **hash code**. Ngăn hồ sơ là **bucket**.

**HashSet** là tủ hồ sơ không cần lưu nội dung — chỉ cần biết "cái tên này đã có trong tủ chưa".

---

## 3. Khái niệm cơ bản

**HashMap (dict trong Python):** Cấu trúc lưu trữ cặp key-value. Cho phép tìm kiếm, thêm, xóa theo key trong O(1) trung bình.

**HashSet (set trong Python):** Tập hợp các giá trị không trùng lặp. Cho phép kiểm tra "có tồn tại không" trong O(1) trung bình.

**Hash function:** Hàm chuyển đổi key (chuỗi, số, ...) thành một con số nguyên (index).

Ví dụ nhỏ nhất:
```python
# HashMap
phone_book = {}
phone_book["An"] = "0912345678"    # thêm
phone_book["Binh"] = "0987654321"

print(phone_book["An"])            # "0912345678" — tìm O(1)
print("An" in phone_book)          # True — kiểm tra O(1)
del phone_book["Binh"]             # xóa O(1)

# HashSet
visited = set()
visited.add("node_A")
print("node_A" in visited)         # True — O(1)
print("node_B" in visited)         # False — O(1)
```

---

## 4. Ví dụ từng bước (step-by-step)

### Bài toán: Tìm phần tử xuất hiện 2 lần đầu tiên

```
Input: [4, 3, 2, 7, 3, 1, 4]
```

**Bước 1:** Tạo HashSet `seen = {}` để ghi nhớ đã thấy gì.
**Bước 2:** Gặp `4` → chưa có trong `seen` → thêm vào: `seen = {4}`
**Bước 3:** Gặp `3` → chưa có → thêm: `seen = {4, 3}`
**Bước 4:** Gặp `2` → chưa có → thêm: `seen = {4, 3, 2}`
**Bước 5:** Gặp `7` → chưa có → thêm: `seen = {4, 3, 2, 7}`
**Bước 6:** Gặp `3` → **ĐÃ CÓ trong seen** → đây là phần tử lặp đầu tiên! Trả về `3`.

Tại sao dùng HashSet thay vì Array? Vì `3 in seen` là O(1), còn `3 in [4, 3, 2, 7]` là O(n) — phải quét toàn bộ.

---

## 5. Code Python đơn giản nhất

```python
# Tìm phần tử đầu tiên xuất hiện 2 lần
def find_first_duplicate(arr):
    seen = set()
    for num in arr:
        if num in seen:      # O(1) lookup
            return num
        seen.add(num)        # O(1) insert
    return -1

# Test
print(find_first_duplicate([4, 3, 2, 7, 3, 1, 4]))  # 3
print(find_first_duplicate([1, 2, 3]))               # -1

# Đếm tần suất xuất hiện
def count_frequency(arr):
    freq = {}
    for num in arr:
        freq[num] = freq.get(num, 0) + 1   # get với default 0
    return freq

print(count_frequency([1, 2, 1, 3, 2, 1]))  # {1: 3, 2: 2, 3: 1}
```

---

## 6. Mở rộng dần

### Hash function hoạt động như thế nào?

```python
# Python dùng hàm hash() built-in
print(hash("hello"))   # Một số nguyên lớn, ví dụ: -5182528...
print(hash(42))        # 42 (số nguyên hash bằng chính nó)
print(hash((1, 2, 3))) # Tuple có thể hash (immutable)
# print(hash([1, 2, 3]))  # LỖI! List không hashable (mutable)
```

### Collision — Khi 2 key có cùng hash

```python
# Minh họa collision resolution bằng chaining
# (Cách Python thực sự dùng: open addressing + probing)

class SimpleHashMap:
    def __init__(self, size=10):
        self.size = size
        self.buckets = [[] for _ in range(size)]   # mỗi bucket là 1 list
    
    def _hash(self, key):
        return hash(key) % self.size
    
    def put(self, key, value):
        idx = self._hash(key)
        for pair in self.buckets[idx]:
            if pair[0] == key:
                pair[1] = value   # update nếu key tồn tại
                return
        self.buckets[idx].append([key, value])   # thêm mới
    
    def get(self, key):
        idx = self._hash(key)
        for pair in self.buckets[idx]:
            if pair[0] == key:
                return pair[1]
        return None

m = SimpleHashMap()
m.put("an", 100)
m.put("binh", 200)
print(m.get("an"))   # 100
```

### defaultdict — tránh KeyError

```python
from collections import defaultdict, Counter

# defaultdict tự động tạo giá trị default khi key chưa tồn tại
word_count = defaultdict(int)   # default = 0
for word in ["apple", "banana", "apple", "cherry", "banana", "apple"]:
    word_count[word] += 1   # Không cần kiểm tra key tồn tại

print(dict(word_count))  # {'apple': 3, 'banana': 2, 'cherry': 1}

# Counter — chuyên đếm tần suất
counter = Counter(["apple", "banana", "apple"])
print(counter.most_common(2))  # [('apple', 2), ('banana', 1)]
```

---

## 7. Độ phức tạp

### HashMap / HashSet

| Thao tác | Best | Average | Worst | Space | Ghi chú |
|----------|------|---------|-------|-------|---------|
| Tìm kiếm (lookup) | O(1) | O(1) | O(n) | O(1) | Worst khi tất cả key collision |
| Thêm (insert) | O(1) | O(1) | O(n) | O(1) | Worst khi resize + collision |
| Xóa (delete) | O(1) | O(1) | O(n) | O(1) | Worst khi collision |
| Duyệt tất cả | O(n) | O(n) | O(n) | O(1) | Luôn O(n) |
| Space (total) | O(n) | O(n) | O(n) | — | Lưu n cặp key-value |

**Tại sao worst case O(n)?** Nếu tất cả key đều hash vào cùng 1 bucket (collision), bucket đó là một danh sách dài n → tìm kiếm O(n). Trong thực tế cực kỳ hiếm với hash function tốt.

**Python 3.7+ guarantee:** dict duy trì thứ tự insertion. Set không đảm bảo thứ tự.

### Load Factor và Resize

```
Load factor = số phần tử / số bucket
- Khi load factor > 0.75 (Python): resize lên gấp đôi
- Resize: tạo mảng mới, rehash tất cả phần tử → O(n) một lần
- Amortized: O(1) per insert
```

---

## 8. Patterns & Variations

### Pattern 1: Two Sum — HashMap để tìm complement

```python
def two_sum(nums, target):
    """
    Với mỗi số x, kiểm tra (target - x) đã thấy chưa.
    O(n) time, O(n) space.
    """
    seen = {}   # {value: index}
    for i, x in enumerate(nums):
        complement = target - x
        if complement in seen:
            return [seen[complement], i]
        seen[x] = i
    return []

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]
```

### Pattern 2: Group By — nhóm theo tiêu chí

```python
from collections import defaultdict

def group_anagrams(strs):
    """
    Nhóm các anagram lại với nhau.
    Key = sorted version của chuỗi.
    """
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))   # "eat" → "aet", "tea" → "aet"
        groups[key].append(s)
    return list(groups.values())

print(group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
# [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

### Pattern 3: Sliding Window + HashMap

```python
def length_of_longest_substring(s):
    """
    Chuỗi con không có ký tự trùng lặp dài nhất.
    HashMap lưu vị trí cuối cùng của mỗi ký tự.
    O(n) time, O(min(n, alphabet_size)) space.
    """
    char_index = {}   # ký tự → vị trí gần nhất
    max_len = 0
    left = 0
    
    for right, char in enumerate(s):
        if char in char_index and char_index[char] >= left:
            left = char_index[char] + 1   # co cửa sổ lại
        char_index[char] = right
        max_len = max(max_len, right - left + 1)
    
    return max_len

print(length_of_longest_substring("abcabcbb"))  # 3 ("abc")
```

---

## 9. Code nâng cao — Production level

```python
# ============================================================
# LRU Cache — HashMap + Doubly Linked List
# ============================================================

from collections import OrderedDict

class LRUCache:
    """
    Least Recently Used Cache.
    Khi cache đầy, xóa phần tử ít dùng nhất gần đây.
    
    Dùng OrderedDict để maintain insertion order.
    get/put đều O(1).
    """
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)   # đánh dấu "mới dùng gần nhất"
        return self.cache[key]
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)   # xóa phần tử đầu tiên (ít dùng nhất)


# ============================================================
# Subarray Sum = K — HashMap + Prefix Sum
# ============================================================

def subarray_sum(nums, k):
    """
    Đếm số subarray có tổng bằng k.
    
    Insight: prefix[j] - prefix[i] = k → cần tìm prefix[i] = prefix[j] - k
    Dùng HashMap đếm số lần xuất hiện của mỗi prefix sum.
    
    Time: O(n), Space: O(n)
    """
    count = 0
    prefix = 0
    freq = {0: 1}   # prefix sum 0 đã xuất hiện 1 lần (trước khi duyệt)
    
    for num in nums:
        prefix += num
        needed = prefix - k
        count += freq.get(needed, 0)
        freq[prefix] = freq.get(prefix, 0) + 1
    
    return count


# ============================================================
# Top K Frequent — HashMap + Heap
# ============================================================

import heapq
from collections import Counter

def top_k_frequent(nums, k):
    """
    Tìm k phần tử xuất hiện nhiều nhất.
    
    Approach: Count → min-heap size k
    Time: O(n log k), Space: O(n)
    """
    freq = Counter(nums)
    
    # min-heap: giữ k phần tử lớn nhất
    heap = []
    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)   # loại phần tử nhỏ nhất
    
    return [num for count, num in heap]


# Demo
lru = LRUCache(2)
lru.put(1, 1); lru.put(2, 2)
print(lru.get(1))    # 1
lru.put(3, 3)        # xóa key 2 (ít dùng nhất)
print(lru.get(2))    # -1 (đã bị xóa)

print(subarray_sum([1, 1, 1], 2))  # 2
print(top_k_frequent([1,1,1,2,2,3], 2))  # [1, 2]
```

---

## 10. Khi nào dùng / Không dùng

**Dùng HashMap khi:**
- Cần tìm kiếm theo key tùy ý, không phải theo index → `dict`
- Đếm tần suất, nhóm phần tử → `Counter`, `defaultdict`
- Cần tra cứu O(1) trong Two Sum, caching
- Implement graph dạng adjacency list
- Memoization trong đệ quy

**Dùng HashSet khi:**
- Chỉ cần biết "tồn tại hay không" — không cần lưu value
- Loại bỏ duplicate: `list(set(arr))`
- Kiểm tra intersection, union: `set_a & set_b`, `set_a | set_b`

**Không dùng HashMap/Set khi:**
- Cần duyệt theo thứ tự sorted → dùng `SortedDict` hoặc BST
- Cần range query ("tất cả key từ A đến B") → dùng BST
- Key không hashable (list, dict) → không thể dùng
- Memory rất hạn chế → HashMap overhead nhiều hơn Array

---

## 11. So sánh với Alternatives

| Cấu trúc | Lookup | Insert | Delete | Ordered | Memory |
|----------|--------|--------|--------|---------|--------|
| HashMap (dict) | O(1) avg | O(1) avg | O(1) avg | Insertion order (Python 3.7+) | Nhiều overhead |
| HashSet (set) | O(1) avg | O(1) avg | O(1) avg | Không | Ít hơn dict |
| Array | O(n) | O(n) | O(n) | Index order | Tối thiểu |
| Sorted Array | O(log n) | O(n) | O(n) | Sorted | Tối thiểu |
| BST (balanced) | O(log n) | O(log n) | O(log n) | Sorted | Trung bình |
| Trie | O(L) | O(L) | O(L) | Lexicographic | Lớn |

**Quy tắc chọn:**
- Cần O(1) lookup + không quan tâm thứ tự → HashMap
- Cần O(log n) lookup + thứ tự sorted → BST / SortedDict
- Cần prefix/string lookup → Trie

---

## 12. Pitfalls & Câu hỏi phỏng vấn

### Common Pitfalls

**Pitfall 1: Dùng mutable object làm key**
```python
# LỖI: list không hashable
d = {}
d[[1, 2]] = "value"   # TypeError: unhashable type: 'list'

# ĐÚNG: dùng tuple thay vì list
d[(1, 2)] = "value"   # OK
```

**Pitfall 2: Nhầm worst case với average case**
```python
# HashMap lookup KHÔNG guarantee O(1)
# Average: O(1), Worst: O(n) khi tất cả collision
# Trong phỏng vấn, nói "O(1) average" hoặc "O(1) expected"
```

**Pitfall 3: Không khởi tạo default cho counter**
```python
# LỖI: KeyError nếu key chưa tồn tại
freq = {}
freq["apple"] += 1   # KeyError!

# ĐÚNG — 3 cách:
freq["apple"] = freq.get("apple", 0) + 1
# hoặc
from collections import defaultdict
freq = defaultdict(int)
freq["apple"] += 1
# hoặc
from collections import Counter
freq = Counter(["apple", "apple", "banana"])
```

**Pitfall 4: Set vs List — nhầm khi cần thứ tự**
```python
# Set không đảm bảo thứ tự
s = {3, 1, 4, 1, 5}
print(s)   # {1, 3, 4, 5} — không có thứ tự nhất định

# Nếu cần unique VÀ giữ thứ tự:
seen = set()
result = []
for x in [3, 1, 4, 1, 5]:
    if x not in seen:
        seen.add(x)
        result.append(x)
# result = [3, 1, 4, 5]
```

### Câu hỏi phỏng vấn thường gặp

**Q1: HashMap hoạt động bên trong như thế nào?**
→ Hash function chuyển key thành index. Lưu vào bucket. Khi collision: Python dùng open addressing (probing), Java dùng chaining (linked list). Load factor > threshold → resize và rehash.

**Q2: Tại sao key của dict phải hashable?**
→ Hash function cần một giá trị bất biến. Nếu key thay đổi sau khi insert, hash của nó thay đổi → không tìm được nữa. Vì vậy chỉ immutable objects (int, str, tuple) mới hashable.

**Q3: Phân biệt `dict.get(k)` và `dict[k]`?**
→ `dict[k]` ném `KeyError` nếu không có. `dict.get(k, default)` trả về `default` (mặc định `None`). Dùng `.get()` khi không chắc key tồn tại.

**Q4: Set intersection O(n) hay O(min(n,m))?**
→ `set_a & set_b`: duyệt qua set nhỏ hơn, kiểm tra từng phần tử trong set lớn hơn → O(min(n,m)).

**Q5: Khi nào nên dùng dict thay vì list để track "đã thấy"?**
→ Khi giá trị cần theo dõi không phải là integer trong range nhỏ. Nếu tracking index 0..n-1, boolean array `visited[n]` hiệu quả hơn. Nếu tracking string, coordinates, object → dict/set.
