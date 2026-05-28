# Inheritance (Kế thừa)

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn có một **tờ giấy mô tả "Động vật"**: nó có tên, biết thở, biết di chuyển. Thay vì viết lại từ đầu tờ giấy mới cho "Chó", bạn chỉ cần nói *"Chó là một loại Động vật, ngoài ra còn biết sủa"*. Chó **thừa hưởng** mọi thứ từ Động vật, rồi **thêm hoặc thay đổi** những điều đặc trưng của riêng mình.

Đó chính là Inheritance trong lập trình: **lớp con (child class) tự động có mọi thuộc tính và hành vi của lớp cha (parent class)**, mà không cần viết lại. Lớp con có thể thêm mới hoặc ghi đè (override) hành vi đó.

Ví dụ cuộc sống:
- Xe tải **là một loại** Xe → thừa hưởng "có bánh", "có động cơ", thêm "chở hàng nặng"
- Giáo viên **là một loại** Người → thừa hưởng "có tên", "có tuổi", thêm "dạy học"

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Các dạng Inheritance

| Loại | Mô tả | Ví dụ |
|------|-------|-------|
| **Single** | A → B (1 cha, 1 con) | Dog extends Animal |
| **Multilevel** | A → B → C (dây chuyền) | Animal → Dog → GuideDog |
| **Multiple** | A, B → C (nhiều cha) | Python hỗ trợ, Java không |
| **Hierarchical** | A → B, A → C | Animal → Dog, Animal → Cat |
| **Hybrid** | Kết hợp nhiều dạng | Gây ra Diamond Problem |

### Method Override vs Method Overload

- **Override**: Lớp con viết lại method cùng tên, cùng signature của lớp cha → xảy ra ở runtime, gắn với polymorphism.
- **Overload**: Cùng tên method nhưng khác tham số → xảy ra ở compile-time (Java/C++). Python **không hỗ trợ overload thực sự** (method sau ghi đè method trước).

### `super()` và Constructor Chaining

Khi lớp con gọi `super().__init__(...)`, nó đang **ủy thác** công việc khởi tạo lên lớp cha. Điều quan trọng:
- Nếu không gọi `super().__init__()`, các thuộc tính của lớp cha **sẽ không được khởi tạo**.
- Trong multiple inheritance, `super()` không phải là "gọi lớp cha trực tiếp" mà tuân theo **MRO**.

### MRO — Method Resolution Order (C3 Linearization)

Python dùng thuật toán **C3 Linearization** để xác định thứ tự tìm method khi có multiple inheritance. Quy tắc:
1. Lớp con trước lớp cha.
2. Giữ thứ tự khai báo base classes.
3. Không vi phạm thứ tự trong từng nhánh thừa kế.

Kiểm tra MRO: `ClassName.__mro__` hoặc `ClassName.mro()`.

### Fragile Base Class Problem

Khi lớp cha thay đổi implementation (thêm gọi method nội bộ, đổi logic), lớp con đang override có thể bị **vỡ silently** vì không biết sự thay đổi. Đây là lý do Composition over Inheritance thường được ưu tiên trong production code.

### Composition over Inheritance

- **Inheritance**: "là một loại" (is-a relationship)
- **Composition**: "có một" (has-a relationship)

Thay vì `GuideDog extends Dog` rồi override hàng loạt, hãy dùng `GuideDog` có một `Dog` bên trong và **delegate** các hành vi cần thiết. Linh hoạt hơn, dễ test hơn, tránh tight coupling.

---

## Định nghĩa chính xác

**Inheritance** là cơ chế trong OOP cho phép một lớp (subclass/derived class) **kế thừa** các thuộc tính (attributes) và phương thức (methods) từ một hoặc nhiều lớp khác (superclass/base class), đồng thời có thể **mở rộng** hoặc **ghi đè** chúng. Quan hệ this là quan hệ **IS-A**.

---

## So sánh / Bảng kỹ thuật

