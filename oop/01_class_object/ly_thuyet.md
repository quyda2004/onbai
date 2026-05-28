# Class và Object trong OOP

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn muốn đóng rất nhiều chiếc bánh mì. Thay vì mỗi lần làm bạn lại phải nhớ lại công thức từ đầu, bạn viết ra một **công thức (recipe)** — ghi rõ nguyên liệu cần có (bột, đường, trứng...) và các bước làm (nhào bột, nướng, lấy ra...).

- **Class** = Công thức đó (blueprint, khuôn mẫu)
- **Object** = Chiếc bánh mì thực sự bạn làm ra từ công thức (instance)

Mỗi chiếc bánh (object) có thể có màu sắc, kích thước khác nhau, nhưng tất cả đều tuân theo cùng một công thức (class). Bạn có thể tạo ra hàng trăm chiếc bánh từ một công thức.

Trong lập trình, class là khuôn mẫu mô tả một thứ gì đó, còn object là thứ đó tồn tại thực sự trong bộ nhớ máy tính khi chương trình chạy.

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Class** là một user-defined type — một template định nghĩa:
- **Attributes (fields/instance variables)**: trạng thái/dữ liệu của object
- **Methods**: hành vi/logic gắn với object đó

**Object** là một instance của class được cấp phát trên **heap memory** (trong hầu hết ngôn ngữ OOP). Khi bạn gọi `Dog()`, Python:
1. Gọi `__new__()` để cấp phát vùng nhớ cho object mới
2. Gọi `__init__()` để khởi tạo các attribute
3. Trả về reference (con trỏ) đến object trong heap

**Instance variable vs Class variable:**
- **Instance variable**: Thuộc về từng object riêng lẻ, được lưu trong `__dict__` của mỗi object
- **Class variable**: Thuộc về class, chia sẻ giữa tất cả instances (lưu trong `class.__dict__`)

**Memory model quan trọng:** Trong Python, mọi thứ đều là object — kể cả class, function, integer. Biến trong Python là **name binding** (nhãn trỏ đến object), không phải container chứa giá trị.

**Constructor & Destructor:**
- `__init__`: Không phải constructor thực sự (object đã tồn tại trước đó), mà là initializer
- `__new__`: Constructor thực sự, hiếm khi cần override trừ Singleton pattern hay immutable types
- `__del__`: Destructor — **không đảm bảo khi nào được gọi** do GC non-deterministic. Không nên dùng để release critical resources; dùng context manager (`with`) thay thế

**self là gì?** Là reference đến instance hiện tại. Python tự động truyền vào làm argument đầu tiên khi gọi instance method.

---

## Định nghĩa chính xác

**Class**: Là một abstract data type (ADT) do người dùng định nghĩa, đóng gói dữ liệu (attributes) và hành vi (methods) liên quan thành một đơn vị. Class là template/blueprint tại compile-time/definition-time.

**Object**: Là một instance cụ thể của class, tồn tại tại runtime, được cấp phát bộ nhớ riêng biệt. Mỗi object có identity (id), type (class), và value (state).

Theo mô hình Python Data Model: "Everything in Python is an object" — mọi entity đều có identity (id()), type (type()), và value.

---

## Độ phức tạp (Time & Space Complexity)

| Thao tác | Best Case | Average Case | Worst Case | Space Complexity | Ghi chú |
|----------|-----------|--------------|------------|------------------|---------|
| Tạo object (`__init__`) | O(1) | O(1) | O(k) | O(k) | k = số attributes khởi tạo |
| Truy cập attribute | O(1) | O(1) | O(n) | O(1) | Hash lookup trong `__dict__`; worst khi hash collision |
| Gọi method | O(1) | O(1) | O(1) | O(1) | Descriptor protocol lookup |
| Xóa object (GC) | O(1) | O(1) | O(n) | O(1) | CPython dùng reference counting; worst khi cycle detection |

---

## Pseudocode / Code mẫu

