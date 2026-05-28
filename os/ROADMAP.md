# OS Roadmap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      OPERATING SYSTEM LEARNING PATH                     │
│                   Mũi tên = cần học trước (prerequisite)                │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  ⚙️ 01 · Process &  │
                        │      Thread         │
                        │  (nền tảng của OS)  │
                        └──────┬──────┬───────┘
                               │      │
                     ┌─────────┘      └──────────┐
                     │                           │
            ┌────────▼─────────┐       ┌─────────▼────────┐
            │  🧠 02 ·         │       │  📅 03 ·          │
            │  Memory          │       │  Scheduling       │
            │  Management      │       │  (lập lịch CPU)   │
            └────────┬─────────┘       └─────────┬─────────┘
                     │                           │
                     └────────────┬──────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🔒 04 · Deadlock    │
                        │  (bế tắc tài nguyên) │
                        └─────────┬────────────┘
                                  │
                     ┌────────────┴────────────┐
                     │                         │
            ┌────────▼─────────┐     ┌─────────▼────────┐
            │  📁 05 ·         │     │  🔄 06 ·          │
            │  File System     │     │  Synchronization  │
            │  (quản lý file)  │     │  (đồng bộ hóa)    │
            └──────────────────┘     └──────────────────┘
```

---

## Giải thích từng topic

| # | Topic | Mô tả ngắn | Tài liệu |
|---|-------|------------|----------|
| 01 | ⚙️ Process & Thread | Process = chương trình đang chạy, Thread = luồng xử lý trong process — context switch, PCB | [📖 Lý thuyết](01_process_thread/ly_thuyet.md) · [📝 Trắc nghiệm](01_process_thread/trac_nghiem.md) |
| 02 | 🧠 Memory Management | RAM ảo, phân trang (paging), phân đoạn (segmentation), page fault, swap | [📖 Lý thuyết](02_memory_management/ly_thuyet.md) · [📝 Trắc nghiệm](02_memory_management/trac_nghiem.md) |
| 03 | 📅 Scheduling | FCFS, SJF, Round Robin, Priority — chọn process nào chạy tiếp theo | [📖 Lý thuyết](03_scheduling/ly_thuyet.md) · [📝 Trắc nghiệm](03_scheduling/trac_nghiem.md) |
| 04 | 🔒 Deadlock | 4 điều kiện Coffman, phát hiện & phòng ngừa deadlock, banker's algorithm | [📖 Lý thuyết](04_deadlock/ly_thuyet.md) · [📝 Trắc nghiệm](04_deadlock/trac_nghiem.md) |
| 05 | 📁 File System | inode, directory tree, FAT/NTFS/ext4, read/write syscall, buffer cache | [📖 Lý thuyết](05_file_system/ly_thuyet.md) · [📝 Trắc nghiệm](05_file_system/trac_nghiem.md) |
| 06 | 🔄 Synchronization | Race condition, mutex, semaphore, monitor, spinlock — tránh data corruption | [📖 Lý thuyết](06_synchronization/ly_thuyet.md) · [📝 Trắc nghiệm](06_synchronization/trac_nghiem.md) |

---

## Lộ trình theo giai đoạn

```
  Giai đoạn 1  ──▶  Process & Thread
                          │
                          ▼
         ┌────────────────┴────────────────┐
         │                                 │
  Memory Management              CPU Scheduling
         │                                 │
         └────────────────┬────────────────┘
                          │
                          ▼
                       Deadlock
                          │
                          ▼
            ┌─────────────┴─────────────┐
            │                           │
       File System               Synchronization
            │                           │
            └─────────────┬─────────────┘
                          │
                    ✅ OS Fundamentals
                       Interview Ready
```

---

## Quan hệ giữa các topic

```
  Process & Thread  ─────▶  Scheduling     (OS chọn thread nào chạy)
  Process & Thread  ─────▶  Synchronization (nhiều thread → race condition)
  Memory Management ─────▶  Deadlock        (tranh giành tài nguyên bộ nhớ)
  Scheduling        ─────▶  Deadlock        (hold & wait khi chờ CPU)
  Synchronization   ◀─────  Deadlock        (mutex sai → deadlock)
  File System       ─────▶  Synchronization (concurrent read/write)
```
