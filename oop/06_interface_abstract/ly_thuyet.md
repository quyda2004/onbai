# Interface & Abstract Class

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đang xây một tòa nhà. Bạn cần điện, nước, internet — nhưng bạn không cần biết điện đến từ nhà máy nào, nước từ nguồn nào. Bạn chỉ cần có **ổ cắm điện tiêu chuẩn**, **vòi nước tiêu chuẩn**, và **cổng RJ45 tiêu chuẩn**.

Những **"tiêu chuẩn"** đó chính là **Interface**: *một bản hợp đồng mô tả "phải làm được gì"*, không quan tâm làm thế nào.

**Abstract Class** thì giống như **bản thiết kế nhà** có sẵn một số phần hoàn chỉnh (tường, móng) nhưng để trống một số phần (nội thất) cho người mua tự chọn.

- **Interface**: Chỉ nói "cần làm gì" — không có code thực thi.
- **Abstract Class**: Có một phần code thực, một phần để lớp con hoàn thiện.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Python — ABC Module

Python không có keyword `interface` như Java/C#. Thay vào đó:

- **Abstract Class**: Dùng `class Foo(ABC)` + `@abstractmethod` từ module `abc`.
- **Interface (Protocol)**: Dùng `typing.Protocol` (Python 3.8+) để implement **structural subtyping** (duck typing có kiểm tra tĩnh).

### ABC — Abstract Base Class

`ABC` là cú pháp đường (syntactic sugar) cho `metaclass=ABCMeta`. Khi class có ít nhất 1 `@abstractmethod`, Python ngăn không cho instantiate trực tiếp.

**Các decorator trong ABC:**
- `@abstractmethod` — method bình thường phải override
- `@property` + `@abstractmethod` — abstract property
- `@classmethod` + `@abstractmethod` — abstract classmethod
- `@staticmethod` + `@abstractmethod` — abstract staticmethod

### Protocol — Structural Subtyping

`Protocol` (từ `typing`) khác với ABC ở chỗ: không cần kế thừa — chỉ cần **có đúng method và attribute là đủ** (structural/duck typing có type hint support).

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...

class Circle:
    def draw(self): print("Drawing circle")

# Circle KHÔNG kế thừa Drawable
# Nhưng vẫn thỏa mãn Drawable Protocol
def render(obj: Drawable) -> None:
    obj.draw()

render(Circle())  # Valid!
```

### Java 8+ — Interface với Default Method

Java 8 thêm `default` method vào interface, làm mờ ranh giới với abstract class:

```java
interface Flyable {
    void fly();                          // Abstract
    default String getType() {           // Default implementation
        return "Generic Flyer";
    }
}
```

### Multiple Interface Implementation

Một class có thể implement **nhiều interface** nhưng chỉ kế thừa từ **một abstract class** (trong Java/C#). Python linh hoạt hơn: hỗ trợ multiple inheritance.

### Mixin Pattern — Python-specific

Mixin là class nhỏ cung cấp tính năng cụ thể, không có `__init__`, không dùng standalone. Đây là cách Python mô phỏng multiple interface:

```python
class JSONMixin:
    def to_json(self): ...

class XMLMixin:
    def to_xml(self): ...

class User(JSONMixin, XMLMixin):
    pass  # User có cả to_json() và to_xml()
