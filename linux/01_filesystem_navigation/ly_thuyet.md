# Linux Filesystem Navigation — Điều hướng & Hệ thống thư mục

---

## Giải thích cho người mới

Linux giống như một tòa nhà với **một cổng vào duy nhất** gọi là `/` (root). Từ đó bạn đi vào các "phòng" (thư mục). Khi dùng terminal, bạn luôn đang đứng ở một vị trí nào đó trong tòa nhà:
- `pwd` — "Tôi đang đứng ở đâu?"
- `ls` — "Trong phòng này có gì?"
- `cd` — "Đi sang phòng khác"

---

## Hệ thống thư mục Linux (FHS)

| Thư mục | Chứa gì | Ví dụ |
|---------|---------|-------|
| `/` | Root — gốc của mọi thứ | — |
| `/home` | Thư mục cá nhân của từng user | `/home/alice`, `~` |
| `/root` | Home của user root | Khác `/home` |
| `/etc` | Config files toàn hệ thống | `/etc/nginx/nginx.conf` |
| `/var` | Logs, database, runtime data | `/var/log/syslog` |
| `/tmp` | File tạm, xóa khi reboot | — |
| `/usr` | Programs của user | `/usr/bin/python3` |
| `/bin` | Essential binaries | `ls`, `cp`, `bash` |
| `/sbin` | System binaries (cần root) | `fdisk`, `iptables` |
| `/dev` | Device files | `/dev/sda`, `/dev/null` |
| `/proc` | Virtual FS — thông tin kernel/process | `/proc/cpuinfo` |
| `/sys` | Virtual FS — thông tin hardware | `/sys/class/net` |
| `/opt` | Software cài thêm | `/opt/vscode` |
| `/mnt`, `/media` | Mount point cho USB, disk | `/media/usb` |

**Absolute path** vs **Relative path:**
```
Absolute: /home/alice/documents/file.txt   (bắt đầu từ /)
Relative: documents/file.txt              (tính từ vị trí hiện tại)
Relative: ../file.txt                     (một cấp lên rồi vào file)
```

**Special paths:**
```
~       = /home/current_user  (home của user hiện tại)
.       = thư mục hiện tại
..      = thư mục cha
-       = thư mục trước đó (dùng với cd -)
```

---

## Giải thích nâng cao

**inode**: Mỗi file có 1 inode chứa metadata (permissions, owner, timestamps, block pointers). Tên file chỉ là pointer đến inode → hard link hoạt động vì 2 tên trỏ cùng 1 inode.

**Virtual filesystem**: `/proc` và `/sys` không tồn tại trên disk — kernel tạo on-the-fly trong RAM. `cat /proc/cpuinfo` không đọc file thật mà kernel sinh ra dữ liệu khi bạn đọc.

**`find` vs `locate`**: `find` duyệt DFS thực tế → chậm nhưng kết quả real-time. `locate` dùng database được index (`updatedb`) → nhanh nhưng có thể stale.

---

## BẢNG LỆNH THỰC HÀNH

### pwd — Print Working Directory
```bash
pwd                    # /home/alice
pwd -L                 # follow symlinks (default)
pwd -P                 # physical path, resolve symlinks
```

### ls — List Directory
```bash
ls                     # liệt kê files (không ẩn)
ls -l                  # long format (permissions, size, date)
ls -a                  # all — bao gồm file ẩn (.bashrc)
ls -la                 # long + all
ls -lh                 # human-readable size (KB, MB, GB)
ls -lt                 # sort by time (mới nhất đầu)
ls -lS                 # sort by size (lớn nhất đầu)
ls -R                  # recursive — liệt kê sub-directories
ls -d */               # chỉ liệt kê directories
ls --color=auto        # highlight loại file bằng màu
ls /etc/*.conf         # glob: tất cả .conf trong /etc
```

**Output của `ls -l`:**
```
-rw-r--r-- 1 alice alice 4096 Jan 15 10:30 file.txt
│          │ │     │     │    │             └── tên file
│          │ │     │     │    └── ngày sửa cuối
│          │ │     │     └── kích thước (bytes)
│          │ │     └── group
│          │ └── owner
│          └── số hard links
└── type + permissions (- = file, d = dir, l = symlink)
```

### cd — Change Directory
```bash
cd /etc                # đến absolute path
cd documents           # đến relative path
cd ~                   # về home
cd                     # về home (không có argument)
cd ..                  # lên một cấp
cd ../..               # lên hai cấp
cd -                   # về thư mục trước đó
cd /var/log/nginx      # đến nested directory
```

