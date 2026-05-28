# OOP Roadmap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           OOP LEARNING PATH                             │
│                   Mũi tên = cần học trước (prerequisite)                │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  🧱 01 · Class &    │
                        │       Object        │
                        │  (nền tảng của OOP) │
                        └──────┬──────┬───────┘
                               │      │
                     ┌─────────┘      └──────────┐
                     │                           │
            ┌────────▼─────────┐       ┌─────────▼────────┐
            │  🔒 02 ·         │       │  👨‍👩‍👦 03 ·         │
            │  Encapsulation   │       │  Inheritance      │
            │  (đóng gói)      │       │  (kế thừa)        │
            └────────┬─────────┘       └─────────┬─────────┘
                     │                           │
                     └────────────┬──────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🎭 04 ·             │
                        │  Polymorphism        │
                        │  (đa hình)           │
                        └─────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🎨 05 ·             │
                        │  Abstraction         │
                        │  (trừu tượng hóa)    │
                        └─────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  📜 06 · Interface   │
                        │  & Abstract Class    │
                        └─────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🏗️ 07 · SOLID       │
                        │  Principles          │
                        └─────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🎯 08 · Design      │
                        │  Patterns            │
                        └──────────────────────┘
```

---

## Giải thích từng topic

| # | Topic | Mô tả ngắn | Tài liệu |
|---|-------|------------|----------|
| 01 | 🧱 Class & Object | Class = bản thiết kế, Object = thực thể — attribute, method, constructor | [📖 Lý thuyết](01_class_object/ly_thuyet.md) · [📝 Trắc nghiệm](01_class_object/trac_nghiem.md) |
| 02 | 🔒 Encapsulation | Giấu dữ liệu bên trong — private/public, getter/setter, giảm coupling | [📖 Lý thuyết](02_encapsulation/ly_thuyet.md) · [📝 Trắc nghiệm](02_encapsulation/trac_nghiem.md) |
| 03 | 👨‍👩‍👦 Inheritance | Lớp con kế thừa lớp cha — IS-A, override method, tái sử dụng code | [📖 Lý thuyết](03_inheritance/ly_thuyet.md) · [📝 Trắc nghiệm](03_inheritance/trac_nghiem.md) |
| 04 | 🎭 Polymorphism | Cùng 1 interface, nhiều hành vi khác nhau — override (runtime) & overload (compile) | [📖 Lý thuyết](04_polymorphism/ly_thuyet.md) · [📝 Trắc nghiệm](04_polymorphism/trac_nghiem.md) |
| 05 | 🎨 Abstraction | Ẩn chi tiết cài đặt, chỉ expose những gì cần thiết — giảm độ phức tạp | [📖 Lý thuyết](05_abstraction/ly_thuyet.md) · [📝 Trắc nghiệm](05_abstraction/trac_nghiem.md) |
| 06 | 📜 Interface & Abstract | Interface = hợp đồng thuần túy, Abstract = bản thiết kế bán hoàn chỉnh | [📖 Lý thuyết](06_interface_abstract/ly_thuyet.md) · [📝 Trắc nghiệm](06_interface_abstract/trac_nghiem.md) |
| 07 | 🏗️ SOLID Principles | 5 nguyên tắc thiết kế OOP — viết code dễ mở rộng, dễ test, ít bug | [📖 Lý thuyết](07_solid_principles/ly_thuyet.md) · [📝 Trắc nghiệm](07_solid_principles/trac_nghiem.md) |
| 08 | 🎯 Design Patterns | 23 mẫu thiết kế Gang of Four — Creational, Structural, Behavioral | [📖 Lý thuyết](08_design_patterns/ly_thuyet.md) · [📝 Trắc nghiệm](08_design_patterns/trac_nghiem.md) |

---

## Chi tiết SOLID Principles

```
  🏗️ SOLID
      │
      ├──▶  S  ─  Single Responsibility  ─  1 class, 1 nhiệm vụ
      │
      ├──▶  O  ─  Open / Closed          ─  mở để mở rộng, đóng để sửa đổi
      │
      ├──▶  L  ─  Liskov Substitution    ─  subclass thay được superclass
      │
      ├──▶  I  ─  Interface Segregation  ─  nhiều interface nhỏ > 1 interface to
      │
      └──▶  D  ─  Dependency Inversion   ─  phụ thuộc vào abstraction, không phải impl
```

---

## Chi tiết Design Patterns

```
  🎯 Design Patterns
        │
        ├──▶  ⚙️  Creational  (tạo object)
        │         │
        │         ├──  Singleton    ─  1 instance duy nhất toàn hệ thống
        │         ├──  Factory      ─  delegate việc tạo object cho subclass
        │         └──  Builder      ─  tạo object phức tạp từng bước
        │
        ├──▶  🏗️  Structural  (ghép object)
        │         │
        │         ├──  Adapter      ─  convert interface này sang interface khác
        │         ├──  Decorator    ─  thêm hành vi mà không sửa class gốc
        │         └──  Composite    ─  cây phân cấp, treat đồng nhất leaf & branch
        │
        └──▶  🎭  Behavioral  (giao tiếp giữa objects)
                  │
                  ├──  Observer     ─  subscribe/notify (event listener)
                  ├──  Strategy     ─  đổi thuật toán lúc runtime
                  └──  Command      ─  đóng gói action thành object
```

---

## Lộ trình theo giai đoạn

```
  Giai đoạn 1  ──▶  Class & Object  ──▶  Encapsulation  ──▶  Inheritance
                                                                    │
  Giai đoạn 2  ◀───────────────────────────────────────────────────┘
       │
       ▼
  Polymorphism  ──▶  Abstraction
                          │
  Giai đoạn 3  ◀──────────┘
       │
       ▼
  Interface & Abstract Class  ──▶  Dependency Injection
                                          │
  Giai đoạn 4  ◀───────────────────────────┘
       │
       ▼
  SOLID Principles  ──▶  Design Patterns
                                │
                          ✅ Senior Dev Level
```
