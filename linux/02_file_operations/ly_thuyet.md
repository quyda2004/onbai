# Linux File Operations — Thao tác với File

---

## Giải thích cho người mới

File trong Linux giống như đồ vật trong phòng. Bạn có thể:
- **Xem nội dung**: đọc tờ giấy (`cat`, `less`, `head`)
- **Sao chép**: photo-copy (`cp`)
- **Di chuyển / Đổi tên**: chuyển sang phòng khác (`mv`)
- **Xóa**: vứt vào thùng rác — **không có thùng rác** trong terminal! (`rm`)
- **Tạo mới**: tạo tờ giấy trắng (`touch`, `mkdir`)

⚠️ **Cảnh báo**: `rm` trong Linux **xóa vĩnh viễn**, không có Recycle Bin!

---

## Giải thích nâng cao

**File types trong Linux** (xem bằng `ls -la` — ký tự đầu tiên):
```
-   regular file
d   directory
l   symbolic link
b   block device (/dev/sda)
c   character device (/dev/tty)
p   named pipe (FIFO)
s   socket
```

**Copy-on-Write**: filesystem như Btrfs/ZFS dùng CoW — khi copy file lớn không tốn disk ngay lập tức, chỉ tạo reference. Khi có sửa đổi mới snapshot data.

**Hard link vs Symbolic link**:
- Hard link: 2 tên trỏ cùng inode → xóa 1 tên, data vẫn còn (reference count > 0). Không cross filesystem.
- Soft link (symlink): như shortcut Windows — trỏ đến path. Xóa file gốc → symlink bị "dangling" (broken).

---

## BẢNG LỆNH THỰC HÀNH

### cat — Xem nội dung file
```bash
cat file.txt                   # in toàn bộ file
cat -n file.txt                # in kèm số dòng
cat -A file.txt                # hiện ký tự ẩn (tabs ^I, newlines $)
cat file1.txt file2.txt        # nối và in 2 file
cat file1.txt file2.txt > combined.txt   # nối vào file mới

# Tạo file từ stdin
cat > newfile.txt              # gõ nội dung, Ctrl+D để kết thúc
cat >> existing.txt            # append vào cuối file

# Tránh dùng cat khi không cần
grep "error" < file.txt        # dùng stdin redirect thay vì: cat file | grep
```

### less / more — Xem file dài
```bash
less file.txt                  # xem từng trang (q để thoát)
less +G file.txt               # mở ở cuối file (xem log)
less +F file.txt               # follow mode (như tail -f)
less -N file.txt               # hiện số dòng

# Trong less:
# /pattern    tìm kiếm xuôi
# ?pattern    tìm kiếm ngược
# n           next match
# N           prev match
# g           đầu file
# G           cuối file
# q           thoát

more file.txt                  # đơn giản hơn less, chỉ cuộn xuống
```

### head / tail — Xem đầu/cuối file
```bash
head file.txt                  # 10 dòng đầu (mặc định)
head -n 20 file.txt            # 20 dòng đầu
head -n -5 file.txt            # tất cả trừ 5 dòng cuối
head -c 100 file.txt           # 100 bytes đầu
head -c 1M file.txt            # 1MB đầu

tail file.txt                  # 10 dòng cuối
tail -n 20 file.txt            # 20 dòng cuối
tail -n +5 file.txt            # từ dòng 5 đến cuối
tail -f /var/log/syslog        # follow — real-time (xem log)
tail -F /var/log/nginx/access.log  # follow + reopen nếu file rotate

# Xem từ dòng 50 đến 60:
sed -n '50,60p' file.txt       # hoặc:
awk 'NR>=50 && NR<=60' file.txt
```

### cp — Copy
```bash
cp file.txt backup.txt         # copy file
cp file.txt /tmp/              # copy đến thư mục khác
cp file.txt /tmp/newname.txt   # copy và đổi tên

cp -r dir/ newdir/             # copy recursive (thư mục)
cp -r dir/ /backup/            # copy thư mục đến đích khác

cp -p file.txt backup.txt      # preserve permissions, timestamps
cp -a dir/ backup/             # archive mode (= -dpr, giữ mọi thứ)
cp -u file.txt backup.txt      # chỉ copy nếu src mới hơn dst
cp -i file.txt existing.txt    # hỏi trước khi overwrite
cp -v file.txt backup.txt      # verbose — in từng file

# Copy nhiều file
cp file1.txt file2.txt file3.txt /destination/
cp *.txt /destination/
cp -r {dir1,dir2,dir3} /destination/
```

