# Tổng quan Linux Syntax — Lệnh và Scripting

---

## Roadmap học Linux

### Bước 1 — Navigation & File System
- Cấu trúc thư mục Linux: `/`, `/home`, `/etc`, `/var`, `/tmp`, `/usr`
- Di chuyển: `cd`, `ls`, `pwd`, `find`, `locate`
- File operations: `cp`, `mv`, `rm`, `mkdir`, `touch`, `ln`

### Bước 2 — File Content & Text Processing
- Xem file: `cat`, `less`, `more`, `head`, `tail`
- Tìm kiếm: `grep`, `awk`, `sed`
- Sắp xếp & lọc: `sort`, `uniq`, `cut`, `wc`
- Pipeline: `|`, `>`, `>>`, `<`, `2>`, `&>`

### Bước 3 — Process & System
- Process management: `ps`, `top`, `htop`, `kill`, `pkill`, `jobs`, `bg`, `fg`
- Disk & memory: `df`, `du`, `free`, `lsblk`
- Network: `ping`, `netstat`, `ss`, `curl`, `wget`, `ssh`, `scp`
- Permissions: `chmod`, `chown`, `chgrp`, `umask`

### Bước 4 — Bash Scripting
- Variables, `if/else`, `for`, `while`, loops
- Functions, arguments (`$1`, `$@`, `$#`)
- Exit codes, error handling
- Cron jobs, scheduling

### Bước 5 — Admin & DevOps
- Package management: `apt`, `yum`, `dnf`, `snap`
- Service management: `systemctl`, `service`
- Log management: `journalctl`, `/var/log/`
- Docker basics: `docker run`, `build`, `ps`, `exec`

---

## Cấu trúc thư mục Linux

| Thư mục | Chứa gì |
|---------|---------|
| `/` | Root — gốc của mọi thứ |
| `/home` | Home directory của users |
| `/root` | Home của root user |
| `/etc` | Config files hệ thống |
| `/var` | Variable data (logs, databases) |
| `/tmp` | Temporary files (xóa khi reboot) |
| `/usr` | User programs, libraries |
| `/bin` | Essential commands (ls, cp, mv) |
| `/sbin` | System commands (fdisk, ifconfig) |
| `/dev` | Device files (disk, terminal) |
| `/proc` | Virtual filesystem — process info |
| `/mnt` | Mount points |

---

## Permissions — Đọc hiểu `rwxrwxrwx`

```
-rwxr-xr--  1  alice  staff  4096  May 28  file.sh
 ^^^         |         |
 |||         owner     group
 ||└── Others (r--)  = 4 = read only
 |└─── Group  (r-x)  = 5 = read + execute
 └──── Owner  (rwx)  = 7 = read + write + execute

chmod 755 file.sh  →  rwxr-xr-x
chmod 644 file.txt →  rw-r--r--
chmod +x file.sh   →  thêm execute cho tất cả
```

| Octal | Binary | rwx |
|-------|--------|-----|
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |
| 4 | 100 | r-- |
| 0 | 000 | --- |

---

## Lệnh thường dùng nhất — Cheat Sheet

### Navigation
```bash
pwd                    # in thư mục hiện tại
ls -la                 # list all files kèm permissions
cd ~                   # về home
cd -                   # về thư mục trước
find . -name "*.py"    # tìm file Python trong thư mục hiện tại
find / -size +100M     # tìm file > 100MB
```

### File Operations
```bash
cp -r src/ dst/        # copy thư mục
mv old.txt new.txt     # rename/move
rm -rf folder/         # xóa folder và nội dung
mkdir -p a/b/c         # tạo nested directory
ln -s /path/to/file symlink  # tạo symbolic link
```

### Text Processing
```bash
grep -r "pattern" .           # tìm pattern đệ quy
grep -n "TODO" *.py           # tìm với số dòng
grep -v "error" log.txt       # KHÔNG chứa "error"
sed 's/old/new/g' file.txt    # replace all
sed -i 's/old/new/g' file.txt # replace trực tiếp trong file
awk '{print $1, $3}' data.txt # in cột 1 và 3
awk -F',' '{sum+=$2} END{print sum}' data.csv  # sum cột 2
sort -k2 -n data.txt          # sort theo cột 2, numeric
sort -u list.txt               # sort và loại duplicate
wc -l file.txt                # đếm số dòng
```

### Process Management
```bash
ps aux | grep python          # tìm process python
kill -9 PID                   # force kill
pkill -f "python script.py"   # kill theo tên
nohup ./script.sh &           # chạy background, thoát terminal được
jobs                          # list background jobs
bg %1                         # đưa job 1 lên background
fg %1                         # đưa job 1 lên foreground
```

### Network
```bash
ping -c 4 google.com          # ping 4 lần
curl -X POST -H "Content-Type: application/json" \
     -d '{"key":"value"}' http://api.example.com
wget -O output.zip http://url/file.zip
ssh user@192.168.1.10
scp file.txt user@server:/path/
ss -tulpn                     # xem ports đang listen
```

### Disk & Memory
```bash
df -h                         # disk space (human readable)
du -sh folder/                # size của folder
du -sh * | sort -rh | head    # top 10 folder lớn nhất
free -h                       # RAM usage
```

---

## Bash Scripting — Cú pháp cốt lõi

### Variables & Input
```bash
#!/bin/bash
name="Alice"
echo "Hello, $name"

read -p "Enter name: " user_name
echo "You entered: $user_name"

# Arguments
echo "Script name: $0"
echo "First arg: $1"
echo "All args: $@"
echo "Arg count: $#"
```

