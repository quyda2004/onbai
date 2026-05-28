# Linux File Operations — Thao tác với Tập tin

---

## Giải thích cho người mới hoàn toàn

Hãy nghĩ file trong Linux giống như tài liệu giấy trong văn phòng. `cp` (copy) giống như dùng máy photocopy — bạn tạo ra bản sao y hệt, bản gốc vẫn còn đó. `mv` (move) giống như nhấc tờ giấy đặt vào ngăn kéo khác — hoặc đổi tên nó. `rm` (remove) là ném vào máy hủy tài liệu — **không có thùng rác**, không khôi phục được dễ dàng.

`ln` (link) là khái niệm thú vị: hãy tưởng tượng một bản tài liệu có hai cái tên trên hai cái kệ khác nhau, nhưng thực ra chỉ là một tờ giấy duy nhất (hard link). Hoặc một tờ giấy ghi "hãy xem tài liệu ở kệ A" (symbolic link — giống shortcut trên Windows).

`tar` giống như máy đóng gói — bạn nhét nhiều file vào một hộp, có thể nén lại để tiết kiệm không gian, rồi gửi đi hay lưu trữ.

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Inode và hard link:** Mỗi file có một inode chứa metadata và data block pointers. Directory entry chỉ là ánh xạ `(tên → inode_number)`. Hard link là tạo thêm một directory entry trỏ tới cùng inode — inode có field `link count`. File thực sự bị xóa khỏi disk khi `link count = 0` và không có process nào đang mở file. Đây là cơ chế `rm` — nó giảm link count, không xóa data ngay lập tức.

**Symbolic link:** Là file đặc biệt chứa đường dẫn (string) tới target. Nếu target bị xóa, symlink trở thành "dangling". Symlink có thể trỏ cross-filesystem, hard link thì không (vì inode là per-filesystem).

**`cp -p` và timestamps:** Khi copy file, mtime/atime mặc định bị reset. `-p` (preserve) giữ nguyên permissions, timestamps, owner — quan trọng khi backup.

**`rsync` algorithm (rsync delta transfer):** rsync tính rolling checksum của từng block, so sánh với source. Chỉ transfer những block khác nhau — cực kỳ hiệu quả cho sync incremental. `-z` compress data trong transit (tốt cho mạng chậm), không cần thiết trên local disk.

**`dd` và block I/O:** `dd` làm việc ở block level, bỏ qua filesystem. Dùng để backup MBR (`dd if=/dev/sda bs=512 count=1`), tạo disk image, test I/O speed (`dd if=/dev/zero of=/tmp/test bs=1M count=100`).

**`stat` syscall:** Lệnh `stat` gọi syscall cùng tên, trả về `struct stat` với 3 loại timestamp: `atime` (access time — đọc file), `mtime` (modify time — sửa nội dung), `ctime` (change time — sửa metadata/inode, không phải content).

---

## Các lệnh / Cú pháp chính

