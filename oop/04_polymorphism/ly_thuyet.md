# Polymorphism (Đa hình)

---

## Giải thích cho người mới hoàn toàn

Hãy nghĩ đến nút **"Phát"** trên các thiết bị khác nhau:
- Nhấn "Phát" trên **TV** → video chạy
- Nhấn "Phát" trên **máy nghe nhạc** → nhạc phát
- Nhấn "Phát" trên **máy chiếu** → slide chạy

Cùng một hành động "Phát", nhưng **mỗi thiết bị thực hiện theo cách riêng của mình**. Bạn không cần biết bên trong chúng khác nhau thế nào — bạn chỉ nhấn nút.

Đó là Polymorphism: **cùng một tên hành động, nhưng các đối tượng khác nhau thực hiện theo cách khác nhau**.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Runtime Polymorphism vs Compile-time Polymorphism

| Loại | Tên khác | Cơ chế | Ngôn ngữ |
|------|----------|--------|----------|
| **Runtime** | Dynamic dispatch, Method overriding | Virtual method table (vtable) quyết định tại runtime | Python, Java, C++ (virtual) |
| **Compile-time** | Static dispatch, Method overloading | Compiler chọn method dựa trên signature | Java, C++, C# |

Python **chỉ có runtime polymorphism**. Mọi method call đều được resolve tại runtime thông qua MRO — không có overloading tĩnh.

### Duck Typing trong Python

> *"Nếu nó đi như vịt và kêu như vịt, thì nó là vịt."*

Python không yêu cầu object phải thuộc cùng hierarchy. Miễn là object **có method được gọi**, Python sẽ gọi được — không cần `isinstance()` check.

```python
class Dog:
    def speak(self): return "Woof"

class Cat:
    def speak(self): return "Meow"

class Robot:
    def speak(self): return "Beep boop"

# Không cần chung base class — duck typing
for animal in [Dog(), Cat(), Robot()]:
    print(animal.speak())   # Mỗi object tự biết làm gì
```

### Dynamic Dispatch — Cơ chế bên trong

Khi Python gọi `obj.method()`:
1. Tra cứu `method` trong `obj.__dict__`
2. Không tìm thấy → leo lên `type(obj).__dict__`
3. Tiếp tục theo MRO cho đến `object`

Đây là **attribute lookup protocol** của Python, làm cho tất cả method call đều là "virtual" theo nghĩa C++.

### isinstance() và type checking

- `isinstance(obj, cls)` — kiểm tra IS-A, hỗ trợ inheritance chain. **Ưu tiên dùng cái này.**
- `type(obj) == cls` — kiểm tra exact type, **không** nhận subclass. Tránh dùng trừ khi cần.
- `hasattr(obj, 'method')` — kiểm tra duck typing, phù hợp hơn với triết lý Python.

### Liskov Substitution Principle (LSP) và Polymorphism

LSP (chữ L trong SOLID) phát biểu: *"Bất kỳ nơi nào dùng được lớp cha, phải dùng được lớp con mà không vỡ logic."*

Polymorphism chỉ hoạt động đúng khi LSP được tuân thủ. Vi phạm LSP = polymorphism hoạt động nhưng cho kết quả sai.

### Abstract Method tạo Polymorphism có hợp đồng

`@abstractmethod` buộc mọi subclass phải implement method → đảm bảo polymorphism "an toàn" hơn duck typing thuần.

---

## Định nghĩa chính xác

**Polymorphism** (đa hình) là khả năng của các đối tượng thuộc các lớp khác nhau **phản hồi cùng một interface (tên method/operator)** theo những cách khác nhau, phù hợp với kiểu dữ liệu hoặc lớp của từng đối tượng. Có hai dạng chính: *ad-hoc polymorphism* (overloading) và *subtype polymorphism* (overriding).

---

## So sánh / Bảng kỹ thuật

### Runtime vs Compile-time Polymorphism