```

---

## Định nghĩa chính xác

**Abstract Class**: Lớp không thể instantiate trực tiếp, chứa ít nhất một abstract method. Cung cấp partial implementation và enforce interface cho subclass. Quan hệ IS-A.

**Interface**: Một contract thuần túy — chỉ khai báo method signatures, không có implementation. Trong Python được thực hiện qua ABC thuần (chỉ `@abstractmethod`, không có concrete method) hoặc `typing.Protocol`.

**Protocol (Python 3.8+)**: Structural subtyping — class thỏa mãn Protocol nếu có đúng cấu trúc (method/attribute), không cần explicitly inherit.

---

## So sánh / Bảng kỹ thuật

### Abstract Class vs Interface vs Protocol

| Tiêu chí | Abstract Class | Interface (ABC thuần) | Protocol (typing) |
|----------|----------------|----------------------|-------------------|
| Có implementation | Có (partial) | Không | Không |
| Kế thừa | IS-A, phải subclass | IS-A, phải subclass | KHÔNG cần kế thừa |
| Multiple | Python: có; Java: không | Java/C#: có | Có |
| Type checking | Runtime (`isinstance`) | Runtime | Static (mypy/pyright) + Runtime |
| Python version | 2.6+ (abc module) | 2.6+ (abc module) | 3.8+ |
| Phù hợp | Shared code + enforce contract | Chỉ enforce contract | Duck typing có type safety |

### So sánh Java, Python, C#

| | Python | Java | C# |
|-|--------|------|-----|
| Abstract class | `class Foo(ABC)` | `abstract class Foo` | `abstract class Foo` |
| Interface | Không có keyword | `interface IFoo` | `interface IFoo` |
| Protocol | `Protocol` (typing) | Không có | Không có |
| Multiple inheritance | Có | Không (chỉ interface) | Không (chỉ interface) |
| Default method | Qua concrete method trong ABC | Java 8+: `default` | C# 8+: default interface method |

### Độ phức tạp

| Thao tác | Best Case | Worst Case | Space | Ghi chú |
|----------|-----------|------------|-------|---------|
| Abstract method check khi define class | O(m) | O(m) | O(m) | m = số abstract methods |
| Instantiate check | O(m) | O(m) | O(1) | Verify tất cả abstract methods được implement |
| Protocol isinstance check | O(n) | O(n) | O(1) | n = số method trong Protocol; dùng `runtime_checkable` |

---

## Code mẫu

```python
from abc import ABC, abstractmethod
from typing import Protocol, runtime_checkable

# ===== ABSTRACT CLASS với shared implementation =====

class Animal(ABC):
    """Abstract class: có shared implementation + abstract methods"""

    def __init__(self, name: str):
        self.name = name           # Shared attribute

    @abstractmethod
    def speak(self) -> str:        # Subclass phải implement
        pass

    @property
    @abstractmethod
    def habitat(self) -> str:      # Abstract property
        pass

    def breathe(self) -> str:      # Concrete method — dùng chung
        return f"{self.name} đang thở"

    def describe(self) -> str:     # Template method
        return f"{self.name} ({self.habitat}): {self.speak()}"


class Dog(Animal):
    def speak(self) -> str:
        return "Woof!"

    @property
    def habitat(self) -> str:
        return "đất liền"


class Fish(Animal):
    def speak(self) -> str:
        return "..."

    @property
    def habitat(self) -> str:
        return "dưới nước"


# ===== INTERFACE THUẦN (ABC không có concrete method) =====

class Flyable(ABC):
    """Pure interface — chỉ khai báo contract"""

    @abstractmethod
    def fly(self) -> str:
        pass

    @abstractmethod
    def land(self) -> str:
        pass


class Swimmable(ABC):
    """Pure interface"""

    @abstractmethod
    def swim(self) -> str:
        pass

    @abstractmethod
    def dive(self, depth: float) -> str:
        pass


# ===== MULTIPLE INTERFACE IMPLEMENTATION =====

class Duck(Animal, Flyable, Swimmable):
    """Duck implement cả Flyable VÀ Swimmable"""

    def speak(self) -> str:
        return "Quack!"

    @property
    def habitat(self) -> str:
        return "cả đất và nước"

    def fly(self) -> str:
        return f"{self.name} bay vỗ cánh"

    def land(self) -> str:
        return f"{self.name} đáp xuống nước"

    def swim(self) -> str:
        return f"{self.name} bơi trên mặt nước"

    def dive(self, depth: float) -> str:
        return f"{self.name} lặn xuống {depth}m"


# Demo multiple interface
donald = Duck("Donald")
print(donald.describe())           # Template method từ Animal
print(donald.fly())                # Từ Flyable
print(donald.swim())               # Từ Swimmable
print(donald.breathe())            # Concrete method từ Animal

# isinstance checks — Duck thỏa mãn cả 3
print(isinstance(donald, Animal))    # True
print(isinstance(donald, Flyable))   # True
print(isinstance(donald, Swimmable)) # True


# ===== PROTOCOL — Structural subtyping =====

@runtime_checkable
class Drawable(Protocol):
    """Protocol: không cần kế thừa, chỉ cần có method draw()"""
    def draw(self) -> None: ...
    def get_color(self) -> str: ...


class Circle:
    """KHÔNG kế thừa Drawable, nhưng có đúng method"""
    def draw(self) -> None:
        print("Drawing circle")

    def get_color(self) -> str:
        return "red"


class Square:
    """Cũng không kế thừa, nhưng thỏa Protocol"""
    def draw(self) -> None:
        print("Drawing square")

    def get_color(self) -> str:
        return "blue"


def render_all(items: list[Drawable]) -> None:
    """Type hint dùng Protocol — linh hoạt hơn ABC"""
    for item in items:
        print(f"Color: {item.get_color()}", end=" | ")
        item.draw()