| Lệnh | Mô tả | Ví dụ |
|------|-------|-------|
| `cp src dst` | Copy file | `cp file.txt backup.txt` |
| `cp -r src/ dst/` | Copy thư mục đệ quy | `cp -r /etc/ /backup/etc/` |
| `cp -p src dst` | Giữ nguyên permissions/timestamps | `cp -p important.conf backup.conf` |
| `cp -u src dst` | Chỉ copy nếu src mới hơn dst | `cp -u *.py ~/backup/` |
| `cp --backup src dst` | Backup file cũ trước khi ghi đè | `cp --backup file.txt file.txt` |
| `mv src dst` | Di chuyển hoặc đổi tên | `mv old.txt new.txt` |
| `mv -i src dst` | Hỏi trước khi ghi đè | `mv -i *.txt /backup/` |
| `mv -n src dst` | Không ghi đè nếu dst tồn tại | `mv -n file.txt /dst/` |
| `rm file` | Xóa file | `rm temp.txt` |
| `rm -r dir/` | Xóa thư mục đệ quy | `rm -r old_project/` |
| `rm -f file` | Force xóa, không hỏi | `rm -f *.lock` |
| `rm -rf dir/` | Force xóa đệ quy — CẨN THẬN | `rm -rf /tmp/cache/` |
| `rm -i file` | Hỏi từng file trước khi xóa | `rm -i *.log` |
| `mkdir dir` | Tạo thư mục | `mkdir projects` |
| `mkdir -p a/b/c` | Tạo nested directories | `mkdir -p src/main/java` |
| `touch file` | Tạo file rỗng / update timestamp | `touch newfile.txt` |
| `file foo` | Xác định loại file | `file /bin/bash` |
| `stat file` | Metadata chi tiết | `stat /etc/passwd` |
| `ln src link` | Tạo hard link | `ln data.txt data_link.txt` |
| `ln -s src link` | Tạo symbolic link | `ln -s /usr/bin/python3 python` |
| `dd if=src of=dst` | Copy block-level | `dd if=/dev/sda of=disk.img` |
| `rsync -avz src/ dst/` | Sync thư mục với delta | `rsync -avz ~/projects/ server:~/` |
| `tar -czf archive.tar.gz dir/` | Nén thư mục (gzip) | `tar -czf backup.tar.gz /etc/` |
| `tar -xzf archive.tar.gz` | Giải nén | `tar -xzf backup.tar.gz -C /tmp/` |
| `tar -tzf archive.tar.gz` | Liệt kê nội dung | `tar -tzf backup.tar.gz` |

---

## Ví dụ thực tế

```bash
# ===== cp — Copy =====
cp file.txt backup.txt               # copy file đơn
cp -r /etc/nginx/ /backup/nginx/     # copy thư mục
cp -rp /home/user/ /backup/user/     # copy + giữ permissions, timestamps
cp -u *.log /backup/logs/            # chỉ copy file mới hơn
cp -v file.txt /backup/              # verbose: hiện tiến trình

# ===== mv — Move/Rename =====
mv old_name.txt new_name.txt         # đổi tên
mv *.jpg /photos/2024/               # di chuyển tất cả .jpg
mv -i important.conf /etc/           # hỏi trước khi ghi đè
# Batch rename: thêm prefix
for f in *.txt; do mv "$f" "backup_$f"; done

# ===== rm — Xóa an toàn =====
rm file.txt                          # xóa file đơn
rm -ri /tmp/old_data/                # xóa đệ quy, hỏi từng file
# Thay thế an toàn: trash-cli
trash-put file.txt                   # vào recycle bin (cần cài)
trash-list                           # xem danh sách
trash-restore                        # khôi phục

# NGUY HIỂM — ví dụ kinh điển:
# rm -rf / --no-preserve-root        # XÓA TOÀN BỘ HỆ THỐNG
# rm -rf $UNDEFINED_VAR/tmp/         # nếu var rỗng => rm -rf /tmp/

# ===== mkdir =====
mkdir -p ~/projects/myapp/{src,tests,docs}  # tạo cấu trúc cùng lúc
mkdir -m 700 /tmp/private                   # tạo với permissions cụ thể

# ===== touch =====
touch newfile.txt                    # tạo file rỗng
touch -t 202401011200 file.txt       # set timestamp cụ thể (YYYYMMDDhhmm)
touch -r reference.txt target.txt   # copy timestamp từ file khác

# ===== file & stat =====
file /bin/ls                         # ELF 64-bit LSB executable...
file image.jpg                       # JPEG image data...
file /etc/passwd                     # ASCII text
stat /etc/passwd
# File: /etc/passwd
# Size: 2847      Blocks: 8    IO Block: 4096  regular file
# Inode: 131074   Links: 1
# Access: 2024-01-15 10:30:00  (atime)
# Modify: 2024-01-10 08:00:00  (mtime — nội dung)
# Change: 2024-01-10 08:00:00  (ctime — metadata/inode)

# ===== ln — Hard link vs Symbolic link =====
# Hard link: cùng inode, cùng data
ln /home/user/data.txt /backup/data.txt
stat data.txt    # Links: 2

# Symbolic link: pointer đến path
ln -s /home/user/projects ~/projects     # symlink trong ~
ln -s /usr/bin/python3 /usr/local/bin/python  # alias python
ls -la ~/projects                        # projects -> /home/user/projects

# Kiểm tra symlink
readlink ~/projects                      # /home/user/projects
readlink -f ~/projects/subdir            # resolve toàn bộ đường dẫn

# ===== dd =====
# Backup MBR (512 bytes đầu của disk)
sudo dd if=/dev/sda of=~/mbr_backup.img bs=512 count=1

# Tạo disk image
sudo dd if=/dev/sdb of=~/usb_backup.img bs=4M status=progress

# Tạo file test 1GB toàn zero (test filesystem)
dd if=/dev/zero of=/tmp/testfile bs=1M count=1024 status=progress

# Test tốc độ ghi disk
dd if=/dev/zero of=/tmp/speedtest bs=1M count=512 oflag=direct status=progress

# ===== rsync =====
# Sync local
rsync -av /source/dir/ /dest/dir/          # trailing / quan trọng!

# Sync tới remote server
rsync -avz ~/projects/ user@server:~/projects/

# Sync và xóa file không còn ở source
rsync -av --delete ~/backup/ /external/backup/

# Loại trừ file/thư mục
rsync -av --exclude="*.log" --exclude=".git/" src/ dst/

# Dry run — xem trước sẽ làm gì
rsync -avn --delete ~/src/ ~/dst/

# Giới hạn bandwidth (KB/s)
rsync -av --bwlimit=1000 /large/dir/ server:~/

# ===== tar =====
# Nén với gzip (.tar.gz hoặc .tgz)
tar -czf archive.tar.gz /path/to/dir/
tar -czf archive.tar.gz file1.txt file2.txt

# Nén với bzip2 (.tar.bz2) — nén tốt hơn, chậm hơn
tar -cjf archive.tar.bz2 /path/to/dir/

# Nén với xz (.tar.xz) — nén tốt nhất, chậm nhất
tar -cJf archive.tar.xz /path/to/dir/

# Giải nén vào thư mục cụ thể
tar -xzf archive.tar.gz -C /tmp/extract/

# Xem nội dung không giải nén
tar -tzf archive.tar.gz

# Thêm file vào archive đã có
tar -rzf archive.tar.gz newfile.txt   # chỉ dùng với gzip

# Giải nén file cụ thể từ archive
tar -xzf archive.tar.gz path/to/specific/file.txt
```

