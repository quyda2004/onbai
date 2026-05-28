# Trắc nghiệm — Linux File Operations

> **Tổng số câu:** 20 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Lệnh nào tạo thư mục `project/src/main` kể cả các thư mục trung gian chưa tồn tại?

- A. `mkdir project/src/main`
- B. `mkdir -r project/src/main`
- C. `mkdir -p project/src/main`
- D. `mkdir --recursive project/src/main`

> **Đáp án: C** — `-p` (parents) tạo tất cả thư mục trung gian cần thiết. Thiếu `-p` sẽ báo lỗi nếu `project/src` chưa tồn tại.

---

**Câu 2:** Lệnh `rm -rf directory/` làm gì?

- A. Xóa thư mục rỗng
- B. Xóa toàn bộ thư mục và nội dung bên trong, không hỏi xác nhận
- C. Chuyển thư mục vào Recycle Bin
- D. Xóa nhưng vẫn hỏi xác nhận

> **Đáp án: B** — `-r` recursive (xóa thư mục), `-f` force (không hỏi). ⚠️ Không có Undo. Dùng `-i` để hỏi trước khi xóa từng file.

---

**Câu 3:** Lệnh `cp -a source/ dest/` có gì đặc biệt so với `cp -r source/ dest/`?

- A. Không có gì khác
- B. `-a` giữ nguyên permissions, timestamps, symlinks, và owner
- C. `-a` copy nhanh hơn
- D. `-a` chỉ copy file mới hơn

> **Đáp án: B** — `-a` (archive) = `-dpr` (preserve symlinks, preserve permissions+timestamps+owner, recursive). Dùng `-a` khi backup, deploy để giữ đúng metadata.

---

**Câu 4:** `tail -f /var/log/syslog` làm gì?

- A. Xem 10 dòng cuối và thoát
- B. Follow — tự động cập nhật khi có dòng mới được ghi vào file
- C. Xem file từ đầu đến cuối
- D. Đếm số dòng trong file

> **Đáp án: B** — `-f` (follow) giữ file mở và in dòng mới khi chúng được thêm vào. Rất hữu ích để xem log real-time. Dùng `-F` để follow và reopen nếu file bị rotate.

---

**Câu 5:** Lệnh `touch newfile.txt` làm gì khi `newfile.txt` đã tồn tại?

- A. Xóa và tạo lại file rỗng
- B. Báo lỗi
- C. Cập nhật access time và modification time thành thời điểm hiện tại
- D. Không làm gì

> **Đáp án: C** — `touch` cập nhật timestamp của file thành `now`. Nếu file chưa tồn tại mới tạo file rỗng. Hữu ích để "fake" rằng file vừa được sửa (trigger make/build systems).

---

**Câu 6:** Lệnh `head -n -5 file.txt` in gì?

- A. 5 dòng đầu
- B. 5 dòng cuối
- C. Tất cả dòng trừ 5 dòng cuối
- D. Từ dòng 5 đến cuối

> **Đáp án: C** — `-n -5` có nghĩa "tất cả trừ 5 dòng cuối". `head -n 5` in 5 dòng đầu. `tail -n 5` in 5 dòng cuối. `tail -n +5` in từ dòng 5 đến cuối.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** `command > file.txt 2>&1` có nghĩa là gì?

- A. Chạy command 2 lần, lần 1 output ra file, lần 2 vào stderr
- B. Redirect stdout sang file.txt, redirect stderr sang stdout (nên cả hai đều vào file)
- C. Redirect stderr sang file, stdout bình thường
- D. Redirect tất cả vào stderr

> **Đáp án: B** — `> file.txt` redirect stdout (fd 1) sang file. `2>&1` redirect stderr (fd 2) sang fd 1 (đang là file). Thứ tự quan trọng! `2>&1 > file` sẽ không làm đúng.

---

**Câu 8:** Sự khác biệt giữa `>` và `>>` trong shell?

