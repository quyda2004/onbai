# Encapsulation (Đóng gói)

---

## Giải thích cho người mới hoàn toàn

Hãy nghĩ đến chiếc máy ATM. Khi bạn rút tiền, bạn chỉ cần nhập số tiền và nhấn nút — bạn **không cần biết** bên trong máy có bao nhiêu tờ tiền, cơ chế đếm tiền hoạt động ra sao, hay kết nối với ngân hàng như thế nào.

ATM **ẩn** tất cả chi tiết phức tạp bên trong và chỉ cho bạn dùng qua một giao diện đơn giản (màn hình, bàn phím). Đây chính là **encapsulation** — đóng gói dữ liệu và logic bên trong, chỉ để lộ ra những gì cần thiết.

Thêm nữa, ATM **bảo vệ** dữ liệu: bạn không thể tự ý chỉnh số dư tài khoản, bạn phải đi qua quy trình kiểm tra PIN, xác thực... Encapsulation bảo vệ dữ liệu khỏi bị truy cập hay thay đổi bừa bãi từ bên ngoài.

---

## Giải thích cho người đã biết lập trình (nâng cao)

Encapsulation là nguyên tắc **bundling data (attributes) và methods operating on that data** vào cùng một unit (class), đồng thời **kiểm soát access** đến các thành phần đó.

**Access Modifiers trong các ngôn ngữ:**

| Ngôn ngữ | Public | Protected | Private |
|----------|--------|-----------|---------|
| Java/C++ | `public` | `protected` | `private` |
| Python | `name` | `_name` (convention) | `__name` (name mangling) |
| C# | `public` | `protected` | `private` |

**Python name mangling**: `__attr` trong class `Foo` trở thành `_Foo__attr` — vẫn có thể truy cập trực tiếp nhưng không phải vô tình. Đây **không phải** true private như Java.

**Property vs Getter/Setter:**
- Getter/setter truyền thống: `get_age()`, `set_age(value)` — Java style
- Python `@property`: truy cập như attribute nhưng thực chất là method call — Pythonic hơn
- Property cho phép thêm validation, lazy loading, computed value mà không thay đổi public API

**Tại sao encapsulation quan trọng trong production?**
1. **Invariant protection**: Đảm bảo object luôn ở trạng thái hợp lệ (age không âm, email có @...)
2. **API stability**: Thay đổi internal implementation không break code người dùng
3. **Reduced coupling**: Code bên ngoài không depend vào internal structure
4. **Easier debugging**: Bug chỉ có thể xảy ra ở những chỗ có quyền modify state

**Vi phạm encapsulation (Law of Demeter):**
```python
# VI PHẠM — "train wreck": chuỗi dài truy cập internal
user.address.city.zip_code

# TỐT HƠN — expose only what's needed
user.get_zip_code()
```

---

## Định nghĩa chính xác

**Encapsulation** là cơ chế OOP bundling data và methods thao tác trên data đó vào một đơn vị (class), đồng thời hạn chế truy cập trực tiếp vào các thành phần internal của object. Encapsulation đạt được thông qua **access modifiers** và **interface methods**, nhằm duy trì **object invariants** và giảm **coupling** giữa các components.

Theo định nghĩa của Grady Booch: "Encapsulation is the process of compartmentalizing the elements of an abstraction that constitute its structure and behavior."

---

## Độ phức tạp

| Thao tác | Không encapsulation | Với Property/Getter | Ghi chú |
|----------|--------------------|--------------------|---------|
| Đọc attribute | O(1) | O(1) + overhead nhỏ | Property là function call thêm |
| Ghi attribute | O(1) | O(1) + validation | Validation có thể là O(n) nếu check unique |
| Name mangling lookup | O(1) | O(1) | CPython dùng dict lookup |

**Overhead của Python `@property`:** Rất nhỏ (~microseconds), đủ Pythonic để không cần lo về performance. Chỉ lo khi gọi trong tight loop hàng triệu lần.

---

## Pseudocode / Code mẫu