---

## Kết hợp lệnh nâng cao (Pipes & Patterns)

```bash
# Backup /etc với timestamp trong tên file
tar -czf /backup/etc_$(date +%Y%m%d_%H%M%S).tar.gz /etc/ 2>/dev/null

# Xóa file log cũ hơn 30 ngày, giữ lại ít nhất 5 file mới nhất
ls -t /var/log/*.log | tail -n +6 | xargs -I {} find {} -mtime +30 -delete

# Copy cấu trúc thư mục không có file
find /source -type d | sed 's/\/source/\/dest/' | xargs mkdir -p

# So sánh hai thư mục, tìm file khác nhau
diff <(find /dir1 -type f -printf '%P\n' | sort) \
     <(find /dir2 -type f -printf '%P\n' | sort)

# Tìm hard link của một file
find / -samefile /path/to/file 2>/dev/null

# Xóa symlink bị broken (dangling)
find /usr/local/bin -type l ! -exec test -e {} \; -delete

# Sync + backup với versioning
rsync -av --backup --backup-dir=/backup/$(date +%Y%m%d) \
  --delete /source/ /current/

# tar qua SSH — backup remote không cần disk tạm
ssh user@server "tar -czf - /etc/" > /local/backup/server_etc.tar.gz

# Giải nén và xem tiến trình
tar -xzf largefile.tar.gz | pv -p -b > /dev/null
```

---

## Lỗi thường gặp (Common Pitfalls)

**1. `rm -rf` với trailing slash sai vị trí**
```bash
# THẢM HỌA tiềm ẩn:
DIR="/mydata/"
rm -rf $DIR   # nếu DIR="" thì => rm -rf /

# An toàn hơn:
[[ -z "$DIR" ]] && exit 1
rm -rf "${DIR:?'DIR is empty'}"
```

