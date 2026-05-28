# SOLID Principles

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn xây một ngôi nhà. Nếu bạn xây mà không theo nguyên tắc gì, sau này muốn sửa nhà bếp lại phải đập cả phòng ngủ; muốn thêm phòng phải xây lại từ đầu.

SOLID là **5 nguyên tắc thiết kế** giúp viết code như xây nhà theo tiêu chuẩn kiến trúc:
- **S**: Mỗi phòng chỉ làm một việc (phòng bếp nấu ăn, phòng ngủ để ngủ).
- **O**: Muốn thêm phòng thì xây thêm, không phải đập phòng cũ.
- **L**: Ghế bành hay ghế văn phòng đều ngồi được — ghế con không làm hỏng chức năng "ghế".
- **I**: Cầu dao điện riêng cho từng phòng, không dùng chung một cầu dao.
- **D**: Đường điện nối vào "ổ cắm tiêu chuẩn", không cắm thẳng vào từng thiết bị.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### S — Single Responsibility Principle (SRP)

> *"A class should have only one reason to change."* — Robert C. Martin

Một class chỉ nên có **một trách nhiệm duy nhất**. Nếu class thay đổi vì 2 lý do khác nhau → nó đang có 2 responsibilities.

**Dấu hiệu vi phạm:**
- Class tên `UserManager` vừa xử lý business logic, vừa gửi email, vừa lưu DB.
- Method dài hơn 20 dòng với nhiều "concern" khác nhau.

**Lợi ích:** Dễ test, dễ thay đổi từng phần độc lập.

### O — Open/Closed Principle (OCP)

> *"Open for extension, closed for modification."*

Class nên **dễ mở rộng** (thêm behavior mới) mà **không cần sửa code cũ** (không break existing behavior). Đạt được qua polymorphism và abstraction.

**Dấu hiệu vi phạm:** `if isinstance(shape, Circle): ... elif isinstance(shape, Rectangle): ...` — mỗi lần thêm shape phải sửa function.

### L — Liskov Substitution Principle (LSP)

> *"If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering correctness."* — Barbara Liskov, 1987

Subclass phải **thay thế được** superclass mà không làm vỡ chương trình. Tức là: behavior contract của superclass phải được tôn trọng.

**Ví dụ vi phạm kinh điển:** `Square extends Rectangle`. Nếu set width của Square tự động set height, thì code dùng Rectangle sẽ cho kết quả sai khi nhận Square.

**Quy tắc:**
- Precondition của submethod ≤ supermethod (không strengthen).
- Postcondition của submethod ≥ supermethod (không weaken).
- Không throw exception mới không được khai báo ở superclass.

### I — Interface Segregation Principle (ISP)

> *"Clients should not be forced to depend on interfaces they do not use."*

Nhiều interface nhỏ, **đặc thù** tốt hơn một interface to, **chung chung**. Tránh "fat interface" buộc implementor phải implement method họ không dùng (thường là throw `NotImplementedError`).

### D — Dependency Inversion Principle (DIP)

> *"High-level modules should not depend on low-level modules. Both should depend on abstractions."*

- **High-level**: Business logic, use cases.
- **Low-level**: Database, HTTP client, email sender.
- Cả hai nên phụ thuộc vào **abstraction** (interface/abstract class).

Đạt được qua **Dependency Injection**: truyền dependency vào từ ngoài thay vì tạo bên trong.

---

## Định nghĩa chính xác

**SOLID** là tập hợp 5 nguyên tắc thiết kế hướng đối tượng do Robert C. Martin ("Uncle Bob") đề xuất, nhằm tạo ra code dễ maintain, dễ extend, và robust trước sự thay đổi yêu cầu.

---

## So sánh / Bảng kỹ thuật

| Principle | Vấn đề giải quyết | Tool sử dụng | Vi phạm phổ biến |
|-----------|-------------------|--------------|------------------|
| SRP | God class, too many concerns | Tách class nhỏ | `UserService` làm 10 việc |
| OCP | Sửa code cũ khi thêm feature | Polymorphism, Abstract class | if/elif theo type |
| LSP | Subclass phá vỡ contract | Careful inheritance | Square/Rectangle problem |
| ISP | Fat interface | Nhiều interface nhỏ | Interface với 20 methods |
| DIP | Hard-coded dependency | Dependency Injection | `self.db = MySQL()` trong constructor |

---

## Code mẫu