```python
# ===== ENCAPSULATION VỚI PYTHON =====

class BankAccount:
    """Ví dụ encapsulation đầy đủ"""

    # Class variable
    _interest_rate = 0.05  # Protected by convention

    def __init__(self, owner: str, initial_balance: float = 0):
        self._owner = owner              # Protected: internal use + subclasses
        self.__balance = 0.0             # Private: chỉ class này dùng
        self.__transaction_history = []  # Private mutable state

        # Dùng setter để validate ngay từ đầu
        if initial_balance < 0:
            raise ValueError("Initial balance cannot be negative")
        self.__balance = initial_balance

    # === PROPERTY: truy cập như attribute, thực chất là method ===

    @property
    def balance(self) -> float:
        """Getter: read-only property"""
        return self.__balance

    @property
    def owner(self) -> str:
        return self._owner

    # Không có setter cho balance — chỉ qua deposit/withdraw

    # === METHODS ĐỂ THAY ĐỔI STATE (kiểm soát) ===

    def deposit(self, amount: float) -> None:
        """Validation trước khi thay đổi state"""
        if amount <= 0:
            raise ValueError(f"Deposit amount must be positive, got {amount}")
        self.__balance += amount
        self.__transaction_history.append(("deposit", amount))

    def withdraw(self, amount: float) -> None:
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive")
        if amount > self.__balance:
            raise ValueError(f"Insufficient funds: balance={self.__balance}, requested={amount}")
        self.__balance -= amount
        self.__transaction_history.append(("withdraw", amount))

    def get_history(self) -> list:
        """Trả về COPY để tránh bên ngoài mutate list nội bộ"""
        return self.__transaction_history.copy()

    def __repr__(self):
        return f"BankAccount(owner='{self._owner}', balance={self.__balance:.2f})"


# === SỬ DỤNG ===
acc = BankAccount("Alice", 1000)
print(acc.balance)      # 1000.0  — qua property
print(acc.owner)        # Alice

acc.deposit(500)
acc.withdraw(200)
print(acc.balance)      # 1300.0

# Thử vi phạm — vẫn có thể, nhưng không vô tình
# acc.balance = 9999    # AttributeError: can't set attribute (no setter)
# acc.__balance         # AttributeError: name mangling
print(acc._BankAccount__balance)  # 1300.0 — CÓ THỂ nhưng "chúng ta đã ký hợp đồng không làm vậy"

print(acc.get_history())  # [('deposit', 500), ('withdraw', 200)]


# ===== SO SÁNH: CÓ VÀ KHÔNG CÓ ENCAPSULATION =====

class BadAccount:
    """Vi phạm encapsulation — công khai mọi thứ"""
    def __init__(self, balance):
        self.balance = balance  # Public — ai cũng sửa được


bad = BadAccount(1000)
bad.balance = -99999   # Không có validation — nguy hiểm!
bad.balance = "hello"  # Thậm chí type sai cũng không bắt được


# ===== PROPERTY VỚI VALIDATION =====

class Person:
    def __init__(self, name: str, age: int):
        self.name = name   # Dùng setter ngay trong __init__
        self.age = age     # Dùng setter ngay trong __init__

    @property
    def age(self) -> int:
        return self._age

    @age.setter
    def age(self, value: int) -> None:
        if not isinstance(value, int):
            raise TypeError(f"Age must be int, got {type(value)}")
        if value < 0 or value > 150:
            raise ValueError(f"Age {value} is not realistic")
        self._age = value

    @property
    def name(self) -> str:
        return self._name

    @name.setter
    def name(self, value: str) -> None:
        if not value or not value.strip():
            raise ValueError("Name cannot be empty")
        self._name = value.strip()


p = Person("Alice", 30)
print(p.age)    # 30
p.age = 31      # OK
# p.age = -5   # ValueError: Age -5 is not realistic
# p.age = 200  # ValueError


# ===== COMPUTED PROPERTY (không lưu, tính khi cần) =====

import math

class Circle:
    def __init__(self, radius: float):
        self.radius = radius

    @property
    def area(self) -> float:
        """Computed property — tính mỗi lần đọc, không lưu"""
        return math.pi * self.radius ** 2

    @property
    def circumference(self) -> float:
        return 2 * math.pi * self.radius


c = Circle(5)
print(f"Area: {c.area:.2f}")           # Area: 78.54
print(f"Circumference: {c.circumference:.2f}")  # Circumference: 31.42
c.radius = 10
print(f"New area: {c.area:.2f}")       # New area: 314.16 — tự cập nhật!
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Object có invariants cần bảo vệ (balance không âm, age hợp lệ, list không rỗng...)
- Cần thay đổi internal implementation mà không break external API
- Nhiều team/module dùng chung class → cần ổn định interface
- Cần validation khi set attribute
- Attribute phức tạp (computed, lazy-loaded, cached)

**Không dùng khi (over-encapsulation):**
- Simple data containers (dùng `dataclass` hoặc `namedtuple` thay thế)
- Getter/setter không làm gì ngoài get/set thuần túy → unnecessary boilerplate
- Script nhỏ, prototyping → private/public không quan trọng
- Performance-critical code với hàng triệu property accesses/giây

---

## So sánh với các khái niệm liên quan

| | Encapsulation | Abstraction | Information Hiding |
|-|---------------|-------------|-------------------|
| Mục tiêu | Bundling + access control | Ẩn complexity, expose interface | Giấu implementation details |
| Thực hiện | Access modifiers, properties | Abstract class, interface | Private members |
| Tập trung | **Cách** bảo vệ dữ liệu | **Cái gì** được expose | **Tại sao** ẩn chi tiết |
| Ví dụ | `__balance` + `deposit()` | `Shape.area()` (abstract) | Giấu algorithm nội bộ |

---

## Lỗi thường gặp (Common Pitfalls)

1. **Quên dùng setter trong `__init__`**: `self._age = age` bỏ qua validation trong setter. Đúng: `self.age = age` (qua property setter).

2. **Trả về mutable internal state trực tiếp**: `return self.__items` — code bên ngoài có thể mutate list nội bộ. **Fix**: `return self.__items.copy()` hoặc `return tuple(self.__items)`.

3. **Over-engineering**: Tạo getter/setter cho mọi attribute dù không có logic → Java anti-pattern trong Python. Python zen: nếu không có validation/logic, dùng public attribute thẳng.

4. **Name mangling nhầm lẫn**: `__attr` không phải truly private. Có thể truy cập qua `_ClassName__attr`. Không nên dùng để "security" — chỉ để tránh name collision trong inheritance.

5. **Computed property expensive không cache**: Nếu `area` cần tính phức tạp và được đọc thường xuyên → dùng `@functools.cached_property` để cache.

6. **Setter không check type**: Chỉ check range mà quên check type → `p.age = "30"` pass validation nhưng crash sau.

---

## Câu hỏi phỏng vấn hay gặp

1. **Encapsulation là gì và tại sao quan trọng?** — Bundling data + methods + access control. Quan trọng vì: bảo vệ invariants, giảm coupling, tăng maintainability.

2. **Python có truly private không?** — Không. `__attr` dùng name mangling thành `_ClassName__attr`, vẫn truy cập được. Python tin vào "we're all consenting adults here" — convention thay vì enforcement.

3. **Khi nào dùng `@property` thay vì public attribute?** — Khi cần validation, computed value, lazy loading, hoặc muốn giữ API ổn định khi thay đổi implementation.

4. **Sự khác biệt giữa `_` và `__` prefix?** — `_name`: protected by convention, cho subclass và internal use. `__name`: name mangling, tránh name collision trong inheritance hierarchy, không phải security.

5. **Law of Demeter là gì liên quan đến encapsulation?** — "Only talk to your immediate friends": `a.method()` tốt hơn `a.b.c.method()`. Chuỗi truy cập dài vi phạm encapsulation vì nó depend vào internal structure của nhiều objects.
