# File System

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng ổ đĩa cứng như một tòa nhà văn phòng khổng lồ với hàng triệu căn phòng nhỏ đánh số (data blocks). File system là **hệ thống quản lý tòa nhà** đó:

- **File** là một tập hợp các căn phòng chứa dữ liệu của bạn (ảnh, văn bản, code...)
- **Inode** là tờ giấy chứng nhận quyền sở hữu: ghi tên chủ, kích thước, ngày tạo, và danh sách số phòng nào thuộc về file này
- **Directory (thư mục)** là bảng chỉ dẫn: "file A ở tờ giấy số 42, file B ở tờ giấy số 107..."
- **File descriptor** là chìa khóa phòng tạm thời: khi bạn mở file, OS cho bạn một chiếc chìa (số nguyên) để đọc/ghi, khi đóng thì trả lại

**Hard link** giống như hai bảng tên trên cùng một căn phòng — cả hai đều trỏ đến cùng dữ liệu thực sự, xóa một tên thì căn phòng vẫn còn.

**Symbolic link (soft link)** giống như tờ giấy note dán trên cửa ghi "đến phòng A205 mà lấy" — nó chỉ là lối tắt. Nếu phòng A205 bị xóa, tờ note trở nên vô dụng (dangling link).

**Journaling** giống như có một cuốn sổ ghi chép trước khi thực hiện mỗi thao tác. Nếu mất điện giữa chừng, hệ thống chỉ cần đọc sổ để biết phải làm gì hay rollback.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### File System Structure: Disk Layout

```
Disk được chia thành các block groups (ext4):
+--------+--------+----------+----------+------------+------------+
| Boot   | Super  | Group    | Block    | Inode      | Data       |
| Block  | Block  | Descriptors| Bitmap  | Table      | Blocks     |
+--------+--------+----------+----------+------------+------------+
```

- **Superblock**: metadata toàn bộ file system (magic number, block size, inode count, block count, mount count)
- **Block Bitmap**: bit map cho biết block nào đang free/used
- **Inode Bitmap**: bit map cho biết inode nào đang free/used
- **Inode Table**: mảng các inode structures

### Inode: Chứa Gì?

```c
struct ext4_inode {
    uint16_t  i_mode;        // File type + permissions (rwxrwxrwx)
    uint16_t  i_uid;         // Owner UID
    uint32_t  i_size_lo;     // File size (bytes)
    uint32_t  i_atime;       // Last access time
    uint32_t  i_ctime;       // inode change time
    uint32_t  i_mtime;       // Last modification time
    uint32_t  i_gid;         // Group ID
    uint16_t  i_links_count; // Hard link count
    uint32_t  i_blocks_lo;   // Block count (512B units)
    uint32_t  i_block[15];   // Pointers to data blocks
                             // [0..11]: direct blocks
                             // [12]:    single indirect block
                             // [13]:    double indirect block
                             // [14]:    triple indirect block
};
```

**Inode KHÔNG chứa**: tên file (tên nằm trong directory entry), data chính.

### Block Pointers: Direct và Indirect

Với block size 4KB, pointer size 4B (1K pointers/block):

```
Direct pointers:     12 blocks × 4KB = 48KB
Single indirect:     1024 blocks × 4KB = 4MB
Double indirect:     1024² blocks × 4KB = 4GB
Triple indirect:     1024³ blocks × 4KB = 4TB
```

→ ext4 dùng **extents** thay vì indirect blocks (cho file liên tục): lưu (start_block, length) → hiệu quả hơn cho large files.

### File Operations: Open → Read/Write → Close

```
open("file.txt", O_RDONLY):
1. Kernel tìm "file.txt" trong directory entries → lấy inode number
2. Load inode vào memory (inode cache)
3. Tạo entry trong Open File Table (system-wide): inode pointer, offset, flags
4. Tạo entry trong File Descriptor Table (per-process): index → open file entry
5. Trả về file descriptor (integer, ví dụ 3)

read(fd, buf, count):
1. Lookup fd → open file entry → inode
2. Tính block location từ current offset
3. Check buffer cache — nếu miss → load từ disk
4. Copy data vào user buffer
5. Cập nhật offset

close(fd):
1. Remove entry từ per-process FD table
2. Decrement reference count trong open file table
3. Nếu ref count = 0: flush dirty pages, free open file entry
   (Nếu link count = 0 và ref count = 0: xóa inode và giải phóng blocks)
```