### If/Else
```bash
# String comparison
if [ "$name" == "Alice" ]; then
    echo "Hello Alice"
elif [ "$name" == "Bob" ]; then
    echo "Hello Bob"
else
    echo "Who are you?"
fi

# Number comparison
num=10
if [ $num -gt 5 ]; then    # -gt: >, -lt: <, -eq: ==, -ne: !=
    echo "Greater than 5"
fi

# File check
if [ -f "file.txt" ]; then    # -f: file exists, -d: directory
    echo "File exists"
fi
```

### Loops
```bash
# For loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# For loop với range
for i in {1..10}; do
    echo $i
done

# For loop qua files
for file in *.txt; do
    echo "Processing: $file"
done

# While loop
count=0
while [ $count -lt 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

### Functions
```bash
greet() {
    local name=$1    # local variable
    echo "Hello, $name!"
    return 0         # exit code
}

greet "Alice"
echo "Exit code: $?"   # $? = exit code của lệnh trước
```

---

## Mã giả — Hỏi output là gì?

### Bài 1 — Pipeline cơ bản

```bash
echo -e "banana\napple\ncherry\napple\nbanana\napple" | sort | uniq -c | sort -rn
```

> **Output là gì?**
> ```
>       3 apple
>       2 banana
>       1 cherry
> ```
> **Giải thích:** `sort` → sắp xếp. `uniq -c` → đếm số lần xuất hiện. `sort -rn` → sort ngược theo số (reverse numeric). apple xuất hiện 3 lần nhiều nhất.

---

### Bài 2 — Script với vòng lặp

```bash
#!/bin/bash
total=0
for num in 1 2 3 4 5; do
    total=$((total + num))
    echo "After adding $num: total=$total"
done
echo "Final: $total"
```

> **Output là gì?**
> ```
> After adding 1: total=1
> After adding 2: total=3
> After adding 3: total=6
> After adding 4: total=10
> After adding 5: total=15
> Final: 15
> ```

---

### Bài 3 — Exit code và &&, ||

```bash
ls /tmp/nonexistent 2>/dev/null && echo "EXISTS" || echo "NOT FOUND"
ls /tmp 2>/dev/null && echo "EXISTS" || echo "NOT FOUND"
```

> **Output là gì?**
> ```
> NOT FOUND
> EXISTS
> ```
> **Giải thích:** `&&` chạy lệnh tiếp nếu lệnh trước SUCCESS (exit 0). `||` chạy nếu FAIL (exit ≠ 0). `2>/dev/null` redirect stderr vào /dev/null (im lặng lỗi).

---

### Bài 4 — AWK

```bash
echo "Alice 25 Engineering
Bob 30 Marketing
Charlie 25 Engineering
Diana 28 Engineering" | awk '$3=="Engineering" {sum+=$2; count++} END{print "Avg age:", sum/count}'
```

> **Output là gì?**
> ```
> Avg age: 26
> ```
> **Giải thích:** AWK lọc rows có cột 3 = "Engineering" (Alice 25, Charlie 25, Diana 28). Sum = 78, count = 3, avg = 26.

---

### Bài 5 — Sed và Pattern

```bash
echo "Hello World 2024" | sed 's/[0-9]\+/YEAR/g'
echo "foo bar foo baz" | sed 's/foo/FOO/'
echo "foo bar foo baz" | sed 's/foo/FOO/g'
```

> **Output là gì?**
> ```
> Hello World YEAR
> FOO bar foo baz
> FOO bar FOO baz
> ```
> **Giải thích:** `[0-9]\+` match một hoặc nhiều chữ số. Không có `g` flag → chỉ replace lần đầu. Có `g` → replace tất cả.

---

### Bài 6 — Permissions

```bash
ls -la cho thấy:
-rwxr-xr-- 1 alice dev 1024 file.sh

Câu hỏi:
a) User "alice" có thể làm gì?
b) User "bob" thuộc group "dev" có thể làm gì?
c) User "carol" không thuộc group "dev" có thể làm gì?
d) chmod 644 file.sh → permissions mới?
```

> **Đáp án:**
> a) **alice**: rwx — đọc, ghi, thực thi
> b) **bob (dev)**: r-x — đọc, thực thi (không ghi)
> c) **carol (others)**: r-- — chỉ đọc
> d) **644** = 110·100·100 = **rw-r--r--** (owner rw, group r, others r)

---

## Cron Job Syntax

```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7, 0=Sunday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

Ví dụ:
0 2 * * *     → Mỗi ngày lúc 2:00 AM
*/15 * * * *  → Mỗi 15 phút
0 9 * * 1-5   → Thứ 2-6 lúc 9:00 AM
0 0 1 * *     → Ngày đầu mỗi tháng lúc 0:00
```

---

## Câu hỏi tự test nhanh

1. `chmod 755` tương đương permission gì? → rwxr-xr-x
2. Lệnh nào tìm file lớn hơn 100MB? → `find / -size +100M`
3. `2>&1` nghĩa là gì? → Redirect stderr (2) vào stdout (1)
4. Sự khác nhau giữa `>` và `>>`? → `>` overwrite, `>>` append
5. Lệnh xem 10 dòng cuối log realtime? → `tail -f -n 10 /var/log/syslog`
6. `ps aux` vs `ps -ef`? → Cả hai list process, `aux` BSD style, `-ef` UNIX style
7. `&&` vs `;` trong shell? → `&&` chỉ chạy nếu lệnh trước thành công; `;` luôn chạy