```python
from abc import ABC, abstractmethod
from typing import Protocol

# ==============================================================
# S — Single Responsibility Principle
# ==============================================================

# --- VI PHẠM: UserService làm quá nhiều việc ---
class UserService_BAD:
    def get_user(self, user_id: int):
        # Responsibility 1: business logic
        return {"id": user_id, "name": "Alice"}

    def save_to_db(self, user: dict):
        # Responsibility 2: database
        print(f"INSERT INTO users: {user}")

    def send_welcome_email(self, user: dict):
        # Responsibility 3: email — không liên quan đến business logic
        print(f"Sending email to {user['name']}")

    def format_as_json(self, user: dict) -> str:
        # Responsibility 4: serialization
        import json
        return json.dumps(user)


# --- ĐÚNG: Mỗi class một trách nhiệm ---
class UserRepository:
    """Responsibility: data access"""
    def get_user(self, user_id: int) -> dict:
        return {"id": user_id, "name": "Alice"}

    def save(self, user: dict) -> None:
        print(f"[DB] Saving user: {user}")


class EmailService:
    """Responsibility: email notification"""
    def send_welcome(self, user: dict) -> None:
        print(f"[Email] Welcome {user['name']}!")


class UserSerializer:
    """Responsibility: serialization"""
    def to_json(self, user: dict) -> str:
        import json
        return json.dumps(user)


class UserService_GOOD:
    """Responsibility: orchestrate use cases"""
    def __init__(self, repo: "UserRepository", email: "EmailService"):
        self.repo = repo
        self.email = email

    def register_user(self, name: str) -> dict:
        user = {"name": name}
        self.repo.save(user)
        self.email.send_welcome(user)
        return user


# ==============================================================
# O — Open/Closed Principle
# ==============================================================

# --- VI PHẠM: mỗi lần thêm shape phải sửa hàm ---
def total_area_BAD(shapes: list) -> float:
    total = 0
    for shape in shapes:
        if shape["type"] == "circle":          # Phải sửa khi thêm Triangle
            total += 3.14 * shape["r"] ** 2
        elif shape["type"] == "rectangle":
            total += shape["w"] * shape["h"]
    return total


# --- ĐÚNG: thêm shape mới mà không sửa total_area ---
class Shape(ABC):
    @abstractmethod
    def area(self) -> float: pass

class Circle(Shape):
    def __init__(self, r): self.r = r
    def area(self): return 3.14159 * self.r ** 2

class Rectangle(Shape):
    def __init__(self, w, h): self.w = w; self.h = h
    def area(self): return self.w * self.h

class Triangle(Shape):
    # Thêm mới, KHÔNG cần sửa total_area
    def __init__(self, base, height): self.base = base; self.height = height
    def area(self): return 0.5 * self.base * self.height

def total_area_GOOD(shapes: list[Shape]) -> float:
    return sum(s.area() for s in shapes)   # Không đổi khi thêm shape mới

shapes = [Circle(5), Rectangle(4, 6), Triangle(3, 4)]
print(f"Tổng diện tích: {total_area_GOOD(shapes):.2f}")


# ==============================================================
# L — Liskov Substitution Principle
# ==============================================================

# --- VI PHẠM: Square extends Rectangle phá vỡ LSP ---
class Rectangle_LSP:
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def set_width(self, w): self._width = w
    def set_height(self, h): self._height = h
    def area(self): return self._width * self._height


class Square_BAD(Rectangle_LSP):
    """VI PHẠM LSP: Square thay đổi behavior của Rectangle"""
    def set_width(self, w):
        self._width = w
        self._height = w    # Side effect không mong đợi!

    def set_height(self, h):
        self._width = h
        self._height = h    # Side effect không mong đợi!


def check_area(rect: Rectangle_LSP):
    rect.set_width(5)
    rect.set_height(10)
    expected = 50
    actual = rect.area()
    print(f"Area={actual}, Expected={expected}, OK={actual == expected}")

check_area(Rectangle_LSP(2, 3))   # Area=50, OK=True
check_area(Square_BAD(2, 2))      # Area=100, OK=False ← LSP bị vi phạm!


# --- ĐÚNG: Dùng chung abstract base, không kế thừa lẫn nhau ---
class Shape2D(ABC):
    @abstractmethod
    def area(self) -> float: pass

class RectangleShape(Shape2D):
    def __init__(self, w, h): self.w = w; self.h = h
    def area(self): return self.w * self.h

class SquareShape(Shape2D):
    def __init__(self, side): self.side = side
    def area(self): return self.side ** 2

# Cả hai thỏa mãn Shape2D contract mà không vi phạm nhau


# ==============================================================
# I — Interface Segregation Principle
# ==============================================================

# --- VI PHẠM: Fat interface ---
class Worker_BAD(ABC):
    @abstractmethod
    def work(self): pass

    @abstractmethod
    def eat(self): pass       # Robot không ăn!

    @abstractmethod
    def sleep(self): pass     # Robot không ngủ!


class Robot_BAD(Worker_BAD):
    def work(self): print("Robot đang làm việc")
    def eat(self): raise NotImplementedError("Robot không ăn!")  # Vi phạm ISP
    def sleep(self): raise NotImplementedError("Robot không ngủ!")


# --- ĐÚNG: Tách thành interface nhỏ ---
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Sleepable(ABC):
    @abstractmethod
    def sleep(self): pass

class Human(Workable, Eatable, Sleepable):
    def work(self): print("Human đang làm việc")
    def eat(self): print("Human đang ăn")
    def sleep(self): print("Human đang ngủ")

class Robot(Workable):
    # Chỉ implement những gì Robot thực sự làm được
    def work(self): print("Robot đang làm việc")


# ==============================================================
# D — Dependency Inversion Principle
# ==============================================================

# --- VI PHẠM: High-level phụ thuộc Low-level trực tiếp ---
class MySQLDatabase_BAD:
    def save(self, data): print(f"[MySQL] Saving: {data}")

class UserService_DIP_BAD:
    def __init__(self):
        self.db = MySQLDatabase_BAD()    # Hard-coded! Không thể test hoặc swap

    def create_user(self, name: str):
        self.db.save({"name": name})


# --- ĐÚNG: Phụ thuộc vào abstraction, inject từ ngoài ---
class Database(ABC):
    @abstractmethod
    def save(self, data: dict) -> None: pass

class MySQLDatabase(Database):
    def save(self, data): print(f"[MySQL] Saving: {data}")

class InMemoryDatabase(Database):
    """Dùng trong unit test"""
    def __init__(self): self.storage = []
    def save(self, data): self.storage.append(data)

class UserService_DIP_GOOD:
    def __init__(self, db: Database):    # Inject abstraction
        self.db = db                     # Không biết là MySQL hay gì

    def create_user(self, name: str):
        self.db.save({"name": name})


# Production: dùng MySQL
svc = UserService_DIP_GOOD(db=MySQLDatabase())
svc.create_user("Alice")

# Test: dùng InMemory
test_db = InMemoryDatabase()
test_svc = UserService_DIP_GOOD(db=test_db)
test_svc.create_user("Bob")
print(f"Test DB có {len(test_db.storage)} user(s)")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng SOLID khi:**
- Code base sẽ phát triển dài hạn, cần maintain.
- Nhiều người cùng làm việc trên codebase.
- Cần viết unit test dễ dàng (DIP/SRP đặc biệt quan trọng cho testability).
- Thêm feature thường xuyên (OCP tránh regression).

**Không nên áp dụng cứng nhắc khi:**
- Prototype hoặc script đơn giản — over-engineering.
- Code sẽ chỉ dùng một lần.
- Team nhỏ, requirement ổn định — YAGNI (You Aren't Gonna Need It).

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm SRP với "mỗi class 1 method"**: SRP không yêu cầu cực đoan như vậy. "1 responsibility" = 1 reason to change, có thể có nhiều method phục vụ cùng 1 mục đích.
- **OCP áp dụng sai**: Không phải "không bao giờ sửa code" — mà là thiết kế để extension không yêu cầu sửa existing tested code.
- **LSP silent failure**: Vi phạm LSP không luôn raise exception — đôi khi trả về kết quả sai silently. Cần test kỹ.
- **ISP quá cực đoan**: Mỗi method một interface là overkill. Nhóm các method có liên quan logical vào cùng 1 interface.
- **DIP nhầm với Dependency Injection (DI)**: DI là kỹ thuật để đạt DIP, không phải DIP = DI. DIP là principle (concept), DI là mechanism.
- **Áp dụng SOLID mà không có unit test**: Lợi ích lớn nhất của SOLID là testability — không viết test thì mất đi nhiều giá trị.

---

## Câu hỏi phỏng vấn hay gặp

1. **Giải thích từng chữ trong SOLID.** SRP: 1 reason to change. OCP: extend without modify. LSP: substitutable subclass. ISP: small interfaces. DIP: depend on abstraction.

2. **Cho ví dụ vi phạm LSP.** Square extends Rectangle — `set_width` trên Square thay đổi cả height, phá vỡ hợp đồng của Rectangle.

3. **DIP vs Dependency Injection?** DIP là principle (high/low level phụ thuộc abstraction). DI là technique để implement DIP (inject dependency từ ngoài).

4. **Khi nào SOLID gây over-engineering?** Khi project nhỏ, đơn giản, không có kế hoạch mở rộng. SOLID có chi phí upfront — chỉ đáng đầu tư khi cần.

5. **OCP trong thực tế — ví dụ cụ thể.** Plugin architecture, Strategy Pattern, Event listeners — thêm behavior mà không sửa core.

6. **ISP giúp gì cho testability?** Interface nhỏ dễ mock hơn. Fat interface yêu cầu mock nhiều method không liên quan, làm test phức tạp.

7. **SRP và Microservices có liên quan không?** Có — Microservices là áp dụng SRP ở level service: mỗi service một responsibility. Đây là lý do Microservices dễ scale và deploy độc lập.
