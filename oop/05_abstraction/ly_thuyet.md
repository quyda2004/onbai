# Abstraction (Trừu tượng hóa)

---

## Giải thích cho người mới hoàn toàn

Khi bạn lái xe, bạn chỉ cần biết: **vô lăng để lái, chân ga để tăng tốc, chân phanh để dừng**. Bạn không cần hiểu bên trong động cơ hoạt động ra sao, hộp số chuyển từng cấp thế nào, hay hệ thống phanh thủy lực vận hành thế nào.

Người thiết kế xe đã **ẩn đi sự phức tạp**, chỉ để lộ ra **giao diện đơn giản** mà người lái cần.

Đó chính là Abstraction trong lập trình: **giấu đi chi tiết triển khai bên trong, chỉ hiển thị những gì người dùng thực sự cần**. Như việc dùng điều khiển TV — bạn chỉ thấy nút, không thấy mạch điện.

Ví dụ khác:
- ATM: Bạn chỉ cần nhét thẻ, nhập PIN, rút tiền — không cần biết hệ thống ngân hàng phía sau.
- Google Search: Bạn chỉ cần gõ từ khóa — không cần biết cả trăm server đang xử lý.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Abstraction là gì chính xác?

Abstraction là quá trình **xác định giao diện (interface)** — tức là "**làm gì**" — và **che giấu implementation** — tức là "**làm thế nào**". Mục tiêu:

1. **Giảm cognitive load**: Developer dùng module chỉ cần biết API, không cần đọc source.
2. **Loose coupling**: Thay đổi implementation không ảnh hưởng consumer.
3. **Interchangeability**: Có thể swap implementation mà không đổi code dùng interface.

### Abstract Class trong Python với ABC

Python dùng module `abc` (Abstract Base Class) để tạo abstract class:

- `ABC` hoặc `metaclass=ABCMeta`: Đánh dấu class là abstract.
- `@abstractmethod`: Buộc subclass phải override method này, nếu không sẽ không thể instantiate.
- `@abstractproperty` (deprecated): Dùng `@property` + `@abstractmethod` thay thế.

**Không thể instantiate abstract class trực tiếp** — Python raise `TypeError`.

### Abstract class vs Concrete class

```
AbstractClass (define "what")
    ↓ implements "how"
ConcreteClass (usable instance)
```

### Abstraction vs Encapsulation — Điểm hay bị nhầm lẫn

| | Abstraction | Encapsulation |
|-|-------------|---------------|
| **Mục tiêu** | Giấu complexity, expose interface | Bảo vệ data, kiểm soát access |
| **Trả lời câu hỏi** | "Làm gì?" (what) | "Bảo vệ như thế nào?" (how to protect) |
| **Thực hiện qua** | Abstract class, Interface | Private/protected, getter/setter |
| **Ví dụ** | `connect()` — không cần biết driver | `_password` — không cho đọc trực tiếp |
| **Level** | Design level (thiết kế) | Implementation level (triển khai) |

**Cách nhớ**: Abstraction là **ẩn complexity** với người dùng bên ngoài. Encapsulation là **bảo vệ state** bên trong object.

### Levels of Abstraction

Trong hệ thống thực tế, abstraction có nhiều tầng:
```
Application Code  ← gọi DatabaseConnector.query()
DatabaseConnector ← abstract: hide MySQL/Postgres differences
MySQL Driver      ← hide network protocol details
TCP/IP Stack      ← hide physical transmission
```

### Template Method Pattern — Abstraction trong action

Abstract class thường dùng Template Method: định nghĩa **skeleton của algorithm**, để subclass fill in các bước cụ thể.

---

## Định nghĩa chính xác

**Abstraction** là một trong bốn trụ cột của OOP, là quá trình **mô hình hóa các đối tượng bằng cách chỉ giữ lại các thuộc tính và hành vi liên quan** đến ngữ cảnh cụ thể, đồng thời **ẩn đi các chi tiết triển khai không cần thiết**. Trong Python, abstraction được thực hiện qua Abstract Base Class (`abc` module) và `@abstractmethod` decorator.

---

## So sánh / Bảng kỹ thuật

### Abstraction vs Encapsulation (chi tiết)

| Tiêu chí | Abstraction | Encapsulation |
|----------|-------------|---------------|
| Khái niệm | Design concept | Implementation concept |
| Ẩn gì | Chi tiết triển khai (HOW) | Data & internal state |
| Công cụ Python | ABC, @abstractmethod | `_private`, `__dunder`, property |
| Lợi ích | Loose coupling, interchangeable | Data integrity, security |
| Ví dụ | `Shape.area()` — không biết tính thế nào | `_balance` — không cho sửa trực tiếp |