```python
# ===== ĐỊNH NGHĨA CLASS =====
class Dog:
    # Class variable: chia sẻ giữa TẤT CẢ instances
    species = "Canis familiaris"
    count = 0  # đếm số dog đã tạo

    # __init__: initializer (không phải constructor thực sự)
    # self = reference đến instance hiện tại
    def __init__(self, name: str, age: int):
        # Instance variables: thuộc về TỪNG object riêng
        self.name = name      # public attribute
        self.age = age        # public attribute
        Dog.count += 1        # tăng class variable

    # Instance method: nhận self, truy cập được instance state
    def bark(self) -> str:
        return f"{self.name} says: Woof!"

    # Class method: nhận cls (class), không cần instance
    @classmethod
    def get_count(cls) -> int:
        return cls.count

    # Static method: không nhận self hay cls — như hàm bình thường nhưng đặt trong namespace class
    @staticmethod
    def is_adult(age: int) -> bool:
        return age >= 2

    # __repr__: dùng cho debugging (unambiguous representation)
    def __repr__(self) -> str:
        return f"Dog(name='{self.name}', age={self.age})"

    # __str__: dùng cho user-facing display
    def __str__(self) -> str:
        return f"{self.name} ({self.age} tuổi)"

    # Destructor: KHÔNG đảm bảo thời điểm gọi
    def __del__(self):
        Dog.count -= 1
        print(f"{self.name} đã bị xóa khỏi bộ nhớ")


# ===== TẠO OBJECTS (INSTANCES) =====
dog1 = Dog("Rex", 3)
dog2 = Dog("Buddy", 1)

print(dog1)                     # Rex (3 tuổi)        — gọi __str__
print(repr(dog1))               # Dog(name='Rex', age=3) — gọi __repr__
print(dog1.bark())              # Rex says: Woof!

# Truy cập class variable qua instance hoặc class
print(dog1.species)             # Canis familiaris
print(Dog.species)              # Canis familiaris

# Class method và static method
print(Dog.get_count())          # 2
print(Dog.is_adult(3))          # True
print(Dog.is_adult(1))          # False

# Kiểm tra instance vs class variable
print(dog1.__dict__)            # {'name': 'Rex', 'age': 3}
print(Dog.__dict__.keys())      # ... 'species', 'count', ...


# ===== INSTANCE VARIABLE vs CLASS VARIABLE — TRAP PHỔ BIẾN =====
class Counter:
    items = []      # CLASS variable — NGUY HIỂM nếu là mutable!

    def add(self, item):
        self.items.append(item)  # Modify class variable chung!


c1 = Counter()
c2 = Counter()
c1.add("apple")
print(c2.items)   # ['apple'] — c2 bị ảnh hưởng! BUG!

# FIX: Khởi tạo mutable trong __init__
class CounterFixed:
    def __init__(self):
        self.items = []   # Instance variable — mỗi object có list riêng

    def add(self, item):
        self.items.append(item)


c1 = CounterFixed()
c2 = CounterFixed()
c1.add("apple")
print(c2.items)   # [] — đúng rồi!
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Cần mô hình hóa một entity có cả trạng thái (data) lẫn hành vi (behavior) liên quan đến nhau
- Cần tạo nhiều instances có cùng cấu trúc nhưng khác state (nhiều User, nhiều Product...)
- Muốn encapsulate logic phức tạp, ẩn implementation detail
- Cần inheritance hoặc polymorphism

**Không dùng khi:**
- Chỉ cần nhóm data đơn giản không có behavior → dùng `dataclass` hoặc `namedtuple`
- Chỉ có một instance duy nhất và không có state → dùng module-level functions
- Logic quá đơn giản, tạo class chỉ làm code phức tạp hơn không cần thiết

---

## So sánh với các khái niệm liên quan

| | Class | Object | Module | Struct (C) |
|-|-------|--------|--------|------------|
| Tồn tại khi | Define-time | Runtime | Import-time | Compile-time |
| Có methods | Có | Có (kế thừa từ class) | Có (functions) | Không |
| Số lượng instances | 1 (class itself) | Nhiều | 1 (singleton) | Nhiều |
| Bộ nhớ | Class object trong heap | Instance trong heap | Module object | Stack/Heap |
| Inheritance | Có | Không | Không | Không |

---

## Lỗi thường gặp (Common Pitfalls)

1. **Mutable class variable**: Dùng list/dict làm class variable → tất cả instances chia sẻ cùng object → bugs khó tìm. **Fix**: Khởi tạo trong `__init__`.

2. **Quên `self`**: Khi gọi method khác trong cùng class, phải viết `self.method()` không phải `method()`.

3. **Nhầm `__str__` và `__repr__`**: `repr()` phải unambiguous (dùng debug), `str()` cho display. Nếu chỉ implement một, implement `__repr__`.

4. **Rely on `__del__`**: Destructor không đảm bảo thời điểm gọi → đừng dùng để close file/connection. Dùng `with` statement.

5. **Shallow copy vs Deep copy**: `copy.copy()` chỉ copy references, không copy nested objects. Dùng `copy.deepcopy()` khi cần.

6. **`is` vs `==`**: `is` kiểm tra identity (cùng object), `==` kiểm tra equality (gọi `__eq__`). `dog1 is dog2` thường `False` dù chúng có cùng name/age.

---

## Câu hỏi phỏng vấn hay gặp

1. **Sự khác nhau giữa class variable và instance variable?** — Class variable chia sẻ giữa tất cả instances; instance variable thuộc về từng object riêng. Nếu class variable là mutable type, các instances sẽ share cùng object.

2. **`__init__` vs `__new__` khác nhau như thế nào?** — `__new__` tạo object (allocate memory), `__init__` khởi tạo object đã có. `__new__` trả về instance, `__init__` trả về None.

3. **Instance method vs Class method vs Static method?** — Instance method nhận `self`, truy cập instance state. Class method nhận `cls`, dùng cho factory methods hay truy cập class state. Static method không nhận gì, là utility function thuộc namespace class.

4. **Python `self` là gì, tại sao phải viết tường minh?** — `self` là convention (có thể đặt tên khác), Python truyền tự động khi gọi instance method. Explicit hơn so với C++/Java để rõ ràng về ownership.

5. **Tại sao không nên dùng mutable default argument trong `__init__`?** — `def __init__(self, items=[])`: list `[]` được tạo một lần lúc define function, không phải mỗi lần gọi → tất cả instances share cùng list.