render_all([Circle(), Square()])   # Không cần kế thừa chung

# runtime_checkable cho phép isinstance check
print(isinstance(Circle(), Drawable))  # True


# ===== MIXIN PATTERN =====

class JSONMixin:
    """Mixin thêm JSON serialization — không dùng standalone"""
    def to_json(self) -> str:
        import json
        return json.dumps(self.__dict__, default=str)


class LogMixin:
    """Mixin thêm logging"""
    def log(self, message: str) -> None:
        print(f"[{self.__class__.__name__}] {message}")


class User(JSONMixin, LogMixin):
    def __init__(self, name: str, email: str):
        self.name = name
        self.email = email

u = User("Alice", "alice@example.com")
print(u.to_json())           # {"name": "Alice", "email": "alice@example.com"}
u.log("User created")        # [User] User created


# ===== Không thể instantiate abstract class =====
try:
    a = Animal("Generic")
except TypeError as e:
    print(f"Lỗi: {e}")
    # Can't instantiate abstract class Animal with abstract methods habitat, speak
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng Abstract Class khi:**
- Có **shared implementation** cần tái sử dụng giữa các subclass.
- Muốn vừa enforce interface vừa cung cấp default behavior.
- Quan hệ IS-A rõ ràng và các subclass thuộc cùng một "family".
- Dùng Template Method Pattern.

**Dùng Interface (ABC thuần / Protocol) khi:**
- Chỉ cần **define contract**, không cần share code.
- Một class cần "implement" nhiều contract khác nhau.
- Muốn loose coupling tối đa — consumer không phụ thuộc implementation hierarchy.

**Dùng Protocol khi:**
- Làm việc với third-party classes không thể sửa để thêm kế thừa.
- Muốn duck typing có static type checking (mypy/pyright).
- Viết generic functions linh hoạt hơn ABC.

**Không dùng khi:**
- Over-engineering — class đơn giản không cần contract formal.
- Khi inheritance thông thường + method override đã đủ.

---

## Lỗi thường gặp (Common Pitfalls)

- **Implement không đầy đủ abstract methods**: Python raise `TypeError` khi instantiate — dễ debug, nhưng nếu partial implementation thì cũng không được.
- **Dùng ABC khi Protocol là đủ**: Nếu không cần shared code, Protocol linh hoạt hơn và không yêu cầu kế thừa.
- **Không dùng `@runtime_checkable` với Protocol**: `isinstance()` sẽ raise `TypeError` với Protocol không được đánh dấu `@runtime_checkable`.
- **Abstract class quá "béo"**: Abstract class với quá nhiều abstract methods vi phạm Interface Segregation Principle — tách thành nhiều interface nhỏ hơn.
- **Nhầm thứ tự decorator**: `@property` + `@abstractmethod` phải đúng thứ tự — property trước, abstractmethod sau.
- **Dùng Mixin không đúng**: Mixin không nên có `__init__` phụ thuộc state. Nếu Mixin cần state, cân nhắc dùng Composition thay thế.

---

## Câu hỏi phỏng vấn hay gặp

1. **Abstract class vs Interface — khi nào dùng cái nào?** Abstract class = shared code + enforce. Interface = chỉ contract. Trong Python: Protocol cho structural subtyping, ABC cho behavioral hierarchy.

2. **Python có interface không?** Không có keyword `interface`, nhưng có 2 cách: ABC thuần (nominal typing) và `typing.Protocol` (structural typing).

3. **Structural typing vs Nominal typing?** Nominal: phải explicitly kế thừa để thỏa mãn interface. Structural: chỉ cần có đúng structure (method/attribute).

4. **Tại sao dùng `@runtime_checkable` với Protocol?** Cho phép `isinstance()` check tại runtime. Mặc định Protocol chỉ cho static checking (mypy).

5. **Multiple inheritance vs multiple interface implementation?** Python hỗ trợ cả hai qua multiple inheritance. Java chỉ cho implement nhiều interface. Diamond problem được giải bằng MRO trong Python.

6. **Khi nào dùng Mixin?** Khi muốn thêm tính năng reusable vào nhiều class không liên quan nhau mà không cần kế thừa. Mixin nhỏ, focused, không standalone.

7. **Protocol trong Python 3.8+ có gì đặc biệt?** Hỗ trợ structural subtyping — không cần kế thừa. Tương thích với mypy/pyright. Với `@runtime_checkable` có thể dùng `isinstance()`.