| Tiêu chí | Runtime (Overriding) | Compile-time (Overloading) |
|----------|---------------------|---------------------------|
| Resolve tại | Runtime | Compile-time |
| Cơ chế | Virtual method / MRO | Signature matching |
| Python hỗ trợ | Có (tất cả method) | Không (chỉ mô phỏng) |
| Java hỗ trợ | Có (`@Override`) | Có (overloading thực sự) |
| Tính linh hoạt | Cao | Thấp hơn |

### Độ phức tạp

| Thao tác | Best Case | Average Case | Worst Case | Space | Ghi chú |
|----------|-----------|--------------|------------|-------|---------|
| Dynamic dispatch (Python MRO) | O(1) | O(1) | O(d) | O(1) | d = độ sâu MRO; thường O(1) nhờ cache |
| `isinstance()` check | O(d) | O(d) | O(d) | O(1) | d = chiều sâu hierarchy |
| `hasattr()` | O(1) | O(1) | O(n) | O(1) | n = số lớp trong MRO |

---

## Code mẫu

```python
from abc import ABC, abstractmethod
import math

# ===== RUNTIME POLYMORPHISM với Abstract Class =====

class Shape(ABC):
    """Base class — định nghĩa interface chung"""

    def __init__(self, color: str = "white"):
        self.color = color

    @abstractmethod
    def area(self) -> float:
        """Buộc mọi subclass phải implement"""
        pass

    @abstractmethod
    def perimeter(self) -> float:
        pass

    def describe(self) -> str:
        # Template method — dùng chung, gọi method bị override
        return (f"{self.__class__.__name__} màu {self.color}: "
                f"diện tích={self.area():.2f}, chu vi={self.perimeter():.2f}")


class Circle(Shape):
    def __init__(self, radius: float, color: str = "white"):
        super().__init__(color)
        self.radius = radius

    def area(self) -> float:
        return math.pi * self.radius ** 2      # Override

    def perimeter(self) -> float:
        return 2 * math.pi * self.radius       # Override


class Rectangle(Shape):
    def __init__(self, width: float, height: float, color: str = "white"):
        super().__init__(color)
        self.width = width
        self.height = height

    def area(self) -> float:
        return self.width * self.height        # Override khác với Circle

    def perimeter(self) -> float:
        return 2 * (self.width + self.height)


class Triangle(Shape):
    def __init__(self, a: float, b: float, c: float):
        super().__init__()
        self.a, self.b, self.c = a, b, c

    def area(self) -> float:
        s = self.perimeter() / 2               # Heron's formula
        return math.sqrt(s * (s-self.a) * (s-self.b) * (s-self.c))

    def perimeter(self) -> float:
        return self.a + self.b + self.c


# ----- Dynamic dispatch: cùng lời gọi, khác hành vi -----
shapes: list[Shape] = [
    Circle(radius=5, color="đỏ"),
    Rectangle(width=4, height=6, color="xanh"),
    Triangle(a=3, b=4, c=5),
]

print("=== Polymorphism qua abstract method ===")
for shape in shapes:
    print(shape.describe())
    # Python tự chọn đúng area()/perimeter() của từng class tại runtime

# Tính tổng diện tích — không cần biết kiểu cụ thể
total_area = sum(shape.area() for shape in shapes)
print(f"\nTổng diện tích: {total_area:.2f}")


# ===== DUCK TYPING — không cần chung base class =====

class PDFExporter:
    def export(self, data): return f"Xuất PDF: {data}"

class CSVExporter:
    def export(self, data): return f"Xuất CSV: {data}"

class JSONExporter:
    def export(self, data): return f"Xuất JSON: {data}"

def process_export(exporter, data: str):
    # Không kiểm tra isinstance — duck typing
    # Miễn là có method export(), sẽ chạy được
    return exporter.export(data)

for exp in [PDFExporter(), CSVExporter(), JSONExporter()]:
    print(process_export(exp, "dữ liệu tháng 5"))


# ===== OPERATOR OVERLOADING — dạng polymorphism khác =====

class Vector:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

    def __add__(self, other: "Vector") -> "Vector":
        # '+' operator có hành vi khác với số nguyên
        return Vector(self.x + other.x, self.y + other.y)

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)   # Vector(4, 6) — cùng toán tử '+', khác hành vi


# ===== isinstance() vs duck typing =====

def print_area(shape):
    # Cách 1: isinstance check (ít Pythonic hơn)
    if isinstance(shape, Shape):
        print(f"Area: {shape.area()}")

    # Cách 2: duck typing / EAFP (Pythonic hơn)
    try:
        print(f"Area (duck): {shape.area()}")
    except AttributeError:
        print("Object không có method area()")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Cần xử lý một nhóm đối tượng khác loại theo cùng một cách (ví dụ: vẽ tất cả Shape).
- Muốn viết code tổng quát không phụ thuộc vào kiểu cụ thể (dependency inversion).
- Thêm loại đối tượng mới mà không sửa code hiện có (Open/Closed Principle).
- Dùng Strategy Pattern, Template Method, Command Pattern.

**Không dùng khi:**
- Chỉ có 1–2 loại object, if/else đơn giản hơn và dễ đọc hơn.
- Các "behavior" thực ra rất khác nhau và không có interface chung thực sự.
- Performance critical code — dynamic dispatch có overhead nhỏ (thường không đáng kể).

---

## Lỗi thường gặp (Common Pitfalls)

- **Vi phạm LSP**: Override method nhưng thay đổi precondition/postcondition → polymorphism cho kết quả sai dù compile/run không lỗi.
- **Dùng `type()` thay `isinstance()`**: Bỏ sót subclass, phá vỡ polymorphism.
- **Overload bằng cách check kiểu trong method**: Anti-pattern — nếu thấy `if isinstance(x, Foo): ... elif isinstance(x, Bar): ...` trong method, đó là dấu hiệu nên dùng polymorphism thay thế.
- **Duck typing quá lỏng**: Không có abstract base class dẫn đến lỗi chỉ phát hiện tại runtime khi object thiếu method.
- **Quên `@abstractmethod`**: Class khai báo method nhưng không mark abstract → subclass không bị buộc implement, gây `AttributeError` muộn.
- **Nhầm polymorphism với overloading trong Python**: Python không có compile-time overloading — method định nghĩa sau luôn ghi đè method trước.

---

## Câu hỏi phỏng vấn hay gặp

1. **Polymorphism là gì? Cho ví dụ thực tế.** Cùng interface, hành vi khác nhau. Ví dụ: `shape.area()` cho Circle vs Rectangle.

2. **Duck typing là gì? Khác gì với subtype polymorphism?** Duck typing không cần IS-A relationship, chỉ cần có đúng method. Subtype polymorphism dựa trên hierarchy.

3. **Python có method overloading không?** Không có thực sự. Dùng default args, `*args`, `**kwargs`, hoặc `@singledispatch` để mô phỏng.

4. **Dynamic dispatch hoạt động thế nào trong Python?** Attribute lookup theo MRO tại runtime, không có vtable như C++.

5. **Khi nào nên dùng `isinstance()` và khi nào nên dùng duck typing?** Duck typing (EAFP) Pythonic hơn cho code linh hoạt. `isinstance()` khi cần validate input rõ ràng hoặc có behavior khác nhau thực sự theo kiểu.

6. **Liskov Substitution Principle liên quan đến polymorphism thế nào?** LSP là điều kiện để polymorphism hoạt động đúng — subclass phải thay thế được superclass mà không vỡ logic.

7. **Operator overloading có phải polymorphism không?** Có — đây là ad-hoc polymorphism, toán tử cùng ký hiệu hoạt động khác nhau với các kiểu dữ liệu khác nhau.