**File Descriptor inheritance**: `fork()` → child inherit FD table (same open file entries → shared offset!). `exec()` → FD table preserved (trừ FD với O_CLOEXEC flag).

### Directory Structure

**Directory entry (ext4)**: `{inode_number, rec_len, name_len, file_type, name}`

```
Flat directory (mọi file trong một directory): đơn giản nhưng không scalable
Hierarchical (tree): Unix standard — root "/" → subdirs

Directory là file đặc biệt: data blocks chứa directory entries
ls thực chất: open directory → read entries → lookup inodes
```

**Hash-tree directories** (htree trong ext4): O(1) lookup thay vì O(n) linear scan cho dir với nhiều file.

### Hard Link vs Symbolic Link

| Tiêu chí | Hard Link | Symbolic Link |
|---------|----------|--------------|
| Trỏ đến | Inode number | Path string |
| Cross-filesystem | Không (cùng inode table) | Có |
| Directory link | Không (tránh cycles) | Có |
| Link count | Tăng inode link count | Không ảnh hưởng |
| File bị xóa | Hard link vẫn valid | Dangling link |
| `ls -l` | Không có indicator đặc biệt | `l` prefix, hiện target |
| Kích thước link | = inode size | = length của target path |

```bash
# Hard link: cùng inode
ln original.txt hardlink.txt
ls -li  # Cùng inode number

# Soft link: khác inode
ln -s original.txt softlink.txt
ls -li  # Khác inode number, softlink.txt → original.txt
```

### File Systems So Sánh

| | FAT32 | NTFS | ext4 | APFS |
|--|-------|------|------|------|
| OS | Windows legacy | Windows | Linux | macOS/iOS |
| Max file size | 4GB | 16TB | 16TB | 8EB |
| Max volume | 8TB | 256TB | 1EB | 16EB |
| Journaling | Không | Có | Có | Có (CoW) |
| Permissions | Không | ACL | POSIX | POSIX + ACL |
| Case sensitive | Không | Optional | Có | Optional |
| Encryption | Không | BitLocker | fscrypt | Native |
| Symlinks | Không | Có | Có | Có |
| Fragmentation | Dễ bị | Ít | Rất ít | Không (CoW) |

### Journaling: Write-Ahead Logging

**Vấn đề**: Update metadata (inode + bitmap) và data cần nhiều write operations. Nếu crash giữa chừng → inconsistent state (block marked used nhưng inode chưa point đến, hoặc ngược lại).

**Journaling modes** (ext4):

| Mode | Journal gì | Performance | Safety |
|------|-----------|------------|--------|
| `data=writeback` | Metadata only | Cao nhất | Thấp nhất |
| `data=ordered` (default) | Metadata, data written first | Trung bình | Tốt |
| `data=journal` | Cả metadata và data | Thấp | Cao nhất |

**WAL (Write-Ahead Logging) process**:
```
1. Write transaction BEGIN to journal
2. Write all changes to journal (log)
3. Write COMMIT to journal
4. Apply changes to actual filesystem (checkpoint)
5. Free journal space
```
Nếu crash ở bước 1-2: rollback (ignore uncommitted journal entries)
Nếu crash ở bước 4: replay journal entries (redo)

### Disk Scheduling Algorithms

Tối thiểu hóa **seek time** (di chuyển đầu đọc):