### mv — Move / Rename
```bash
mv old.txt new.txt             # đổi tên (trong cùng filesystem)
mv file.txt /tmp/              # di chuyển
mv file.txt /tmp/newname.txt   # di chuyển và đổi tên

mv -i file.txt dest/           # hỏi trước khi overwrite
mv -u file.txt dest/           # chỉ move nếu src mới hơn dst
mv -v *.log /archive/          # verbose

# Rename hàng loạt (cần rename hoặc loop)
for f in *.txt; do mv "$f" "${f%.txt}.bak"; done
rename 's/\.txt$/.bak/' *.txt  # dùng rename (Perl)
```

### rm — Remove (XÓA VĨNH VIỄN!)
```bash
rm file.txt                    # xóa file
rm file1.txt file2.txt         # xóa nhiều file
rm *.log                       # xóa tất cả .log

rm -r directory/               # xóa thư mục recursive
rm -rf directory/              # xóa không hỏi (-f = force)
rm -i file.txt                 # hỏi trước khi xóa (SAFE)
rm -v file.txt                 # verbose

# ⚠️ NGUY HIỂM - ĐỪNG CHẠY:
# rm -rf /          → xóa toàn bộ hệ thống
# rm -rf ~/*        → xóa toàn bộ home dir
# rm -rf ./         → xóa thư mục hiện tại và mọi thứ trong đó

# An toàn hơn: dùng trash-cli
# trash file.txt     → đưa vào trash
# trash-list         → xem trash
# trash-restore      → restore
```

### mkdir — Make Directory
```bash
mkdir newdir                   # tạo thư mục
mkdir dir1 dir2 dir3           # tạo nhiều thư mục cùng lúc
mkdir -p path/to/nested/dir    # tạo nested (kể cả parent nếu chưa có)
mkdir -p project/{src,test,docs,build}  # tạo nhiều sub-dir cùng lúc
mkdir -m 755 newdir            # tạo với permission cụ thể
mkdir -v newdir                # verbose
```

### rmdir — Remove Directory (chỉ xóa dir rỗng)
```bash
rmdir emptydir                 # chỉ xóa nếu rỗng
rmdir -p path/to/nested/       # xóa nested nếu rỗng
# Xóa thư mục có nội dung: dùng rm -r
```

### touch — Tạo file / Cập nhật timestamp
```bash
touch newfile.txt              # tạo file rỗng (hoặc cập nhật timestamp nếu đã tồn tại)
touch file1.txt file2.txt      # nhiều file
touch -t 202401150930 file.txt # set timestamp cụ thể (YYYYMMDDhhmm)
touch -r reference.txt file.txt # copy timestamp từ file khác
touch -a file.txt              # chỉ cập nhật access time
touch -m file.txt              # chỉ cập nhật modification time
```

### ln — Create Links
```bash
# Hard link
ln original.txt hardlink.txt   # tạo hard link
ls -li original.txt hardlink.txt  # cùng inode number!

# Symbolic link (symlink)
ln -s /path/to/original symlink_name    # tạo symlink
ln -s /usr/bin/python3 /usr/local/bin/python  # symlink python
ln -s $(pwd)/script.sh ~/bin/script     # symlink với absolute path

ln -sf newfile.txt symlink.txt  # force overwrite existing symlink
ls -la symlink.txt             # xem symlink trỏ đến đâu
readlink symlink.txt           # in đường dẫn thực
readlink -f symlink.txt        # resolve all symlinks (canonical path)
```

### file — Xác định loại file
```bash
file image.jpg                 # JPEG image data
file script                    # ELF 64-bit LSB executable
file archive.tar.gz            # gzip compressed data
file unknown                   # ASCII text, with CRLF line terminators
file -i image.jpg              # MIME type: image/jpeg; charset=binary
```

### wc — Word Count
```bash
wc file.txt                    # lines words bytes
wc -l file.txt                 # chỉ đếm dòng (lines)
wc -w file.txt                 # chỉ đếm từ (words)
wc -c file.txt                 # chỉ đếm bytes
wc -m file.txt                 # đếm characters (UTF-8 aware)
wc -l *.log                    # đếm dòng trong nhiều file
find . -name "*.py" | xargs wc -l | tail -1  # tổng dòng code
```

### stat — File Statistics
```bash
stat file.txt                  # thông tin chi tiết (inode, permissions, timestamps)
stat -c "%n %s %y" file.txt    # format: name size modification_time
stat --format="%A %U %G" file.txt   # permissions owner group
```

