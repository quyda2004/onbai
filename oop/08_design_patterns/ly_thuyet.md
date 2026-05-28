# Design Patterns (Mẫu thiết kế)

---

## Giải thích cho người mới hoàn toàn

Trong xây dựng, có những giải pháp kiến trúc đã được kiểm chứng qua hàng trăm năm: cầu treo, hành lang, phòng đệm. Khi gặp bài toán tương tự, kiến trúc sư không cần phát minh lại từ đầu — họ áp dụng mẫu đã biết.

**Design Patterns** (mẫu thiết kế) là những **giải pháp đã được kiểm chứng** cho các bài toán lặp lại trong lập trình. Không phải code có thể copy-paste, mà là **template tư duy** — cách tiếp cận vấn đề đã được chứng minh hiệu quả.

Ví dụ:
- Chỉ cần **1 kết nối database** cho toàn app → Singleton
- Muốn **tạo đối tượng** mà không cần biết class cụ thể → Factory
- Muốn **thêm tính năng** cho object mà không sửa class gốc → Decorator

---

## Giải thích cho người đã biết lập trình (nâng cao)

Design Patterns được phân loại thành 3 nhóm bởi "Gang of Four" (GoF — book 1994):

| Nhóm | Mô tả | Patterns |
|------|-------|---------|
| **Creational** | Cách tạo objects | Singleton, Factory, Abstract Factory, Builder, Prototype |
| **Structural** | Cách tổ chức class/object | Adapter, Decorator, Facade, Proxy, Composite |
| **Behavioral** | Cách object giao tiếp | Observer, Strategy, Command, Iterator, Template Method |

Bài này cover 7 pattern quan trọng nhất trong phỏng vấn.

---

## Định nghĩa chính xác

**Design Pattern** là một mô tả hoặc template tái sử dụng được dùng để giải quyết các vấn đề thiết kế phần mềm thường gặp trong một ngữ cảnh cụ thể. Pattern mô tả: *Problem* (bài toán), *Solution* (giải pháp cấu trúc), và *Consequences* (hệ quả trade-off).

---

## So sánh / Bảng kỹ thuật

| Pattern | Nhóm | Bài toán giải quyết | Khi nào dùng |
|---------|------|---------------------|--------------|
| Singleton | Creational | Chỉ 1 instance | DB connection, Logger, Config |
| Factory Method | Creational | Tạo object linh hoạt | Khi không biết class cụ thể trước |
| Builder | Creational | Object phức tạp nhiều bước | Query builder, HTTP request builder |
| Adapter | Structural | Interface không tương thích | Tích hợp third-party library |
| Decorator | Structural | Thêm tính năng không sửa class | Middleware, caching, logging |
| Observer | Behavioral | Thông báo nhiều listener | Event system, UI updates |
| Strategy | Behavioral | Đổi algorithm runtime | Sorting, payment method, export |

### Độ phức tạp

| Pattern | Space Overhead | Time Overhead | Ghi chú |
|---------|---------------|---------------|---------|
| Singleton | O(1) — 1 instance | O(1) sau lần đầu | Lock overhead nếu thread-safe |
| Factory | O(1) | O(1) | Thêm 1 layer indirection |
| Builder | O(n) | O(n) | n = số steps |
| Adapter | O(1) | O(1) | Thêm 1 layer wrapping |
| Decorator | O(d) | O(d) | d = số decorators xếp chồng |
| Observer | O(n) | O(n) | n = số observers |
| Strategy | O(1) | O(1) | Chỉ giữ reference đến strategy |

---

## Code mẫu