| Tiêu chí | Inheritance | Composition |
|----------|-------------|-------------|
| Quan hệ | IS-A | HAS-A |
| Tái sử dụng code | Tốt | Tốt hơn |
| Coupling | Chặt (tight) | Lỏng (loose) |
| Thay đổi linh hoạt | Khó (fragile base class) | Dễ (swap implementation) |
| Test | Khó isolate | Dễ mock/stub |
| Phù hợp | Khi quan hệ IS-A rõ ràng | Hầu hết các trường hợp còn lại |

### Độ phức tạp

| Thao tác | Best Case | Average Case | Worst Case | Space | Ghi chú |
|----------|-----------|--------------|------------|-------|---------|
| Method lookup (single inheritance) | O(1) | O(1) | O(d) | O(1) | d = depth of hierarchy |
| Method lookup (multiple inheritance, MRO) | O(1) | O(n) | O(n) | O(n) | n = số lớp trong MRO |
| `isinstance()` check | O(d) | O(d) | O(d) | O(1) | d = chiều sâu cây thừa kế |

---

## Code mẫu

```python
# ===== SINGLE & MULTILEVEL INHERITANCE =====

class Animal:
    """Lớp cha cơ sở"""

    def __init__(self, name: str, species: str):
        self.name = name
        self.species = species
        print(f"[Animal.__init__] {name} ({species}) được tạo")

    def breathe(self):
        return f"{self.name} đang thở"

    def speak(self):
        # Method này sẽ bị lớp con override
        return f"{self.name} tạo ra âm thanh"

    def __repr__(self):
        return f"{self.__class__.__name__}(name={self.name!r})"


class Dog(Animal):
    """Lớp con — kế thừa từ Animal"""

    def __init__(self, name: str, breed: str):
        # Gọi constructor lớp cha qua super()
        super().__init__(name, species="Canis lupus familiaris")
        self.breed = breed
        print(f"[Dog.__init__] Giống chó: {breed}")

    def speak(self):
        # Override method của lớp cha
        return f"{self.name} sủa: Woof!"

    def fetch(self, item: str):
        # Method riêng của Dog, Animal không có
        return f"{self.name} lấy {item}"


class GuideDog(Dog):
    """Lớp con tầng 2 — multilevel inheritance"""

    def __init__(self, name: str, breed: str, handler: str):
        super().__init__(name, breed)      # Gọi Dog.__init__
        self.handler = handler
        print(f"[GuideDog.__init__] Dẫn đường cho: {handler}")

    def guide(self):
        return f"{self.name} đang dẫn {self.handler} qua đường"

    def speak(self):
        # Override lần 2
        base = super().speak()             # Tái sử dụng logic của Dog
        return f"{base} (nhẹ nhàng vì đang làm việc)"


# ----- Demo thứ tự gọi __init__ -----
print("=== Tạo GuideDog ===")
buddy = GuideDog(name="Buddy", breed="Labrador", handler="Ông Nam")
# Output thứ tự:
#   [Animal.__init__] Buddy (Canis lupus familiaris) được tạo
#   [Dog.__init__] Giống chó: Labrador
#   [GuideDog.__init__] Dẫn đường cho: Ông Nam

print()
print(buddy.breathe())    # Kế thừa từ Animal: "Buddy đang thở"
print(buddy.fetch("bóng"))  # Kế thừa từ Dog: "Buddy lấy bóng"
print(buddy.speak())      # Override bởi GuideDog
print(buddy.guide())      # Riêng của GuideDog

# ----- Kiểm tra MRO -----
print()
print("MRO:", [cls.__name__ for cls in GuideDog.__mro__])
# ['GuideDog', 'Dog', 'Animal', 'object']

# ----- isinstance() -----
print(isinstance(buddy, GuideDog))  # True
print(isinstance(buddy, Dog))       # True — vì GuideDog IS-A Dog
print(isinstance(buddy, Animal))    # True — vì GuideDog IS-A Animal


# ===== MULTIPLE INHERITANCE & MRO =====

class Flyable:
    def move(self):
        return "Tôi bay"

class Swimmable:
    def move(self):
        return "Tôi bơi"

class Duck(Flyable, Swimmable):
    pass

d = Duck()
print(d.move())                  # "Tôi bay" — Flyable được ưu tiên theo MRO
print(Duck.__mro__)              # Duck → Flyable → Swimmable → object


# ===== COMPOSITION THAY THẾ INHERITANCE =====

class Engine:
    def start(self):
        return "Động cơ khởi động"

class Car:
    """Dùng Composition: Car HAS-A Engine (không phải IS-A Engine)"""

    def __init__(self):
        self._engine = Engine()   # Composition

    def start(self):
        return self._engine.start()  # Delegation
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Quan hệ IS-A rõ ràng và ổn định (Dog IS-A Animal, không đổi theo thời gian).
- Muốn tái sử dụng logic của lớp cha mà không cần copy-paste.
- Cần polymorphism — xử lý các object cùng kiểu cha theo cách thống nhất.
- Framework/library yêu cầu extend một base class (Django Model, Flask View...).

**Không dùng khi:**
- Quan hệ chỉ là HAS-A hoặc USES-A → dùng Composition.
- Hierarchy quá sâu (> 3 tầng) → khó debug, fragile base class problem.
- Chỉ muốn tái sử dụng 1–2 method của lớp cha → dùng utility function hoặc mixin.
- Lớp cha có thể thay đổi thường xuyên → tránh tight coupling bằng Composition.

---

## Lỗi thường gặp (Common Pitfalls)

- **Quên gọi `super().__init__()`**: Thuộc tính của lớp cha không được khởi tạo, gây `AttributeError` khi truy cập.
- **Diamond Problem không kiểm soát**: Khi multiple inheritance tạo ra hình thoi, method nào được gọi phụ thuộc MRO — nếu không hiểu MRO sẽ ra kết quả bất ngờ.
- **Override method nhưng thay đổi signature**: Vi phạm Liskov Substitution Principle (LSP), khiến lớp con không thay thế được lớp cha.
- **Hierarchy quá sâu**: Mỗi tầng thêm complexity, debug trở nên khó khăn. Rule of thumb: tối đa 2–3 tầng.
- **Nhầm Inheritance với Composition**: Dùng inheritance chỉ để tái sử dụng code mà không có quan hệ IS-A thực sự.
- **Lớp con phụ thuộc vào implementation detail của lớp cha**: Khi lớp cha refactor nội bộ, lớp con bị vỡ (Fragile Base Class).

---

## Câu hỏi phỏng vấn hay gặp

1. **Inheritance vs Composition — khi nào dùng cái nào?** Inheritance cho IS-A, Composition cho HAS-A. Prefer composition for flexibility.

2. **Python hỗ trợ multiple inheritance như thế nào? MRO là gì?** C3 Linearization, dùng `__mro__` để kiểm tra.

3. **`super()` trong Python làm gì chính xác?** Không phải "gọi lớp cha", mà gọi class tiếp theo trong MRO — quan trọng trong multiple inheritance.

4. **Fragile Base Class Problem là gì? Cách tránh?** Lớp con bị vỡ khi lớp cha thay đổi implementation. Tránh bằng Composition, hoặc dùng abstract interface thay vì concrete class.

5. **Override vs Overload — Python có overloading không?** Python không có overloading thực sự. Dùng default arguments hoặc `*args`/`**kwargs` để mô phỏng.

6. **Giải thích Diamond Problem và cách Python giải quyết.** Khi 2 lớp cha cùng kế thừa từ 1 lớp gốc, C3 MRO đảm bảo mỗi lớp chỉ xuất hiện một lần trong chuỗi tìm kiếm.

7. **Khi nào nên dùng `super()` thay vì gọi trực tiếp tên lớp cha?** Luôn dùng `super()` để tương thích với multiple inheritance và MRO. Gọi tên trực tiếp (`Animal.__init__`) bỏ qua MRO.