| Algorithm | Cơ chế | Ưu điểm | Nhược điểm |
|-----------|--------|---------|-----------|
| **FCFS** | Phục vụ theo thứ tự request đến | Công bằng, đơn giản | Seek time cao nếu requests phân tán |
| **SSTF** | Phục vụ request gần đầu đọc nhất | Seek time thấp | Starvation cho requests xa |
| **SCAN (Elevator)** | Di chuyển một chiều, phục vụ all requests trên đường, đảo chiều ở cuối | Không starvation | Wait time không uniform |
| **C-SCAN** | Chỉ phục vụ khi đi một chiều, nhảy về đầu | Wait time đồng đều hơn | Overhead khi nhảy về |
| **LOOK/C-LOOK** | Như SCAN/C-SCAN nhưng chỉ đến request ngoài cùng, không đến cylinder cuối | Hiệu quả hơn SCAN | Phức tạp hơn |

**Disk Access Time = Seek Time + Rotational Latency + Transfer Time**
- Seek time: di chuyển đầu đọc đến đúng track (thống trị, ~1-10ms cho HDD)
- Rotational latency: chờ sector xoay đến (~4ms cho 7200RPM)
- Transfer time: thực sự đọc data (ít nhất)
- **SSD**: không có seek time hay rotational latency → FCFS thường đủ tốt

### Mount: Mounting File System

```bash
mount /dev/sdb1 /mnt/usb    # Mount partition vào mount point
mount -t ext4 /dev/sda2 /   # Mount root filesystem

# /proc/mounts hoặc /etc/mtab: list mounted filesystems
```

Kernel duy trì **VFS (Virtual File System)** layer: abstraction cho phép multiple filesystem types (ext4, ntfs, tmpfs, procfs, sysfs) hoạt động thống nhất qua cùng system call interface.

---

## Định nghĩa chính xác

**Inode (Index Node)**: Cấu trúc dữ liệu trong file system Unix lưu trữ metadata của file (permissions, ownership, timestamps, size, pointers to data blocks) nhưng không lưu tên file.

**File Descriptor**: Số nguyên non-negative là handle cho một open file trong per-process file descriptor table. FD 0 = stdin, 1 = stdout, 2 = stderr.

**Journaling**: Kỹ thuật đảm bảo file system consistency bằng cách ghi các thay đổi vào journal (log) trước khi áp dụng vào file system chính, cho phép recovery sau crash.

**VFS (Virtual File System)**: Abstraction layer trong kernel cung cấp giao diện thống nhất cho nhiều loại file system khác nhau.

---

## Bảng so sánh / Sơ đồ kỹ thuật

### Disk Scheduling — Ví dụ Trực Quan

```
Disk head tại cylinder 50. Requests: [98, 183, 37, 122, 14, 124, 65, 67]

FCFS:   50→98→183→37→122→14→124→65→67   Total movement = 640
SSTF:   50→65→67→37→14→98→122→124→183   Total movement = 236
SCAN:   50→65→67→98→122→124→183→37→14   (đi phải trước)
        Total movement = 208
C-SCAN: 50→65→67→98→122→124→183→14→37   (nhảy về 0 rồi đi phải)
        Total movement = 382 (nhưng wait time đồng đều hơn)
```

### File System Layers

```
User Space:    open() read() write() close()
               ↓
Kernel:        System Call Interface
               ↓
               VFS Layer (common abstractions: inode, dentry, file, superblock)
               ↓
        ┌──────┼──────┬──────┐
       ext4   ntfs  tmpfs  procfs   (concrete file system implementations)
        ↓
       Block Layer (I/O scheduler, block device driver)
        ↓
       Storage Hardware (HDD, SSD, NVMe)
```

### inode Pointer Structure

```
inode.i_block[]:
  [0]-[11]: Direct blocks (12 × 4KB = 48KB)
  [12]: → Indirect block → [block1][block2]...[block1024]
                                                   ↕ each 4KB
  [13]: → Double indirect → [indir1][indir2]...
                                 ↓
                           [block1]...[block1024]
  [14]: → Triple indirect (very large files)
```

---

## Code mẫu