- A. `>` append, `>>` overwrite
- B. `>` overwrite (xóa nội dung cũ), `>>` append (thêm vào cuối)
- C. Không có sự khác biệt
- D. `>>` chỉ dùng với stderr

> **Đáp án: B** — `>` truncate file và ghi mới. `>>` giữ nội dung cũ, ghi tiếp vào cuối. Dùng `>>` khi muốn accumulate log.

---

**Câu 9:** Lệnh `tar -czf backup.tar.gz /home/alice` làm gì?

- A. Giải nén backup.tar.gz vào /home/alice
- B. Nén thư mục /home/alice thành file backup.tar.gz dùng gzip
- C. Liệt kê nội dung backup.tar.gz
- D. Xem nội dung không giải nén

> **Đáp án: B** — `-c` create, `-z` gzip compression, `-f` filename. Tổ hợp: tạo compressed archive. `-x` là extract, `-t` là list.

---

**Câu 10:** `ln -s /usr/bin/python3 /usr/local/bin/python` tạo gì?

- A. Hard link từ python đến python3
- B. Copy của python3
- C. Symbolic link: `/usr/local/bin/python` → `/usr/bin/python3`
- D. Alias trong shell

> **Đáp án: C** — `-s` tạo symbolic link. Sau đó `python` sẽ thực thi python3. `readlink /usr/local/bin/python` sẽ hiện `/usr/bin/python3`.

---

**Câu 11:** `cat file1.txt file2.txt > combined.txt` và `cat file1.txt >> combined.txt` khác nhau thế nào?

- A. Không khác gì
- B. Lệnh 1 tạo file mới (overwrite) từ 2 file; lệnh 2 thêm file1 vào cuối combined.txt
- C. Lệnh 1 chỉ in ra terminal, lệnh 2 lưu vào file
- D. Lệnh 2 chạy nhanh hơn

> **Đáp án: B** — Lệnh 1 dùng `>` overwrite: nếu combined.txt đã tồn tại sẽ bị xóa và tạo lại từ file1+file2. Lệnh 2 dùng `>>` append: chỉ thêm file1 vào cuối combined.txt.

---

**Câu 12:** `rsync -av source/ dest/` có gì khác `cp -a source/ dest/`?

- A. Không khác gì
- B. rsync chỉ transfer files đã thay đổi (delta), hiệu quả hơn với large datasets; có thể sync qua SSH
- C. rsync nhanh hơn vì dùng UDP
- D. rsync giữ permissions, cp thì không

> **Đáp án: B** — rsync sử dụng delta algorithm: chỉ transfer phần thay đổi. Với 10GB data, nếu chỉ thay đổi 1MB, rsync chỉ transfer 1MB. `cp` luôn copy toàn bộ. rsync còn hỗ trợ SSH, `--delete`, dry-run `-n`.

---

**Câu 13:** `wc -l *.py | tail -1` làm gì?

- A. Đếm từ trong các file .py
- B. Đếm số dòng trong từng file .py, rồi in dòng cuối (tổng cộng tất cả)
- C. Tìm file .py có nhiều dòng nhất
- D. In dòng cuối của tất cả file .py

> **Đáp án: B** — `wc -l *.py` in số dòng của từng file và tổng (`total`) ở dòng cuối. `tail -1` lấy dòng cuối đó = tổng số dòng của tất cả .py files.

---

**Câu 14:** Lệnh `file image.jpg` trả về gì?

- A. Kích thước file
- B. Magic bytes / loại thực sự của file (không phụ thuộc vào extension)
- C. Checksum của file
- D. Metadata của ảnh (EXIF)

> **Đáp án: B** — `file` đọc magic bytes (signature bytes) đầu file để xác định loại thực sự. `file script` có thể trả về "ELF 64-bit executable" dù không có extension. Hữu ích với file không rõ loại.

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Phân tích lệnh nguy hiểm: `rm -rf ` (có space sau dấu \ nhưng không có tên thư mục nào). Điều gì xảy ra?

