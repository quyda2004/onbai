# Trắc nghiệm — Linux Text Processing

> **Tổng số câu:** 20 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Lệnh `grep -i "error" file.log` tìm gì?

- A. Chỉ tìm "error" viết thường
- B. Tìm "error", "ERROR", "Error" và mọi biến thể hoa thường
- C. Tìm ngược lại — dòng không có "error"
- D. Tìm trong tất cả thư mục con

> **Đáp án: B** — `-i` là case-insensitive. `-v` là invert (dòng không match). `-r` là recursive. `-n` hiện số dòng.

---

**Câu 2:** `grep -v "DEBUG" app.log` làm gì?

- A. Tìm dòng chứa "DEBUG"
- B. In ra tất cả dòng KHÔNG chứa "DEBUG"
- C. Xóa dòng chứa "DEBUG" khỏi file
- D. Đếm số dòng có "DEBUG"

> **Đáp án: B** — `-v` (invert match) in những dòng KHÔNG khớp với pattern. Hữu ích để lọc bỏ noise.

---

**Câu 3:** `sed 's/old/new/g' file.txt` vs `sed 's/old/new/' file.txt` — khác nhau thế nào?

- A. Không khác gì
- B. `/g` thay tất cả lần xuất hiện trên mỗi dòng; không có `/g` chỉ thay lần đầu tiên mỗi dòng
- C. `/g` thay trên tất cả các dòng; không có `/g` chỉ thay dòng đầu
- D. `/g` edit in-place; không có `/g` chỉ in ra stdout

> **Đáp án: B** — `s/old/new/` thay lần đầu trên mỗi dòng. `s/old/new/g` (global) thay tất cả. Ví dụ: dòng "aaa" với `s/a/b/` → "baa", với `s/a/b/g` → "bbb".

---

**Câu 4:** `sort` cần thiết trước `uniq` vì sao?

- A. `sort` tăng tốc uniq
- B. `uniq` chỉ loại bỏ consecutive (liên tiếp) duplicates — cần sort để duplicate adjacent nhau
- C. `sort` thêm line numbers cho uniq
- D. Không cần, thứ tự không quan trọng

> **Đáp án: B** — `uniq` chỉ loại bỏ dòng trùng liền kề nhau. File: "a\nb\na" → `uniq` giữ cả 3 (không adjacent). Sau `sort`: "a\na\nb" → `uniq` → "a\nb". Luôn `sort | uniq`.

---

**Câu 5:** `awk '{print $3}' file.txt` làm gì?

- A. In 3 ký tự đầu mỗi dòng
- B. In cột 3 (từ thứ 3, space-separated) của mỗi dòng
- C. In dòng thứ 3
- D. Skip 3 dòng đầu và in phần còn lại

> **Đáp án: B** — `$3` là field 3. `$0` là toàn dòng, `$1` là field 1, `$NF` là field cuối (NF = Number of Fields). Delimiter mặc định là whitespace.

---

**Câu 6:** `cut -d: -f1 /etc/passwd` làm gì?

- A. Cắt 1 ký tự đầu của mỗi dòng
- B. In field 1 từ /etc/passwd với delimiter `:` (tức là usernames)
- C. In dòng 1 của /etc/passwd
- D. Xóa dấu `:` trong /etc/passwd

> **Đáp án: B** — `-d:` đặt delimiter là `:`, `-f1` lấy field 1. `/etc/passwd` format: `name:x:uid:gid:info:home:shell` → field 1 là usernames.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Pipeline nào đếm top 5 IP address xuất hiện nhiều nhất trong access.log?

- A. `grep access.log | count | top 5`
- B. `awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -5`
- C. `cut access.log | sort -n | head -5`
- D. `wc -l access.log | sort | head -5`

> **Đáp án: B** — Quy trình: (1) `awk '{print $1}'` lấy IP (field 1 trong nginx log), (2) `sort` để uniq hoạt động, (3) `uniq -c` đếm tần suất, (4) `sort -rn` sort ngược numeric (nhiều nhất đầu), (5) `head -5` lấy top 5.

