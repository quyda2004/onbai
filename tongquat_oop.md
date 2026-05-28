# Tổng quan OOP — Object-Oriented Programming

---

## Roadmap học OOP

### Bước 1 — Nền tảng
- Class & Object — blueprint vs instance, constructor, destructor
- Attributes (instance vs class), Methods (instance vs static vs class)
- `self` / `this`, memory model (stack vs heap cho object)

### Bước 2 — 4 trụ cột OOP
- **Encapsulation** — access modifier (public/protected/private), getter/setter, data hiding
- **Inheritance** — single, multilevel, multiple (Python), override vs overload, `super()`
- **Polymorphism** — compile-time (overloading) vs runtime (overriding), duck typing
- **Abstraction** — abstract class vs interface, khi nào dùng cái nào

### Bước 3 — SOLID Principles
- **S** — Single Responsibility: mỗi class chỉ làm 1 việc
- **O** — Open/Closed: mở rộng được, không sửa code cũ
- **L** — Liskov Substitution: subclass thay thế được superclass
- **I** — Interface Segregation: nhiều interface nhỏ hơn 1 interface to
- **D** — Dependency Inversion: depend on abstraction, not concrete

### Bước 4 — Design Patterns (hay gặp)
- **Creational**: Singleton, Factory, Builder
- **Structural**: Adapter, Decorator, Composite
- **Behavioral**: Observer, Strategy, Command

---

## Bảng so sánh nhanh

| | Abstract Class | Interface |
|-|---------------|-----------|
| Khởi tạo | Không được | Không được |
| Constructor | Có | Không (Java/C#) |
| Multiple inherit | Không (Java/C#) | Có |
| Có code thực | Có | Có (default method Java 8+) |
| Khi nào dùng | Chia sẻ code chung | Định nghĩa contract |

| | Override | Overload |
|-|----------|----------|
| Loại | Runtime polymorphism | Compile-time polymorphism |
| Signature | Giống nhau | Khác tham số |
| Kế thừa | Cần | Không cần |
| `@Override` | Có | Không |

| | Composition | Inheritance |
|-|------------|-------------|
| Quan hệ | "has-a" | "is-a" |
| Coupling | Lỏng (loose) | Chặt (tight) |
| Linh hoạt | Cao hơn | Thấp hơn |
| Khi nào dùng | Ưu tiên dùng | Khi thực sự "is-a" |

---

## Mã giả — Hỏi output là gì?

### Bài 1 — Kế thừa & Override

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} makes a sound"

class Dog(Animal):
    def speak(self):
        return f"{self.name} barks"

class Cat(Animal):
    pass

animals = [Dog("Rex"), Cat("Mimi"), Animal("Unknown")]
for a in animals:
    print(a.speak())
```

> **Output là gì?**
> ```
> Rex barks
> Mimi makes a sound
> Unknown makes a sound
> ```
> **Giải thích:** Dog override `speak()` → dùng version của Dog. Cat không override → dùng version của Animal. Đây là **runtime polymorphism**.

---

### Bài 2 — `super()` và thứ tự khởi tạo

```python
class A:
    def __init__(self):
        print("A init")

class B(A):
    def __init__(self):
        print("B init")
        super().__init__()

class C(B):
    def __init__(self):
        print("C init")
        super().__init__()

c = C()
```

> **Output là gì?**
> ```
> C init
> B init
> A init
> ```
> **Giải thích:** `super()` gọi constructor của cha theo thứ tự MRO (Method Resolution Order). C gọi B, B gọi A.

---

### Bài 3 — Class variable vs Instance variable

```python
class Counter:
    count = 0  # class variable

    def __init__(self):
        Counter.count += 1
        self.id = Counter.count  # instance variable

a = Counter()
b = Counter()
c = Counter()
print(Counter.count)
print(a.id, b.id, c.id)
```

> **Output là gì?**
> ```
> 3
> 1 2 3
> ```
> **Giải thích:** `count` là class variable, chia sẻ giữa tất cả instances. `self.id` là instance variable riêng của từng object.

---

### Bài 4 — Đa kế thừa & MRO (Python)

```python
class X:
    def hello(self):
        print("X")

class Y(X):
    def hello(self):
        print("Y")

class Z(X):
    def hello(self):
        print("Z")

class W(Y, Z):
    pass

w = W()
w.hello()
print(W.__mro__)
```

> **Output là gì?**
> ```
> Y
> (<class 'W'>, <class 'Y'>, <class 'Z'>, <class 'X'>, <class 'object'>)
> ```
> **Giải thích:** Python dùng **C3 Linearization** (MRO). W tìm `hello()` theo thứ tự W→Y→Z→X. Tìm thấy ở Y → in "Y".

---

### Bài 5 — Static method vs Class method vs Instance method

```python
class MyClass:
    value = 10

    def instance_method(self):
        return f"instance, value={self.value}"

    @classmethod
    def class_method(cls):
        return f"class, value={cls.value}"

    @staticmethod
    def static_method():
        return "static, no access to class"

obj = MyClass()
print(obj.instance_method())
print(MyClass.class_method())
print(MyClass.static_method())
MyClass.value = 99
print(obj.class_method())
```

> **Output là gì?**
> ```
> instance, value=10
> class, value=10
> static, no access to class
> class, value=99
> ```
> **Giải thích:** `classmethod` nhận `cls` → truy cập class variable. Khi `MyClass.value = 99`, `cls.value` thay đổi theo.

---

### Bài 6 — Decorator Pattern (đơn giản)

```python
class Coffee:
    def cost(self):
        return 5

class MilkDecorator:
    def __init__(self, coffee):
        self._coffee = coffee

    def cost(self):
        return self._coffee.cost() + 2

class SugarDecorator:
    def __init__(self, coffee):
        self._coffee = coffee

    def cost(self):
        return self._coffee.cost() + 1

drink = SugarDecorator(MilkDecorator(Coffee()))
print(drink.cost())
```

> **Output là gì?**
> ```
> 8
> ```
> **Giải thích:** Coffee=5, MilkDecorator bọc Coffee → 5+2=7, SugarDecorator bọc MilkDecorator → 7+1=8. Đây là **Decorator Pattern** — thêm tính năng mà không sửa class gốc.

---

## Bảng SOLID — tóm tắt vi phạm thường gặp

| Nguyên tắc | Vi phạm điển hình | Cách sửa |
|------------|------------------|---------|
| SRP | Class vừa xử lý DB vừa format JSON | Tách thành 2 class |
| OCP | `if type == "A"... elif type == "B"` | Dùng polymorphism |
| LSP | Subclass throw exception khi gọi method cha | Redesign hierarchy |
| ISP | Interface có 10 method nhưng class chỉ dùng 2 | Tách interface nhỏ |
| DIP | Class A tạo `new B()` bên trong | Inject B qua constructor |

---

## Câu hỏi trắc nghiệm nhanh (tự test)

1. Encapsulation giải quyết vấn đề gì? → Kiểm soát truy cập dữ liệu
2. Abstract class khác Interface ở điểm gì quan trọng nhất? → Abstract class có thể có state (fields)
3. Liskov Substitution vi phạm khi nào? → Subclass hành xử khác cha theo cách bất ngờ
4. Composition over Inheritance nghĩa là gì? → Ưu tiên "has-a" thay vì "is-a" để giảm coupling
5. Singleton đảm bảo gì? → Chỉ có duy nhất 1 instance trong toàn chương trình