- A. Không làm gì (thiếu argument)
- B. Xóa thư mục hiện tại
- C. Tùy shell và globbing — có thể xóa toàn bộ thư mục hiện tại
- D. Báo lỗi và dừng

> **Đáp án: C** — Nếu shell expand `` thành tất cả files, lệnh trở thành `rm -rf file1 file2 ...`. Nếu argument rỗng, behavior tùy shell. Đây là lý do `rm -rf "$dir"` phải check `$dir` không rỗng trước. Luôn test với `echo rm -rf "$dir"` trước khi chạy.

---

**Câu 16:** `cp dir/* dest/` sẽ KHÔNG copy gì?

- A. File lớn hơn 1GB
- B. File ẩn (bắt đầu bằng `.`)
- C. Symbolic links
- D. Binary files

> **Đáp án: B** — `*` glob trong shell không match file ẩn (bắt đầu bằng `.` như `.bashrc`, `.gitignore`). Để copy tất cả kể cả hidden: `cp -a dir/. dest/` hoặc `rsync -a dir/ dest/`.

---

**Câu 17:** Đường ống (pipe) `command1 | command2` hoạt động thế nào?

- A. Chạy command1, lưu output vào temp file, command2 đọc file đó
- B. stdout của command1 kết nối trực tiếp với stdin của command2 qua kernel pipe
- C. command2 chạy command1 như sub-process
- D. Cả hai command chạy tuần tự, không kết nối

> **Đáp án: B** — Pipe là IPC mechanism của kernel: kernel tạo pipe buffer, command1 ghi vào fd1 (stdout) → kernel buffer → command2 đọc từ fd0 (stdin). Cả hai process chạy **đồng thời**, không cần temp file.

---

**Câu 18:** Lệnh nào in vừa ra terminal vừa ghi vào file?

- A. `command > file.txt && cat file.txt`
- B. `command | tee file.txt`
- C. `command 2>&1 file.txt`
- D. `command | cat file.txt`

> **Đáp án: B** — `tee` đọc stdin, ghi đồng thời vào stdout VÀ file. Có thể chain: `command | tee file.txt | wc -l` vừa save vừa đếm dòng. `tee -a` để append.

---

**Câu 19:** Hard link không thể tạo giữa hai filesystem khác nhau vì sao?

- A. Vì filesystem không share inode space — inode number chỉ unique trong cùng filesystem
- B. Vì kernel không hỗ trợ
- C. Vì permission không cho phép
- D. Vì hard link chỉ dùng với text files

> **Đáp án: A** — Hard link lưu inode number. Mỗi filesystem có inode table riêng → inode 1234 ở /dev/sda1 và inode 1234 ở /dev/sdb1 là hai thứ khác nhau. Symbolic link không gặp vấn đề này vì trỏ đến path, không phải inode.

---

**Câu 20:** `tar -xzf backup.tar.gz -C /restore/` khác `tar -xzf backup.tar.gz` thế nào?

- A. `-C` dùng gzip khác
- B. `-C /restore/` extract vào thư mục `/restore/` thay vì thư mục hiện tại
- C. `-C` skip các file lỗi
- D. Không có sự khác biệt

> **Đáp án: B** — `-C dir` (change to directory) chỉ định thư mục đích khi extract. Nếu không có `-C`, extract vào thư mục hiện tại. Luôn dùng `-t` để xem nội dung trước khi extract: `tar -tzf backup.tar.gz`.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | C      | 11  | B      |
| 2   | B      | 12  | B      |
| 3   | B      | 13  | B      |
| 4   | B      | 14  | B      |
| 5   | C      | 15  | C      |
| 6   | C      | 16  | B      |
| 7   | B      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | A      |
| 10  | C      | 20  | B      |