---

**Câu 8:** `sed -i 's/http:/https:/g' config.txt` có gì đặc biệt với `-i`?

- A. `-i` là case-insensitive
- B. `-i` edit in-place — sửa trực tiếp file gốc
- C. `-i` tạo backup tự động
- D. `-i` chỉ thay trên dòng đầu tiên

> **Đáp án: B** — `-i` (in-place) sửa file gốc thay vì in ra stdout. Dùng `-i.bak` để tạo backup: `sed -i.bak 's/old/new/g' file` → tạo `file.bak` trước khi sửa.

---

**Câu 9:** `awk -F: '$3 >= 1000 {print $1}' /etc/passwd` làm gì?

- A. In dòng thứ 1000 của /etc/passwd
- B. In usernames của các regular users (UID >= 1000, tức không phải system users)
- C. In 1000 dòng đầu của /etc/passwd
- D. Tìm user có password >= 1000 ký tự

> **Đáp án: B** — `-F:` đặt delimiter `:`, `$3` là UID, `>= 1000` filter regular users (system accounts thường có UID < 1000), `{print $1}` in tên user (field 1).

---

**Câu 10:** `grep -E "[0-9]{3}\.[0-9]{3}\.[0-9]{3}\.[0-9]{3}"` tìm pattern gì?

- A. Mọi số có 3 chữ số
- B. IPv4 address pattern (dạng đơn giản)
- C. Mọi chuỗi có 12 chữ số
- D. Số điện thoại

> **Đáp án: B** — `-E` dùng Extended Regex. `[0-9]{3}` match đúng 3 chữ số, `\.` match dấu chấm literal. Pattern này match `192.168.001.001` (simplified, không validate range 0-255).

---

**Câu 11:** `tr -s ' ' < file.txt` làm gì?

- A. Thay tất cả space bằng tab
- B. Squeeze — replace nhiều spaces liên tiếp thành 1 space
- C. Xóa tất cả spaces
- D. Đếm số spaces

> **Đáp án: B** — `-s` (squeeze-repeats) thay nhiều ký tự giống nhau liên tiếp thành 1. `tr -s ' '` → "hello   world" → "hello world". Hữu ích để normalize whitespace.

---

**Câu 12:** `find . -name "*.py" | xargs grep -l "TODO"` làm gì?

- A. Đếm số TODO trong file .py
- B. In tên các file .py có chứa ít nhất 1 "TODO"
- C. Thay thế "TODO" trong file .py
- D. Tạo file chứa tên các file .py có TODO

> **Đáp án: B** — `find` tìm file .py, `xargs grep -l "TODO"` chạy `grep -l` (chỉ in tên file có match) trên từng file. `-l` = files with matches (không in nội dung match, chỉ tên file).

---

**Câu 13:** `diff -u file1.txt file2.txt` tạo output dạng gì?

- A. Chỉ liệt kê dòng trong file1 không có trong file2
- B. Unified diff format — hiện context lines, dòng xóa có `-`, dòng thêm có `+`
- C. Side-by-side comparison
- D. Binary diff

> **Đáp án: B** — `-u` (unified) format là chuẩn cho patch: `---` là file cũ, `+++` là file mới, `-line` là dòng bị xóa, `+line` là dòng được thêm. Đây là format của `git diff`.

---

**Câu 14:** `sort -k2 -n file.txt` sort file theo tiêu chí nào?

- A. Sort alphabetically theo toàn dòng
- B. Sort numeric theo giá trị của field/cột 2
- C. Sort theo ký tự thứ 2 của mỗi dòng
- D. Sort ngược (reverse) theo cột 2

> **Đáp án: B** — `-k2` chỉ định key field 2, `-n` là numeric sort (phân biệt 1, 2, 10 khác với lexicographic 1, 10, 2). Thiếu `-n` thì "10" < "2" về mặt string.

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Lệnh sau làm gì?

```bash
awk '{sum += $3; count++} END {printf "Average: %.2f\n", sum/count}' data.txt
```

