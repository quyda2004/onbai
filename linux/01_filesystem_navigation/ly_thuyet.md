# Linux Filesystem Navigation — Điều hướng Hệ thống Tập tin

---

## Giải thích cho người mới hoàn toàn

Hãy hình dung máy tính của bạn giống như một tòa nhà lớn. Tòa nhà đó có một cổng chính duy nhất gọi là `/` (root — gốc). Từ cổng này bạn đi vào các tầng, phòng khác nhau: tầng `/home` là nơi ở của từng người dùng (mỗi người một phòng riêng), tầng `/etc` là phòng cấu hình chứa toàn bộ "bản thiết kế" của tòa nhà, tầng `/tmp` là phòng rác tạm thời — ai cũng có thể để đồ vào đây nhưng sẽ bị dọn sạch khi tắt máy.

Khi bạn dùng terminal, bạn như người đang đứng ở một vị trí nào đó trong tòa nhà. Lệnh `pwd` ("print working directory") sẽ cho bạn biết mình đang đứng ở đâu; lệnh `ls` giống như bật đèn nhìn xung quanh để xem có những gì; lệnh `cd` là bước chân đi sang phòng khác.

---

## Giải thích cho người đã biết lập trình (nâng cao)

Linux tuân theo chuẩn **FHS (Filesystem Hierarchy Standard)**. Toàn bộ hệ thống là một cây thư mục duy nhất bắt đầu từ `/`, khác với Windows dùng nhiều drive letter (C:, D:, ...).

**Cơ chế inode:** Mỗi file/directory được đại diện bởi một inode chứa metadata (permissions, timestamps, owner, block pointers). Tên file chỉ là một "directory entry" trỏ tới inode — đây là lý do hard link hoạt động được.

**Virtual filesystems:** `/proc` và `/sys` không phải filesystem thật trên disk — kernel tạo ra chúng trong RAM để expose thông tin hệ thống dưới dạng file. `/proc/cpuinfo` không lưu trên ổ cứng — nó được tạo on-the-fly khi bạn đọc.

**Mount points:** Các partition, USB, network share được "gắn" vào các điểm trong cây thư mục qua `mount`. Lệnh `df -h` cho thấy các mount points đang active.

**`find` hoạt động thế nào:** `find` duyệt cây thư mục theo kiểu DFS (depth-first search), gọi `stat()` syscall trên từng entry để kiểm tra điều kiện. Đây là lý do nó chậm hơn `locate` (dùng database được index trước).

---

## Linux Filesystem Hierarchy

| Thư mục | Mục đích | Ghi chú |
|---------|----------|---------|
| `/` | Root — gốc của mọi thứ | Mount point đầu tiên |
| `/home` | Thư mục cá nhân của người dùng | `/home/username` |
| `/root` | Home của user root | Khác `/home/root` |
| `/etc` | Config files toàn hệ thống | Text files, không có binary |
| `/var` | Variable data: logs, databases, spool | `/var/log`, `/var/lib` |
| `/tmp` | Temporary files | Bị xóa khi reboot (thường) |
| `/usr` | User programs & data | `/usr/bin`, `/usr/lib` |
| `/bin` | Essential binaries | `ls`, `cp`, `bash` |
| `/sbin` | System binaries (cần root) | `fdisk`, `ifconfig` |
| `/dev` | Device files | `/dev/sda`, `/dev/null` |
| `/proc` | Kernel & process info (virtual) | `/proc/cpuinfo`, `/proc/meminfo` |
| `/sys` | Hardware info (virtual) | Thay thế `/proc` cho hardware |
| `/mnt` | Temporary mount point | Thủ công |
| `/media` | Auto-mount cho removable media | USB, CD-ROM |
| `/opt` | Optional/third-party software | |
| `/lib` | Shared libraries cho `/bin`, `/sbin` | `.so` files |
| `/boot` | Boot loader, kernel images | `vmlinuz`, `initrd` |

---

## Các lệnh / Cú pháp chính