### find — Tìm kiếm file
```bash
# Cú pháp: find [đường_dẫn] [điều_kiện] [hành_động]

find . -name "*.log"              # tìm file .log trong thư mục hiện tại
find /home -name "*.txt"          # tìm trong /home
find . -name "config*"            # tìm file bắt đầu bằng "config"
find . -iname "*.TXT"             # case-insensitive

find . -type f                    # chỉ tìm file
find . -type d                    # chỉ tìm directory
find . -type l                    # chỉ tìm symlink

find . -size +100M                # file lớn hơn 100MB
find . -size -1k                  # file nhỏ hơn 1KB
find . -size +1M -size -1G        # từ 1MB đến 1GB

find . -mtime -7                  # sửa trong 7 ngày qua
find . -mtime +30                 # sửa hơn 30 ngày trước
find . -newer file.txt            # mới hơn file.txt

find . -perm 644                  # đúng permission 644
find . -perm /u+x                 # có execute bit của owner

find . -user alice                # thuộc về user alice
find . -group developers          # thuộc group developers

find . -name "*.tmp" -delete      # tìm và xóa
find . -name "*.log" -exec ls -lh {} \;   # tìm và chạy lệnh
find . -name "*.conf" -exec cp {} /backup/ \;

find . -maxdepth 2                # chỉ tìm đến độ sâu 2
find . -mindepth 1                # bỏ qua thư mục hiện tại

find / -empty                     # file/dir rỗng
find . \( -name "*.jpg" -o -name "*.png" \)  # OR condition
find . -name "*.log" -not -name "error*"     # NOT condition
```

### locate — Tìm nhanh bằng database
```bash
locate nginx.conf              # tìm tất cả file có tên nginx.conf
locate -i readme               # case-insensitive
locate "*.conf"                # pattern matching
locate -n 10 "*.log"           # giới hạn 10 kết quả
sudo updatedb                  # cập nhật database (cần chạy thường xuyên)
```

### tree — Hiển thị cây thư mục
```bash
tree                           # cây toàn bộ từ đây
tree -L 2                      # chỉ 2 cấp
tree -d                        # chỉ directories, không file
tree -a                        # bao gồm file ẩn
tree -h                        # human-readable size
tree --du                      # disk usage
tree /etc -L 1                 # cây /etc, 1 cấp
```

### du — Disk Usage
```bash
du -h file.txt                 # kích thước file
du -sh /var/log                # tổng kích thước thư mục
du -sh *                       # kích thước từng item hiện tại
du -sh * | sort -rh            # sort by size (lớn nhất đầu)
du -h --max-depth=1 /var       # chỉ 1 cấp sâu
du -ah /home/alice             # tất cả files, human-readable
```

### df — Disk Free (không gian đĩa)
```bash
df -h                          # all mounted filesystems, human-readable
df -h /                        # chỉ root filesystem
df -T                          # hiện type (ext4, tmpfs, ...)
df -i                          # inode usage thay vì blocks
df -h --total                  # cộng tổng cùng
```

### which, where, type — Tìm executable
```bash
which python3                  # /usr/bin/python3
which -a python3               # tất cả locations (PATH)
whereis nginx                  # binary + source + man pages
type ls                        # ls is aliased to 'ls --color=auto'
type -a ls                     # tất cả definitions
command -v git                 # kiểm tra command tồn tại (script-friendly)
```

### Shortcuts điều hướng
```bash
pushd /etc/nginx               # push current dir, cd đến /etc/nginx
popd                           # quay lại dir trước (pop stack)
dirs -v                        # xem directory stack

# History navigation
cd -                           # thư mục trước
!!                             # lệnh trước
!$                             # argument cuối của lệnh trước
Alt+.                          # argument cuối của lệnh trước (interactive)
```

---

## Khi nào dùng gì

| Tình huống | Lệnh |
|-----------|------|
| Biết tên file, tìm nhanh | `locate filename` |
| Tìm chính xác, real-time | `find / -name "filename"` |
| Tìm file lớn chiếm disk | `find / -size +100M` hoặc `du -sh * \| sort -rh` |
| Xem disk usage | `df -h` (tổng quan), `du -sh dir/` (cụ thể) |
| Xem cấu trúc project | `tree -L 3` |
| Kiểm tra command ở đâu | `which command` |

---

## Lỗi thường gặp

```bash
# Permission denied khi cd vào thư mục
ls -la /root             # xem permissions
sudo ls /root            # dùng sudo

# Dùng ~ trong sudo
sudo ls ~/               # ~ expand thành /root, không phải /home/user
sudo -u alice ls ~alice/ # đúng cách

# find quá chậm trên /
find / -name "*.log" 2>/dev/null    # redirect errors

# ls bị lỗi argument too long khi có quá nhiều file
ls *.txt                 # shell expand: lỗi nếu > ARG_MAX
find . -name "*.txt"     # cách đúng
```

---

## Câu hỏi phỏng vấn hay gặp

- `/bin` vs `/usr/bin` vs `/usr/local/bin` — khác nhau thế nào?
- Hard link vs Symbolic link — khi nào dùng cái nào?
- `/proc/cpuinfo` là file thật không? Dữ liệu lấy từ đâu?
- `find . -name "*.log" -delete` vs `rm $(find . -name "*.log")` — cái nào an toàn hơn?
- Tại sao `locate` nhanh hơn `find`? Khi nào `find` chính xác hơn?