```python
from __future__ import annotations
from abc import ABC, abstractmethod
from threading import Lock
from typing import Callable

# ==============================================================
# 1. SINGLETON — Chỉ 1 instance, thread-safe
# ==============================================================

class DatabaseConnection:
    """
    Thread-safe Singleton sử dụng double-checked locking.
    Đảm bảo chỉ có 1 instance ngay cả khi nhiều thread cùng tạo.
    """
    _instance: DatabaseConnection | None = None
    _lock: Lock = Lock()

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            with cls._lock:                        # Thread-safe: chỉ 1 thread vào
                if cls._instance is None:          # Double-check sau khi có lock
                    cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self, url: str = "postgresql://localhost/mydb"):
        if not hasattr(self, "_initialized"):      # Tránh reinitialize
            self.url = url
            self._initialized = True
            print(f"[Singleton] DB connection tạo mới: {url}")

    def query(self, sql: str) -> list:
        return [{"result": f"data từ '{sql}'"}]


# Kiểm tra: cả hai biến trỏ cùng một object
db1 = DatabaseConnection("postgresql://server1/app")
db2 = DatabaseConnection("postgresql://server2/app")  # Không tạo mới
print(f"Cùng object? {db1 is db2}")   # True
print(f"URL: {db1.url}")               # server1 — db2 không override


# ==============================================================
# 2. FACTORY METHOD — Tạo object không chỉ định class cụ thể
# ==============================================================

class Notification(ABC):
    @abstractmethod
    def send(self, message: str) -> None: pass

class EmailNotification(Notification):
    def __init__(self, email: str): self.email = email
    def send(self, message: str): print(f"[Email → {self.email}] {message}")

class SMSNotification(Notification):
    def __init__(self, phone: str): self.phone = phone
    def send(self, message: str): print(f"[SMS → {self.phone}] {message}")

class PushNotification(Notification):
    def __init__(self, device_id: str): self.device_id = device_id
    def send(self, message: str): print(f"[Push → {self.device_id}] {message}")


class NotificationFactory:
    """Factory: tạo Notification dựa trên type string"""
    _registry: dict[str, type] = {
        "email": EmailNotification,
        "sms": SMSNotification,
        "push": PushNotification,
    }

    @classmethod
    def create(cls, ntype: str, target: str) -> Notification:
        if ntype not in cls._registry:
            raise ValueError(f"Unknown notification type: {ntype}")
        return cls._registry[ntype](target)

    @classmethod
    def register(cls, ntype: str, klass: type) -> None:
        """Mở rộng factory không cần sửa code — OCP"""
        cls._registry[ntype] = klass


# Client code không biết class cụ thể:
for ntype, target in [("email", "user@example.com"), ("sms", "0901234567"), ("push", "device-abc")]:
    notif = NotificationFactory.create(ntype, target)
    notif.send("Chào mừng đến hệ thống!")


# ==============================================================
# 3. BUILDER — Tạo object phức tạp từng bước
# ==============================================================

class SQLQuery:
    """Complex object được build từng bước"""
    def __init__(self):
        self.table = ""
        self.conditions: list[str] = []
        self.columns: list[str] = ["*"]
        self.order_by: str | None = None
        self.limit_val: int | None = None

    def build(self) -> str:
        sql = f"SELECT {', '.join(self.columns)} FROM {self.table}"
        if self.conditions:
            sql += " WHERE " + " AND ".join(self.conditions)
        if self.order_by:
            sql += f" ORDER BY {self.order_by}"
        if self.limit_val:
            sql += f" LIMIT {self.limit_val}"
        return sql


class QueryBuilder:
    """Builder: xây dựng SQLQuery từng bước, trả về self để chain"""

    def __init__(self):
        self._query = SQLQuery()

    def select(self, *columns: str) -> "QueryBuilder":
        self._query.columns = list(columns)
        return self                          # Method chaining

    def from_table(self, table: str) -> "QueryBuilder":
        self._query.table = table
        return self

    def where(self, condition: str) -> "QueryBuilder":
        self._query.conditions.append(condition)
        return self

    def order_by(self, column: str) -> "QueryBuilder":
        self._query.order_by = column
        return self

    def limit(self, n: int) -> "QueryBuilder":
        self._query.limit_val = n
        return self

    def build(self) -> str:
        return self._query.build()


# Fluent interface nhờ method chaining:
query = (QueryBuilder()
    .select("id", "name", "email")
    .from_table("users")
    .where("age > 18")
    .where("active = true")
    .order_by("name")
    .limit(10)
    .build())
print(query)
# SELECT id, name, email FROM users WHERE age > 18 AND active = true ORDER BY name LIMIT 10


# ==============================================================
# 4. ADAPTER — Wrap interface không tương thích
# ==============================================================

# Thư viện cũ (không thể sửa):
class OldPaymentGateway:
    def make_payment(self, amount_cents: int, currency_code: str) -> bool:
        print(f"[OldGateway] Thanh toán {amount_cents} cents ({currency_code})")
        return True


# Interface mới mà hệ thống dùng:
class PaymentProcessor(ABC):
    @abstractmethod
    def process(self, amount_dollars: float, currency: str) -> bool: pass


# Adapter: wrap OldGateway để dùng với interface mới
class OldGatewayAdapter(PaymentProcessor):
    def __init__(self, old_gateway: OldPaymentGateway):
        self._gateway = old_gateway        # Wrap old interface

    def process(self, amount_dollars: float, currency: str) -> bool:
        # Convert: dollars → cents
        amount_cents = int(amount_dollars * 100)
        return self._gateway.make_payment(amount_cents, currency.upper())


# Client code chỉ biết PaymentProcessor:
def checkout(processor: PaymentProcessor, amount: float):
    success = processor.process(amount, "vnd")
    print(f"Thanh toán {'thành công' if success else 'thất bại'}")

adapter = OldGatewayAdapter(OldPaymentGateway())
checkout(adapter, 99.99)


# ==============================================================
# 5. DECORATOR — Thêm tính năng không sửa class gốc
# ==============================================================

class Coffee(ABC):
    @abstractmethod
    def cost(self) -> float: pass

    @abstractmethod
    def description(self) -> str: pass


class SimpleCoffee(Coffee):
    def cost(self) -> float: return 20_000
    def description(self) -> str: return "Cà phê đen"


class CoffeeDecorator(Coffee):
    """Base decorator — wrap một Coffee object"""
    def __init__(self, coffee: Coffee):
        self._coffee = coffee

    def cost(self) -> float:
        return self._coffee.cost()          # Delegate to wrapped

    def description(self) -> str:
        return self._coffee.description()


class MilkDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 5_000
    def description(self) -> str:
        return self._coffee.description() + " + Sữa"

class SugarDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 2_000
    def description(self) -> str:
        return self._coffee.description() + " + Đường"

class WhipDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 8_000
    def description(self) -> str:
        return self._coffee.description() + " + Kem tươi"


# Xếp chồng decorators linh hoạt:
order = WhipDecorator(MilkDecorator(SugarDecorator(SimpleCoffee())))
print(f"{order.description()}: {order.cost():,} VND")
# Cà phê đen + Đường + Sữa + Kem tươi: 35,000 VND


# ==============================================================
# 6. OBSERVER — Publish/Subscribe event system
# ==============================================================

class EventBus:
    """Simple Observer / Event Bus"""
    def __init__(self):
        self._listeners: dict[str, list[Callable]] = {}

    def subscribe(self, event: str, callback: Callable) -> None:
        self._listeners.setdefault(event, []).append(callback)

    def unsubscribe(self, event: str, callback: Callable) -> None:
        if event in self._listeners:
            self._listeners[event].remove(callback)

    def publish(self, event: str, data: dict = None) -> None:
        for callback in self._listeners.get(event, []):
            callback(data or {})


# Subscribers:
def send_welcome_email(data: dict):
    print(f"[Email] Gửi welcome email đến {data['email']}")

def create_user_profile(data: dict):
    print(f"[Profile] Tạo profile cho {data['name']}")

def log_registration(data: dict):
    print(f"[Log] User mới: {data['name']} đăng ký lúc {data.get('time', 'now')}")


bus = EventBus()
bus.subscribe("user.registered", send_welcome_email)
bus.subscribe("user.registered", create_user_profile)
bus.subscribe("user.registered", log_registration)

# Publish event — tất cả subscribers được notify
bus.publish("user.registered", {
    "name": "Alice",
    "email": "alice@example.com",
    "time": "2024-01-15 10:00"
})


# ==============================================================
# 7. STRATEGY — Đổi algorithm tại runtime
# ==============================================================

class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data: list) -> list: pass

class BubbleSort(SortStrategy):
    def sort(self, data: list) -> list:
        arr = data.copy()
        n = len(arr)
        for i in range(n):
            for j in range(n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
        print("[BubbleSort] O(n²)")
        return arr

class QuickSort(SortStrategy):
    def sort(self, data: list) -> list:
        if len(data) <= 1: return data
        pivot = data[len(data) // 2]
        left = [x for x in data if x < pivot]
        mid = [x for x in data if x == pivot]
        right = [x for x in data if x > pivot]
        print("[QuickSort] O(n log n) average")
        return self.sort(left) + mid + self.sort(right)

class TimSort(SortStrategy):
    def sort(self, data: list) -> list:
        print("[TimSort] Python built-in O(n log n)")
        return sorted(data)    # Python's built-in timsort


class DataSorter:
    """Context — có thể swap strategy tại runtime"""
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: SortStrategy) -> None:
        self._strategy = strategy          # Đổi algorithm không sửa class

    def sort(self, data: list) -> list:
        return self._strategy.sort(data)


data = [64, 25, 12, 22, 11]

sorter = DataSorter(BubbleSort())
print(sorter.sort(data))

sorter.set_strategy(QuickSort())          # Đổi sang QuickSort tại runtime
print(sorter.sort(data))

sorter.set_strategy(TimSort())            # Đổi sang TimSort
print(sorter.sort(data))
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Singleton:**
- Dùng: Database connection pool, Logger, Configuration, Cache.
- Không dùng: Khi cần multiple instances (ví dụ test isolation rất khó với Singleton).

**Factory Method:**
- Dùng: Khi cần tạo object thuộc subclass khác nhau dựa trên điều kiện runtime.
- Không dùng: Khi chỉ có 1 loại object và không cần polymorphism.

**Builder:**
- Dùng: Object có nhiều optional parameter, cần fluent interface.
- Không dùng: Object đơn giản ít tham số.

**Adapter:**
- Dùng: Tích hợp third-party library với interface khác; legacy code integration.
- Không dùng: Khi có thể refactor trực tiếp.

**Decorator:**
- Dùng: Middleware pipeline, caching/logging wrapper, UI component styling.
- Không dùng: Khi chỉ cần subclass đơn giản là đủ.

**Observer:**
- Dùng: Event system, UI binding (React-like), microservice event-driven.
- Không dùng: Khi quá nhiều observers dẫn đến "event hell" khó debug.

**Strategy:**
- Dùng: Nhiều algorithm có thể hoán đổi (sort, payment, compression).
- Không dùng: Chỉ 1–2 algorithm, if/else đơn giản hơn.

---

## Lỗi thường gặp (Common Pitfalls)

- **Singleton Anti-pattern**: Singleton là "global state" — khó test (không thể reset giữa các test), khó parallel. Ưu tiên Dependency Injection thay vì Singleton khi có thể.
- **Factory phức tạp không cần thiết**: Nếu chỉ có 1–2 loại object, simple if/else rõ ràng hơn Factory.
- **Builder method chaining không trả về `self`**: Quên `return self` trong mỗi method → fluent API không hoạt động.
- **Decorator làm mất type information**: Decorator wrap object nhưng thay đổi interface, làm `isinstance()` check fail. Dùng `functools.wraps` cho function decorator.
- **Observer memory leak**: Subscriber đăng ký nhưng không unsubscribe → object không được garbage collect vì EventBus giữ reference.
- **Strategy overuse**: Không phải lúc nào cũng cần Strategy. Đôi khi `sort(key=...)` đơn giản hơn nhiều.
- **Nhầm Pattern với Silver Bullet**: Design pattern giải quyết vấn đề cụ thể. Áp dụng sai pattern gây thêm complexity.

---

## Câu hỏi phỏng vấn hay gặp

1. **Singleton thread-safe trong Python thế nào?** Dùng `threading.Lock` với double-checked locking trong `__new__`. Hoặc dùng module-level instance (Python GIL bảo vệ module import).

2. **Factory Method vs Abstract Factory?** Factory Method: 1 method tạo 1 loại object. Abstract Factory: family các factory methods tạo nhiều loại object liên quan.

3. **Decorator Pattern vs Python @decorator?** Khác nhau. Design Pattern Decorator wrap object để thêm behavior. Python `@decorator` là syntactic sugar cho higher-order functions. Cùng ý tưởng nhưng khác cú pháp.

4. **Observer vs Pub/Sub khác nhau thế nào?** Observer: subscriber biết về publisher (direct coupling). Pub/Sub: qua message broker (event bus), publisher và subscriber không biết nhau.

5. **Strategy Pattern có thể thay bằng gì trong Python?** Dùng first-class functions — truyền function làm argument thay vì tạo Strategy class. Đơn giản hơn cho use case nhỏ.

6. **Khi nào dùng Builder thay vì constructor với default args?** Builder khi: nhiều required + optional params, cần validation phức tạp, object cần immutable sau khi build, cần fluent API.

7. **Adapter vs Facade khác nhau?** Adapter: make incompatible interface compatible (1-to-1). Facade: simplify complex subsystem với 1 interface đơn giản (1-to-many).