```python
import os
import stat
import time

# ============================================================
# 1. Thao tác file cơ bản và File Descriptor
# ============================================================

print("=" * 60)
print("File Operations Demo")
print("=" * 60)

# Tạo file demo
test_file = "/tmp/fs_demo.txt"
with open(test_file, "w") as f:
    f.write("Hello, File System!\nLine 2\nLine 3\n")

# Lấy thông tin inode
file_stat = os.stat(test_file)
print(f"\ninode info for {test_file}:")
print(f"  inode number:    {file_stat.st_ino}")
print(f"  size:            {file_stat.st_size} bytes")
print(f"  link count:      {file_stat.st_nlink}")
print(f"  permissions:     {oct(stat.S_IMODE(file_stat.st_mode))}")
print(f"  uid/gid:         {file_stat.st_uid}/{file_stat.st_gid}")
print(f"  atime:           {time.ctime(file_stat.st_atime)}")
print(f"  mtime:           {time.ctime(file_stat.st_mtime)}")
print(f"  block count:     {file_stat.st_blocks} (512B units)")


# ============================================================
# 2. Hard Link vs Symbolic Link
# ============================================================

hard_link = "/tmp/fs_hardlink.txt"
soft_link = "/tmp/fs_softlink.txt"

# Tạo hard link
if os.path.exists(hard_link):
    os.remove(hard_link)
os.link(test_file, hard_link)

# Tạo symbolic link
if os.path.exists(soft_link):
    os.remove(soft_link)
os.symlink(test_file, soft_link)

print(f"\nHard link vs Symbolic Link:")
print(f"  Original  inode: {os.stat(test_file).st_ino}, links={os.stat(test_file).st_nlink}")
print(f"  Hard link inode: {os.stat(hard_link).st_ino}, links={os.stat(hard_link).st_nlink}")
print(f"  Soft link inode: {os.lstat(soft_link).st_ino} (link itself), target={os.readlink(soft_link)}")
print(f"  Same inode? hard={os.stat(test_file).st_ino == os.stat(hard_link).st_ino}")

# Xóa original — hard link vẫn readable
os.remove(test_file)
print(f"\nAfter deleting original:")
try:
    with open(hard_link) as f:
        print(f"  Hard link still readable: '{f.readline().strip()}'")
except Exception as e:
    print(f"  Hard link error: {e}")

try:
    with open(soft_link) as f:
        print(f"  Soft link still readable: OK")
except FileNotFoundError:
    print(f"  Soft link: DANGLING (target deleted)")

# Cleanup
for path in [hard_link, soft_link]:
    if os.path.exists(path) or os.path.islink(path):
        os.remove(path)


# ============================================================
# 3. Mô phỏng Disk Scheduling Algorithms
# ============================================================

def disk_seek_distance(sequence):
    """Tính tổng quãng đường đầu đọc di chuyển"""
    return sum(abs(sequence[i+1] - sequence[i]) for i in range(len(sequence)-1))

def fcfs_disk(head, requests):
    sequence = [head] + requests
    return sequence, disk_seek_distance(sequence)

def sstf_disk(head, requests):
    """Shortest Seek Time First"""
    remaining = requests[:]
    sequence = [head]
    current = head
    
    while remaining:
        # Tìm request gần nhất
        closest = min(remaining, key=lambda x: abs(x - current))
        sequence.append(closest)
        current = closest
        remaining.remove(closest)
    
    return sequence, disk_seek_distance(sequence)

def scan_disk(head, requests, disk_size=200, direction="right"):
    """SCAN (Elevator) Algorithm"""
    left = sorted([r for r in requests if r < head], reverse=True)
    right = sorted([r for r in requests if r >= head])
    
    if direction == "right":
        sequence = [head] + right + [disk_size - 1] + left
    else:
        sequence = [head] + left + [0] + right
    
    return sequence, disk_seek_distance(sequence)

def cscan_disk(head, requests, disk_size=200):
    """C-SCAN Algorithm"""
    right = sorted([r for r in requests if r >= head])
    left = sorted([r for r in requests if r < head])
    
    # Đi hết sang phải, nhảy về 0, tiếp tục
    sequence = [head] + right + [disk_size - 1, 0] + left
    return sequence, disk_seek_distance(sequence)


print("\n" + "=" * 60)
print("Disk Scheduling Algorithms")
print("=" * 60)

head_pos = 50
requests = [98, 183, 37, 122, 14, 124, 65, 67]
print(f"Head: {head_pos}, Requests: {requests}\n")

for name, func, args in [
    ("FCFS",   fcfs_disk,  (head_pos, requests)),
    ("SSTF",   sstf_disk,  (head_pos, requests)),
    ("SCAN",   scan_disk,  (head_pos, requests)),
    ("C-SCAN", cscan_disk, (head_pos, requests)),
]:
    seq, dist = func(*args)
    print(f"{name:8}: total_seek={dist:4}  sequence={seq}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**ext4 — Dùng khi:**
- General-purpose Linux server/desktop
- Cần tương thích rộng rãi, mature, well-tested
- Daily drives, boot partitions

**ZFS/Btrfs — Dùng khi:**
- Cần built-in RAID, snapshots, checksumming
- Storage servers, NAS
- Data integrity quan trọng hàng đầu

**FAT32 — Dùng khi:**
- USB drives cần tương thích cross-platform
- Embedded devices, bootloaders (UEFI ESP partition)

**tmpfs — Dùng khi:**
- /tmp, /dev/shm — temporary files cần tốc độ cao
- Build systems (mount /tmp as tmpfs)

**SSTF Disk Scheduling — Không dùng khi:**
- Có requests quan trọng ở xa — starvation risk
- Thay vào đó dùng SCAN/C-LOOK

**Journaling data=writeback — Không dùng khi:**
- Dữ liệu quan trọng, consistency cao — dùng ordered hoặc journal mode

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm inode chứa tên file**: Tên file chỉ nằm trong directory entry. Inode chứa metadata và data pointers, không có filename.
- **Hard link không thể cross-filesystem**: Hard link chia sẻ inode number — inode number chỉ unique trong một file system. Để link cross-filesystem phải dùng soft link.
- **File bị xóa nhưng disk space không giải phóng**: Nếu process đang giữ file descriptor open, file chỉ bị unlink (directory entry removed) nhưng dữ liệu vẫn còn đến khi tất cả FDs được close. `lsof | grep deleted` để tìm.
- **Không đóng file sau khi dùng**: Mỗi process có giới hạn số FDs (ulimit -n, default 1024). Để file leak → "Too many open files" error.
- **Symlink loop**: A → B → A → B... Kernel giới hạn số lần follow symlink (MAXSYMLINKS = 40 trên Linux).
- **Nhầm atime, mtime, ctime**: atime = last access; mtime = last data modification; ctime = last inode change (metadata change, không phải creation time). Trên Linux không có creation time trong ext4.
- **SSTF starvation**: Continuous stream of requests ở một vùng disk sẽ làm requests ở vùng khác không bao giờ được phục vụ.

---

## Câu hỏi phỏng vấn hay gặp

**Cơ bản:**
1. Inode là gì? Nó chứa những gì và không chứa gì?
2. Hard link vs symbolic link — phân biệt và ví dụ use case.
3. Khi bạn gọi `open()`, điều gì xảy ra bên trong kernel?
4. Tại sao xóa file không giải phóng disk space ngay nếu còn process mở file đó?

**Trung bình:**
5. ext4 vs FAT32 — dùng cái nào khi nào?
6. Journaling giải quyết vấn đề gì? Ordered vs writeback mode khác nhau thế nào?
7. Tại sao SCAN tốt hơn SSTF cho disk scheduling?
8. Inode pointers: direct, indirect, double indirect — tính max file size với block size 4KB.

**Nâng cao:**
9. VFS layer trong Linux làm gì? Tại sao cần?
10. Describe một scenario "disk full" mặc dù `df` cho thấy có space (hint: inode exhaustion).
11. `mmap()` vs `read()/write()` — tradeoffs?
12. Log-structured file systems (LFS) khác journaling thế nào? Ưu/nhược điểm?
13. SSD wear leveling ảnh hưởng thế nào đến file system design?