| Lệnh | Mô tả | Ví dụ |
|------|-------|-------|
| `pwd` | In thư mục hiện tại | `pwd` |
| `ls` | Liệt kê nội dung thư mục | `ls -la /etc` |
| `ls -l` | Hiển thị dạng danh sách chi tiết | `ls -lh /var/log` |
| `ls -a` | Hiện cả file ẩn (bắt đầu bằng `.`) | `ls -la ~` |
| `ls -lh` | Size dạng human-readable (KB, MB) | `ls -lh /var/log` |
| `ls -lt` | Sort theo thời gian sửa đổi (mới nhất trước) | `ls -lt /tmp` |
| `ls -lR` | Recursive — liệt kê tất cả subdirectories | `ls -lR /etc` |
| `cd` | Chuyển thư mục | `cd /var/log` |
| `cd ~` | Về home directory | `cd ~` |
| `cd -` | Về thư mục vừa rời | `cd -` |
| `cd ..` | Lên một cấp cha | `cd ../..` |
| `tree` | Hiển thị cây thư mục | `tree -L 2 /etc` |
| `tree -a` | Bao gồm file ẩn | `tree -a ~/.config` |
| `find` | Tìm file theo điều kiện | `find /home -name "*.py"` |
| `locate` | Tìm nhanh qua database | `locate nginx.conf` |
| `which` | Tìm đường dẫn của lệnh | `which python3` |
| `whereis` | Tìm binary, source, manual | `whereis bash` |
| `pushd` | Push thư mục vào stack, cd tới đó | `pushd /etc` |
| `popd` | Pop stack, quay lại thư mục trước | `popd` |
| `dirs` | Xem directory stack | `dirs -v` |

---

## Ví dụ thực tế

```bash
# ===== pwd & ls cơ bản =====
pwd                          # /home/user
ls -la                       # hiện tất cả, kể cả file ẩn
ls -lh /var/log              # xem log với size dễ đọc
ls -lt /tmp | head -10       # 10 file mới nhất trong /tmp

# ===== Absolute vs Relative paths =====
cd /etc/nginx/               # absolute: bắt đầu từ /
cd ../nginx/                 # relative: từ vị trí hiện tại
cd ~/projects/               # ~ = $HOME = /home/username

# ===== find — công cụ tìm kiếm mạnh nhất =====
# Tìm file .py trong /home, không đi vào thư mục hidden
find /home -name "*.py" -not -path "*/\.*"

# Tìm file lớn hơn 100MB
find / -type f -size +100M 2>/dev/null

# Tìm file .py được sửa trong 7 ngày qua
find /home -name "*.py" -mtime -7

# Tìm và xóa file .log cũ hơn 30 ngày
find /var/log -name "*.log" -mtime +30 -exec rm {} \;

# Tìm file với maxdepth 2 (không đi quá sâu)
find /etc -maxdepth 2 -name "*.conf" -type f

# Tìm thư mục trống
find /tmp -type d -empty

# Tìm file có permission 777 (nguy hiểm)
find / -type f -perm 0777 2>/dev/null

# ===== locate — nhanh nhưng cần update database =====
locate "*.conf" | grep nginx  # tìm qua database (updatedb)
sudo updatedb                 # cập nhật database của locate

# ===== Wildcards =====
ls /etc/*.conf                # tất cả file .conf trong /etc
ls /var/log/sys*              # file bắt đầu bằng sys
ls /home/user/file[0-9].txt   # file0.txt đến file9.txt
ls /home/{alice,bob}/         # home của alice và bob

# ===== pushd/popd — directory stack =====
pushd /etc/nginx              # cd vào /etc/nginx, lưu vị trí cũ
pushd /var/log                # cd vào /var/log, stack: /etc/nginx ~
dirs -v                       # xem stack:  0  /var/log  1  /etc/nginx  2  ~
popd                          # về /etc/nginx
popd                          # về ~
```

---

## Kết hợp lệnh nâng cao (Pipes & Patterns)

```bash
# Tìm 10 thư mục lớn nhất trong hệ thống
du -h / 2>/dev/null | sort -rh | head -10

# Đếm số file theo extension trong thư mục hiện tại
find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -rn

# Tìm tất cả file config và xem nội dung qua less
find /etc -name "*.conf" -type f 2>/dev/null | xargs ls -lh

# Kiểm tra cây thư mục /proc của một process cụ thể
ls -la /proc/$$/fd           # file descriptors của shell hiện tại

# Tạo symlink cho tất cả script trong thư mục
find ~/scripts -name "*.sh" -exec ln -sf {} ~/bin/ \;

# So sánh hai thư mục
diff <(ls /dir1 | sort) <(ls /dir2 | sort)

# Tìm file duplicate theo size
find . -type f -printf '%s\n' | sort -n | uniq -d

# Monitor thư mục thay đổi realtime (cần inotify-tools)
inotifywait -m -r /var/www --event modify,create,delete
```

