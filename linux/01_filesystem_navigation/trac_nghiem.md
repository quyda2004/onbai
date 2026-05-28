# Trắc nghiệm — Linux Filesystem Navigation

> **Tổng số câu:** 20 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Lệnh nào hiển thị thư mục đang làm việc hiện tại?

- A. `ls`
- B. `cd`
- C. `pwd`
- D. `dir`

> **Đáp án: C** — `pwd` (print working directory) in ra đường dẫn tuyệt đối của thư mục hiện tại. `ls` liệt kê nội dung, `cd` di chuyển, `dir` không phải lệnh Linux chuẩn.

---

**Câu 2:** Lệnh `ls -la` làm gì?

- A. Liệt kê file theo kích thước
- B. Liệt kê tất cả file kể cả file ẩn, ở định dạng long (chi tiết)
- C. Liệt kê file trong tất cả thư mục con
- D. Chỉ liệt kê thư mục, không file

> **Đáp án: B** — `-l` là long format (hiện permissions, owner, size...), `-a` là all (bao gồm file ẩn bắt đầu bằng `.`). Kết hợp `-la` là cả hai.

---

**Câu 3:** `~` trong Linux có nghĩa là gì?

- A. Thư mục root `/`
- B. Thư mục hiện tại
- C. Home directory của user hiện tại
- D. Thư mục cha

> **Đáp án: C** — `~` expand thành home directory của user hiện tại, ví dụ `/home/alice`. `~bob` là home của user bob.

---

**Câu 4:** Lệnh `cd ..` làm gì?

- A. Di chuyển về home directory
- B. Di chuyển đến thư mục cha (lên một cấp)
- C. Di chuyển đến thư mục gốc `/`
- D. Di chuyển về thư mục trước đó

> **Đáp án: B** — `..` là thư mục cha. `cd ~` hoặc `cd` về home, `cd /` về root, `cd -` về thư mục trước.

---

**Câu 5:** Thư mục `/etc` trong Linux chứa gì?

- A. Home directories của users
- B. Temporary files
- C. Configuration files của hệ thống
- D. Binary executables

> **Đáp án: C** — `/etc` chứa system configuration files (nginx.conf, passwd, hosts...). `/home` là home dirs, `/tmp` là temp files, `/bin` là executables.

---

**Câu 6:** Lệnh nào tìm tất cả file `.log` trong thư mục hiện tại và các thư mục con?

- A. `ls -r *.log`
- B. `find . -name "*.log"`
- C. `grep -r "*.log" .`
- D. `locate *.log .`

> **Đáp án: B** — `find . -name "*.log"` tìm đệ quy từ thư mục hiện tại (`.`). `ls -r` không tìm đệ quy, `grep` dùng để tìm nội dung trong file không phải tên file.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Sự khác biệt giữa `/proc/cpuinfo` và một file thông thường?

- A. `/proc/cpuinfo` được mã hóa
- B. `/proc/cpuinfo` không tồn tại trên disk — kernel tạo on-the-fly trong RAM khi đọc
- C. `/proc/cpuinfo` chỉ đọc được bởi root
- D. Không có sự khác biệt

> **Đáp án: B** — `/proc` là virtual filesystem. Kernel sinh ra dữ liệu trong RAM khi có process đọc, không lưu trên disk. Tương tự với `/sys`.

---

**Câu 8:** Lệnh `find / -size +100M -type f` làm gì?

- A. Tìm thư mục lớn hơn 100MB
- B. Tìm tất cả file có kích thước lớn hơn 100MB trên toàn hệ thống
- C. Tạo file 100MB
- D. Xóa file lớn hơn 100MB

> **Đáp án: B** — `find /` từ root, `-size +100M` lớn hơn 100MB, `-type f` chỉ file (không thư mục). Cộng `2>/dev/null` để ẩn "Permission denied".

---

**Câu 9:** `df -h` và `du -sh` khác nhau thế nào?

- A. `df` xem file size, `du` xem disk space
- B. `df` xem toàn bộ mounted filesystem, `du` xem usage của thư mục/file cụ thể
- C. Không có sự khác biệt
- D. `df` chỉ dùng được với root

> **Đáp án: B** — `df -h` hiển thị space của các mount point (ổ đĩa). `du -sh dir/` tính kích thước của directory cụ thể. Dùng `du -sh * | sort -rh` để tìm thư mục chiếm nhiều nhất.

---

**Câu 10:** Hard link vs Symbolic link — điểm nào đúng?

- A. Symbolic link giữ data nếu file gốc bị xóa
- B. Hard link trỏ đến inode, symbolic link trỏ đến path; xóa file gốc → symlink bị broken
- C. Hard link hoạt động cross filesystem
- D. Symbolic link và hard link có cùng inode number

> **Đáp án: B** — Hard link: trỏ đến cùng inode, không cross filesystem, xóa "file gốc" thì data vẫn còn (reference count). Symlink: trỏ đến path, xóa path gốc → symlink broken, có thể cross filesystem.

---

**Câu 11:** Lệnh nào hiển thị dung lượng của mỗi file/thư mục trong thư mục hiện tại, sắp xếp từ lớn đến nhỏ?

- A. `ls -lS`
- B. `du -sh * | sort -rh`
- C. `df -h | sort -rh`
- D. `find . -size | sort`