**2. `cp -r src/ dst/` vs `cp -r src dst/`**
```bash
cp -r src/ dst/   # copy NỘI DUNG của src vào dst (dst/file1, dst/file2)
cp -r src dst/    # copy THƯ MỤC src vào dst (dst/src/file1)
# Tương tự rsync: trailing / quan trọng!
```

**3. Hard link không hoạt động cross-filesystem**
```bash
ln /home/user/file.txt /mnt/usb/file.txt  # LỖI: cross-device link
# Dùng symlink thay thế:
ln -s /home/user/file.txt /mnt/usb/file.txt
```

**4. Symlink relative vs absolute**
```bash
# Absolute symlink — luôn đúng
ln -s /abs/path/to/target linkname
# Relative symlink — chỉ đúng nếu link và target cùng relative position
ln -s ../target linkname
# Dùng ln -sf để replace symlink cũ
```

**5. `stat` ctime là "change time" không phải "create time"**
```bash
# ctime thay đổi khi: chmod, chown, rename, write
# Linux không lưu creation time (birthtime) trong ext4 mặc định
stat --format="%w" file  # birthtime trên ext4 với kernel mới
```

**6. `tar` không bao gồm leading `/` khi extract**
```bash
tar -czf backup.tar.gz /etc/passwd   # lưu dưới dạng etc/passwd (không có /)
tar -xzf backup.tar.gz               # extract ra ./etc/passwd
# Giải nén về đúng vị trí:
tar -xzf backup.tar.gz -C / --strip-components=0
```

**7. rsync trailing slash**
```bash
rsync -av /src/    /dst/   # copy NỘI DUNG src vào dst
rsync -av /src     /dst/   # copy THƯ MỤC src vào dst (tạo /dst/src/)
```

---

## Câu hỏi phỏng vấn hay gặp

**Q1: Sự khác biệt giữa hard link và symbolic link?**
> Hard link: cùng inode, không thể cross-filesystem, nếu xóa file gốc thì link vẫn truy cập được data (vì cùng inode). Symbolic link: file riêng chứa đường dẫn, có thể cross-filesystem, nếu target bị xóa thì link bị "dangling". `ls -la` hiện symlink với `->`.

**Q2: Tại sao `rm` không thể xóa thư mục nếu không có `-r`?**
> `rm` mặc định gọi `unlink()` syscall, chỉ hoạt động trên file. Directory cần `rmdir()` syscall (chỉ xóa được thư mục rỗng) hoặc duyệt đệ quy để xóa từng file rồi mới xóa thư mục. Flag `-r` bật behavior đó.

**Q3: Giải thích `atime`, `mtime`, `ctime`?**
> `atime` (access): cập nhật khi file được đọc. `mtime` (modify): cập nhật khi nội dung file thay đổi. `ctime` (change): cập nhật khi inode thay đổi (chmod, chown, rename, hoặc mtime thay đổi). `ctime` không phải "creation time". Nhiều filesystem mount với `noatime` để tăng performance.

**Q4: `cp` vs `rsync` — khi nào dùng cái nào?**
> `cp` đơn giản hơn, tốt cho copy one-off. `rsync` hiệu quả hơn cho sync incremental (chỉ copy delta), có progress indicator, bandwidth throttling, dry-run mode, và bảo toàn metadata tốt hơn. Cho backup scripts, luôn dùng `rsync`.

**Q5: Tại sao `dd` nguy hiểm?**
> `dd` không kiểm tra, không hỏi, không có safety net. `dd if=/dev/sda of=/dev/sdb` ghi đè toàn bộ `/dev/sdb` mà không báo lỗi. Nhầm `if` và `of` có thể xóa toàn bộ disk nguồn. Luôn double-check lệnh trước khi Enter.

**Q6: File bị xóa nhưng vẫn chiếm disk space — tại sao?**
> Process đang giữ file descriptor open. `unlink()` xóa directory entry (giảm link count), nhưng inode và data blocks chỉ được giải phóng khi link count = 0 VÀ không có open file descriptor. Lệnh: `lsof | grep deleted` để tìm các file như vậy.