---

## Lỗi thường gặp (Common Pitfalls)

**1. Nhầm `/` (root directory) với `/root` (home của root user)**
```bash
cd /       # đi về gốc hệ thống
cd /root   # đi vào home của root (cần quyền)
cd ~root   # cũng đi vào /root
```

**2. `find` báo "Permission denied" đầy màn hình**
```bash
# Redirect stderr về /dev/null
find / -name "*.conf" 2>/dev/null
```

**3. Dùng `ls -lR /` — đệ quy từ root, liệt kê hàng triệu file**
```bash
# Giới hạn depth
ls -lR /etc 2>/dev/null | head -50
# Hoặc dùng find với maxdepth
find /etc -maxdepth 1 -ls
```

**4. `locate` trả về kết quả cũ vì database chưa update**
```bash
sudo updatedb && locate myfile
```

**5. Dùng `cd` mà không có arguments — về $HOME, không phải /root**
```bash
cd        # về $HOME
cd ~      # cũng về $HOME
cd /      # về root directory
```

**6. Nhầm absolute path vs relative path**
```bash
# Nếu pwd = /home/user
cd etc         # LỖI: /home/user/etc không tồn tại
cd /etc        # ĐÚNG: absolute path
cd ../../../   # relative: lên 3 cấp
```

**7. `find -exec` không escape dấu `;`**
```bash
find . -exec rm {} ;    # LỖI: shell interpret ;
find . -exec rm {} \;   # ĐÚNG: escape ;
find . -exec rm {} +    # NHANH HƠN: gom nhiều file vào một lệnh
```

---

## Câu hỏi phỏng vấn hay gặp

**Q1: Sự khác biệt giữa `/bin` và `/usr/bin`?**
> `/bin` chứa essential binaries cần thiết cho single-user mode và recovery (ls, cp, bash). `/usr/bin` chứa non-essential user programs. Trên nhiều distro hiện đại, `/bin` là symlink tới `/usr/bin`.

**Q2: `/proc` là gì, nó được lưu ở đâu trên disk?**
> `/proc` là virtual filesystem (procfs) do kernel tạo ra trong RAM. Không có byte nào trên disk. Nó cung cấp interface để đọc/ghi kernel data structures. Đọc `/proc/cpuinfo` thực ra gọi kernel để format thông tin CPU thành text.

**Q3: `find` vs `locate` — khi nào dùng cái nào?**
> `locate` nhanh hơn nhiều vì query SQLite database được index bởi `updatedb` (thường chạy qua cron). Dùng `locate` khi cần tìm nhanh file static. Dùng `find` khi cần tìm file mới tạo, filter theo size/permission/mtime, hoặc thực thi action trên kết quả.

**Q4: Tại sao `cd` là built-in shell chứ không phải external command?**
> Nếu `cd` là external command, nó sẽ chạy trong subprocess riêng. Subprocess thay đổi directory của chính nó, shell cha vẫn ở chỗ cũ. Built-in command chạy trong context của shell hiện tại nên có thể thay đổi `$PWD` của shell đó.

**Q5: Giải thích `find . -mtime -7 -mtime +1`?**
> Tìm file được sửa trong 1–7 ngày qua. `-mtime -7` = sửa chưa đầy 7 ngày; `-mtime +1` = sửa hơn 1 ngày. Kết hợp: từ 1 đến 7 ngày. Lưu ý: `-mtime 0` = trong 24h qua, `-mtime 1` = từ 24–48h trước.

**Q6: `pushd` và `popd` hữu ích trong scripting như thế nào?**
> Giữ lại directory stack cho phép script di chuyển qua nhiều thư mục và quay lại đúng chỗ mà không cần lưu biến. Pattern hay dùng: `pushd /build && make && popd` — build xong tự quay về directory gốc, dù make thành công hay fail.

**Q7: Làm sao tìm tất cả file lớn hơn 1GB trong hệ thống mà không có quyền root?**
```bash
find / -type f -size +1G 2>/dev/null
# 2>/dev/null redirect các lỗi "Permission denied" đi
# Chỉ hiện các file mà user hiện tại có quyền đọc
```