### Các mức độ Abstract

| Loại | Có thể instantiate | Có implementation | Dùng khi |
|------|--------------------|-------------------|----------|
| Concrete class | Có | Tất cả methods | Lớp cuối cùng dùng trực tiếp |
| Abstract class (partial) | Không | Một số method có, một số abstract | Chia sẻ code chung + enforce interface |
| Pure abstract / Interface | Không | Không có (chỉ khai báo) | Chỉ define contract |

### Độ phức tạp

| Thao tác | Best Case | Worst Case | Space | Ghi chú |
|----------|-----------|------------|-------|---------|
| Instantiate concrete subclass | O(1) | O(n) | O(n) | n = số attributes khởi tạo |
| Abstract method check (Python) | O(m) | O(m) | O(1) | m = số abstract methods khi class được define |
| Method lookup (abstract → concrete) | O(1) | O(d) | O(1) | d = depth MRO, thường cache O(1) |

---

## Code mẫu

```python
from abc import ABC, abstractmethod
from typing import Optional

# ===== ABSTRACT CLASS — DatabaseConnector =====

class DatabaseConnector(ABC):
    """
    Abstract class — định nghĩa interface chung cho mọi loại DB.
    Ẩn đi toàn bộ chi tiết kết nối cụ thể.
    """

    def __init__(self, host: str, port: int, database: str):
        self.host = host
        self.port = port
        self.database = database
        self._connection = None            # Private state

    # Abstract methods — subclass PHẢI implement
    @abstractmethod
    def connect(self) -> bool:
        """Kết nối đến database — HOW là do từng subclass quyết định"""
        pass

    @abstractmethod
    def disconnect(self) -> None:
        pass

    @abstractmethod
    def execute(self, query: str, params: tuple = ()) -> list:
        pass

    # Abstract property
    @property
    @abstractmethod
    def driver_name(self) -> str:
        """Tên driver, mỗi DB có tên khác nhau"""
        pass

    # Concrete method — dùng chung, gọi các abstract methods
    def query(self, sql: str, params: tuple = ()) -> Optional[list]:
        """Template method — người dùng chỉ cần gọi cái này"""
        if not self._connection:
            self.connect()                 # Delegate to subclass
        try:
            results = self.execute(sql, params)   # Delegate to subclass
            return results
        except Exception as e:
            print(f"[{self.driver_name}] Query failed: {e}")
            return None

    def __repr__(self):
        return f"{self.__class__.__name__}({self.host}:{self.port}/{self.database})"


# ===== CONCRETE IMPLEMENTATIONS =====

class MySQLConnector(DatabaseConnector):
    """MySQL implementation — hide MySQL-specific details"""

    def connect(self) -> bool:
        # Thực tế sẽ dùng mysql-connector-python
        self._connection = f"mysql://{self.host}:{self.port}/{self.database}"
        print(f"[MySQL] Kết nối tới {self._connection}")
        return True

    def disconnect(self) -> None:
        self._connection = None
        print("[MySQL] Đã ngắt kết nối")

    def execute(self, query: str, params: tuple = ()) -> list:
        print(f"[MySQL] Executing: {query} | params={params}")
        return [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]

    @property
    def driver_name(self) -> str:
        return "MySQL 8.0"


class PostgreSQLConnector(DatabaseConnector):
    """PostgreSQL implementation — hide Postgres-specific details"""

    def __init__(self, host, port, database, schema: str = "public"):
        super().__init__(host, port, database)
        self.schema = schema               # Postgres-specific attribute

    def connect(self) -> bool:
        self._connection = f"postgresql://{self.host}:{self.port}/{self.database}"
        print(f"[PostgreSQL] Kết nối tới {self._connection} (schema={self.schema})")
        return True

    def disconnect(self) -> None:
        self._connection = None
        print("[PostgreSQL] Đã ngắt kết nối")

    def execute(self, query: str, params: tuple = ()) -> list:
        print(f"[PostgreSQL] Executing: {query} | params={params}")
        return [{"id": 10, "email": "alice@example.com"}]

    @property
    def driver_name(self) -> str:
        return "PostgreSQL 15"


# ===== CLIENT CODE — không cần biết MySQL hay Postgres =====

def get_users(db: DatabaseConnector) -> list:
    """
    Hàm này chỉ biết 'DatabaseConnector interface'.
    Không quan tâm là MySQL hay PostgreSQL — đó là abstraction.
    """
    return db.query("SELECT * FROM users") or []

# Swap implementation mà không đổi client code:
mysql_db = MySQLConnector("localhost", 3306, "myapp")
postgres_db = PostgreSQLConnector("prod-server", 5432, "myapp", schema="app")

print("=== Dùng MySQL ===")
users = get_users(mysql_db)
print(f"Lấy được {len(users)} users")

print("\n=== Dùng PostgreSQL ===")
users = get_users(postgres_db)
print(f"Lấy được {len(users)} users")

# Không thể instantiate abstract class:
try:
    db = DatabaseConnector("localhost", 5432, "test")
except TypeError as e:
    print(f"\nLỗi đúng như mong đợi: {e}")
    # TypeError: Can't instantiate abstract class DatabaseConnector
    # with abstract methods connect, disconnect, driver_name, execute


# ===== ABSTRACT PROPERTY =====

class Animal(ABC):
    @property
    @abstractmethod
    def sound(self) -> str:
        pass

    def speak(self):
        print(f"Tôi nói: {self.sound}")    # sound là abstract property

class Dog(Animal):
    @property
    def sound(self) -> str:
        return "Woof!"

Dog().speak()   # "Tôi nói: Woof!"
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Có nhiều implementation của cùng một concept (MySQL/PostgreSQL, HTTP/HTTPS client, v.v.).
- Muốn định nghĩa contract rõ ràng mà các subclass phải tuân theo.
- Cần loose coupling — business logic không phụ thuộc vào concrete implementation.
- Viết library/framework mà người khác sẽ extend.
- Muốn enable dependency injection và testability (mock abstract class dễ hơn).

**Không dùng khi:**
- Chỉ có một implementation duy nhất và không có kế hoạch thay đổi — over-engineering.
- Class quá đơn giản, không cần enforce interface.
- Duck typing đã đủ (Python không yêu cầu kế thừa để có interface chung).

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm Abstraction với Encapsulation**: Abstraction = ẩn complexity (WHAT vs HOW). Encapsulation = bảo vệ data. Hai khái niệm khác nhau, thường hoạt động cùng nhau.
- **Tạo abstract class rồi không dùng**: Khai báo `@abstractmethod` nhưng subclass vẫn không implement đủ → instantiate sẽ raise `TypeError`.
- **Abstract class có quá nhiều concrete logic**: Abstract class nên minimal về concrete code. Nếu có quá nhiều, xem xét tách thành interface + helper class.
- **Không dùng `@property` + `@abstractmethod` đúng thứ tự**: Phải là `@property` trước, `@abstractmethod` sau trong Python 3.3+. Nếu đảo ngược, property không abstract.
- **Quên import ABC**: `from abc import ABC, abstractmethod` — thiếu import sẽ không raise lỗi khi instantiate.
- **Nhầm `ABC` với `ABCMeta`**: `class Foo(ABC)` là cách modern. `class Foo(metaclass=ABCMeta)` là cách cũ — tương đương nhưng verbose hơn.

---

## Câu hỏi phỏng vấn hay gặp

1. **Abstraction vs Encapsulation — khác nhau thế nào?** Abstraction giấu complexity (design level). Encapsulation giấu data (implementation level). Abstraction trả lời "làm gì", encapsulation trả lời "bảo vệ thế nào".

2. **Khi nào dùng abstract class, khi nào dùng interface (hoặc Protocol trong Python)?** Abstract class khi có shared implementation + enforce interface. Protocol/Interface khi chỉ cần define contract, không share code.

3. **Có thể instantiate abstract class không?** Không — Python raise `TypeError`. Phải subclass và implement tất cả `@abstractmethod`.

4. **Sự khác nhau giữa abstract class và concrete class?** Abstract class có ít nhất 1 `@abstractmethod`, không thể instantiate trực tiếp, dùng để define interface. Concrete class implement tất cả methods và có thể instantiate.

5. **Tại sao Abstraction giúp code dễ maintain?** Vì consumer code phụ thuộc vào interface (stable), không phụ thuộc vào implementation (có thể thay đổi). Thay database driver chỉ cần viết class mới, không sửa business logic.

6. **Cho ví dụ abstraction trong thực tế production.** ORM (SQLAlchemy session ẩn SQL), HTTP client library (requests ẩn socket), cloud storage SDK (S3/GCS cùng interface), payment gateway abstraction.

7. **Template Method Pattern liên quan đến abstraction thế nào?** Template Method dùng abstract class để define skeleton algorithm — concrete steps được abstract ra và delegate cho subclass. Đây là ví dụ điển hình của abstraction trong action.