> **Đáp án: B** — `du -sh *` tính size từng item, `sort -rh` sort reverse human-readable. `-h` trong sort hiểu 1K, 1M, 1G. `ls -lS` sort theo byte size không human-readable.

---

**Câu 12:** Lệnh `find . -mtime -7` tìm gì?

- A. File sửa đổi hơn 7 ngày trước
- B. File sửa đổi trong 7 ngày qua
- C. File có kích thước 7 bytes
- D. File tạo 7 phút trước

> **Đáp án: B** — `-mtime -7` là "modified time less than 7 days ago" (trong 7 ngày qua). `-mtime +7` là "hơn 7 ngày trước". `-mmin -60` là trong 60 phút qua.

---

**Câu 13:** Lệnh `pushd /etc && popd` làm gì?

- A. Push file /etc vào queue và xóa
- B. `pushd` cd đến /etc và lưu thư mục hiện tại vào stack; `popd` quay về thư mục đó
- C. Tạo link từ /etc
- D. Copy /etc và restore

> **Đáp án: B** — `pushd dir` = push current dir lên stack, rồi cd đến dir. `popd` = pop stack và cd về. Hữu ích khi cần switch qua lại nhiều thư mục.

---

**Câu 14:** `find . -name "*.py" -exec grep "TODO" {} \;` làm gì?

- A. Tìm file .py có tên "TODO"
- B. Tìm tất cả file .py, rồi chạy grep "TODO" trên từng file
- C. Tìm "TODO" trong tất cả file
- D. Xóa file .py chứa "TODO"

> **Đáp án: B** — `-exec command {} \;` chạy command với mỗi file tìm thấy, `{}` là placeholder. Tương đương `find . -name "*.py" | xargs grep "TODO"` (nhưng xargs thường nhanh hơn).

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Tại sao `locate` nhanh hơn `find` nhưng đôi khi cho kết quả cũ?

- A. `locate` dùng RAM cache, `find` đọc disk
- B. `locate` tra database được index trước (`updatedb`); database có thể lỗi thời nếu chưa cập nhật
- C. `find` bị giới hạn bởi quyền user
- D. `locate` không hỗ trợ wildcard

> **Đáp án: B** — `locate` tra `/var/lib/mlocate/mlocate.db` được cron cập nhật (thường 1 lần/ngày). File mới tạo chưa được index. `find` duyệt filesystem thực tế → luôn chính xác nhưng chậm.

---

**Câu 16:** Điều gì xảy ra khi chạy `find . -name "*.log" -delete`?

- A. Hỏi xác nhận trước mỗi file
- B. Xóa VĨNH VIỄN tất cả file .log tìm thấy, không hỏi
- C. Move vào Trash
- D. Chỉ list file, không xóa

> **Đáp án: B** — `-delete` xóa trực tiếp, không hỏi, không Trash. Nên chạy `find . -name "*.log"` trước để kiểm tra danh sách, rồi mới thêm `-delete`.

---

**Câu 17:** Đường dẫn tuyệt đối và tương đối khác nhau thế nào?

- A. Tuyệt đối dùng symlinks, tương đối không dùng
- B. Tuyệt đối bắt đầu bằng `/` và luôn đúng bất kể đang ở đâu; tương đối tính từ thư mục hiện tại
- C. Tuyệt đối chỉ dùng được với root user
- D. Không có sự khác biệt thực tế

> **Đáp án: B** — `/home/alice/file.txt` là absolute, luôn trỏ đúng. `../file.txt` là relative, tính từ cwd. Script nên dùng absolute path để tránh lỗi khi chạy từ thư mục khác.

---

**Câu 18:** Lệnh nào hiển thị inode number của file?

- A. `ls -l file`
- B. `ls -i file`
- C. `stat -n file`
- D. `find -inode file`

> **Đáp án: B** — `ls -i` hiển thị inode number. `stat file` cũng hiện inode trong output chi tiết. Hai hard link cùng inode number → cùng data.

---

**Câu 19:** `/dev/null` là gì và dùng để làm gì?

- A. File chứa null bytes, dùng để tạo file rỗng
- B. Special device file — bỏ toàn bộ data ghi vào nó, đọc ra 0 bytes (black hole)
- C. Symbolic link đến /tmp
- D. File tạm, bị xóa khi reboot

> **Đáp án: B** — `/dev/null` là "black hole": ghi vào → biến mất, đọc ra → ngay lập tức EOF. Dùng để suppress output: `command > /dev/null 2>&1` (bỏ cả stdout và stderr).

---

**Câu 20:** Kết quả của `ls -la | awk '{print $9}'` là gì?

- A. Kích thước file
- B. Owner của file
- C. Tên file (cột 9 trong output của ls -la)
- D. Ngày sửa cuối

> **Đáp án: C** — Output của `ls -la`: cột 1=permissions, 2=links, 3=owner, 4=group, 5=size, 6=month, 7=day, 8=time/year, 9=filename. `awk '{print $9}'` in ra tên file.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | C      | 11  | B      |
| 2   | B      | 12  | B      |
| 3   | C      | 13  | B      |
| 4   | B      | 14  | B      |
| 5   | C      | 15  | B      |
| 6   | B      | 16  | B      |
| 7   | B      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | B      |
| 10  | B      | 20  | C      |