### Redirection — Chuyển hướng output
```bash
# Output redirection
command > file.txt             # stdout → file (overwrite)
command >> file.txt            # stdout → file (append)
command 2> error.txt           # stderr → file
command 2>> error.txt          # stderr → file (append)
command &> output.txt          # stdout + stderr → file
command > output.txt 2>&1      # stdout + stderr → file (POSIX)
command > /dev/null 2>&1       # bỏ toàn bộ output

# Input redirection
command < input.txt            # đọc input từ file
command << EOF                 # here-document
This is input
EOF

command <<< "string"           # here-string

# Pipe
command1 | command2            # stdout của cmd1 → stdin của cmd2
command1 |& command2           # stdout + stderr → cmd2
tee file.txt                   # đọc stdin, ghi vào file VÀ stdout
command | tee output.txt | wc -l  # vừa save vừa tiếp tục pipeline
```

### Nén / Giải nén
```bash
# tar
tar -czf archive.tar.gz dir/          # tạo .tar.gz
tar -cjf archive.tar.bz2 dir/         # tạo .tar.bz2
tar -cJf archive.tar.xz dir/          # tạo .tar.xz (nhỏ nhất)
tar -xzf archive.tar.gz               # giải nén .tar.gz
tar -xzf archive.tar.gz -C /dest/     # giải nén vào thư mục khác
tar -tzf archive.tar.gz               # xem nội dung không giải nén
tar -xzf archive.tar.gz file.txt      # chỉ giải nén file cụ thể

# gzip
gzip file.txt                  # nén → file.txt.gz (xóa file gốc)
gzip -k file.txt               # giữ file gốc (-k = keep)
gzip -d file.txt.gz            # giải nén (= gunzip)
gzip -9 file.txt               # nén tối đa (chậm hơn)
gunzip file.txt.gz             # giải nén

# zip (cross-platform)
zip archive.zip file1 file2    # tạo zip
zip -r archive.zip dir/        # zip thư mục
unzip archive.zip              # giải nén
unzip archive.zip -d /dest/    # giải nén vào thư mục
unzip -l archive.zip           # xem nội dung

# rsync — sync thư mục (tốt hơn cp cho large data)
rsync -av source/ dest/        # sync, archive mode, verbose
rsync -avz source/ user@server:/dest/   # qua SSH
rsync --delete source/ dest/   # xóa file ở dest nếu không có ở source
rsync -n source/ dest/         # dry-run — xem sẽ làm gì
```

---

## Khi nào dùng gì

| Tình huống | Lệnh |
|-----------|------|
| Xem file nhỏ | `cat file.txt` |
| Xem file lớn | `less file.txt` |
| Xem log real-time | `tail -f /var/log/...` |
| Copy giữ metadata | `cp -a` hoặc `rsync -a` |
| Đổi tên file | `mv` |
| Xóa an toàn (hỏi trước) | `rm -i` |
| Tạo backup nhanh | `cp -a dir/ dir.bak/` |
| Sync thư mục lớn | `rsync -av src/ dst/` |
| Nén để transfer | `tar -czf archive.tar.gz dir/` |

---

## Lỗi thường gặp

```bash
# Xóa nhầm vì globbing
rm -rf *.txt        # ổn nếu có *.txt, nhưng...
rm -rf * .txt       # NGUY HIỂM: rm -rf * xóa hết, rồi xóa .txt
# → Dùng: set -f để disable globbing, hoặc rm -rf -- *.txt

# cp không copy hidden files
cp dir/* dest/      # KHÔNG copy .hidden files
cp -r dir/. dest/   # copy TẤT CẢ kể cả hidden
rsync -a dir/ dest/ # cách tốt nhất

# mv: overwrite không hỏi
mv -i old.txt existing.txt   # thêm -i để hỏi trước

# tar: quên -C khi extract
tar -xzf archive.tar.gz      # extract vào thư mục hiện tại
tar -xzf archive.tar.gz -C /dest/  # extract vào đúng thư mục
```

---

## Câu hỏi phỏng vấn hay gặp

- Hard link vs symlink — sự khác biệt và khi nào dùng?
- Tại sao `rm -rf /` rất nguy hiểm?
- Làm thế nào để xem nội dung file binary?
- Giải thích `2>&1` trong redirect.
- `cp` vs `rsync` — khi nào dùng rsync?