- A. Tính tổng cột 3
- B. Tính trung bình cột 3 với 2 chữ số thập phân, xử lý toàn file
- C. In dòng cuối của file
- D. Đếm số dòng trong file

> **Đáp án: B** — `sum += $3` cộng dồn cột 3, `count++` đếm dòng. `END` block chạy sau khi đọc hết file. `printf "%.2f"` format 2 chữ số thập phân.

---

**Câu 16:** `sed -n '50,100p' largefile.txt` làm gì và tại sao hiệu quả hơn `head -100 | tail -51`?

- A. In dòng 50 đến 100 (không khác gì head/tail)
- B. In dòng 50 đến 100; sed dừng sau dòng 100 nên không phải đọc hết file, hiệu quả hơn với file rất lớn
- C. In 50 dòng bắt đầu từ dòng 100
- D. In toàn bộ file trừ dòng 50-100

> **Đáp án: B** — `sed -n '50,100p'` với `q` (quit) sẽ dừng sau dòng 100. `head -100 | tail -51` phải đọc đến dòng 100 rồi tính ngược. Với file GB, cả hai đều dừng sớm. Cách tốt nhất: `sed -n '50,100p; 101q'`.

---

**Câu 17:** Giải thích lệnh sau:

```bash
ps aux | awk '$3 > 5.0 {print $1, $2, $3, $11}'
```

- A. Liệt kê processes dùng hơn 5MB RAM
- B. Liệt kê user, PID, %CPU, command của processes dùng hơn 5% CPU
- C. Liệt kê 5 processes đầu tiên
- D. Kill processes dùng hơn 5% CPU

> **Đáp án: B** — Trong `ps aux`: $1=USER, $2=PID, $3=%CPU, $4=%MEM, ..., $11=COMMAND. `$3 > 5.0` filter dòng có %CPU > 5.

---

**Câu 18:** Lệnh `grep -oP "(?<=Authorization: Bearer )\S+"` dùng để làm gì?

- A. Tìm dòng có "Authorization: Bearer"
- B. Extract JWT token sau "Authorization: Bearer " (Perl lookbehind)
- C. Xóa Authorization header
- D. Thay thế Bearer token

> **Đáp án: B** — `-o` chỉ in phần match, `-P` Perl regex, `(?<=...)` là lookbehind (không include trong match), `\S+` là non-whitespace characters. Kết quả: chỉ in token, không kèm "Authorization: Bearer".

---

**Câu 19:** Pipeline nào xóa comment và dòng trống từ config file?

- A. `cat config.conf | remove comments`
- B. `grep -v '^#' config.conf | grep -v '^$'`
- C. `sed 'd/^#/' config.conf`
- D. `awk '!/#/' config.conf`

> **Đáp án: B** — `grep -v '^#'` loại dòng bắt đầu bằng `#` (comment). `grep -v '^$'` loại dòng trống (`^$` = bắt đầu rồi kết thúc ngay). Cách sed tương đương: `sed '/^#/d; /^$/d' config.conf`.

---

**Câu 20:** `tee` command dùng để làm gì trong pipeline sau?

```bash
make 2>&1 | tee build.log | grep -E "error|warning"
```

- A. Chỉ lọc error và warning
- B. Vừa lưu toàn bộ output vào build.log, vừa tiếp tục pipeline để grep lọc error/warning hiển thị terminal
- C. Chạy make 2 lần
- D. Ghi file log và dừng pipeline

> **Đáp án: B** — `tee` là T-junction: đọc stdin, ghi ra stdout VÀ file. Ở đây: make output (stdout+stderr sau `2>&1`) → `tee` ghi vào `build.log` VÀ chuyển tiếp → `grep` lọc hiện terminal. Sau khi xong, `build.log` có full output, terminal chỉ thấy errors/warnings.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 11  | B      |
| 2   | B      | 12  | B      |
| 3   | B      | 13  | B      |
| 4   | B      | 14  | B      |
| 5   | B      | 15  | B      |
| 6   | B      | 16  | B      |
| 7   | B      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | B      |
| 10  | B      | 20  | B      |
