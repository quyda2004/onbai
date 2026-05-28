# Linux Text Processing — Xử lý Văn bản

---

## Giải thích cho người mới hoàn toàn

Linux coi mọi thứ gần như là văn bản (text). Config của phần mềm là text, logs là text, danh sách process là text. Vì vậy, biết cách lọc, tìm kiếm, và biến đổi text là kỹ năng cực kỳ quan trọng.

Hãy nghĩ như đang xử lý một cuốn sách khổng lồ:
- `cat` = đọc toàn bộ cuốn sách ra màn hình
- `head`/`tail` = chỉ đọc vài trang đầu/cuối
- `grep` = máy tìm kiếm từ khóa — bôi vàng những dòng chứa từ bạn cần
- `sed` = bút xóa + bút viết — tìm từ/cụm từ và thay thế
- `awk` = máy tính + bộ lọc thông minh — xử lý từng "cột" trong bảng

Sức mạnh thật sự đến từ việc nối các lệnh này lại bằng dấu `|` (pipe). Giống như dây chuyền sản xuất: văn bản chạy qua từng máy, mỗi máy làm một việc.

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Triết lý Unix:** Mỗi công cụ làm một việc tốt, input/output là text stream. Pipe (`|`) kết nối stdout của lệnh này với stdin của lệnh kia. Đây là functional programming style: compose small pure functions.

**`grep` và regex engine:** `grep` dùng POSIX BRE (Basic Regular Expressions) mặc định. `-E` hoặc `egrep` dùng ERE (Extended: `+`, `?`, `|`, `()` không cần escape). `-P` dùng PCRE (Perl-compatible: lookahead, lookbehind, `\d`, `\w`). Performance: `grep` là C code tối ưu với Boyer-Moore-Horspool algorithm — nhanh hơn Python/Ruby grep nhiều lần.

**`sed` là stream editor:** Đọc từng dòng vào pattern space, thực hiện commands, output. `-i` edit in-place (thực ra tạo file tạm rồi rename). `-n` suppress automatic print. Address có thể là line number, regex, hoặc range `first~step`.

**`awk` là programming language:** Có built-in arrays, math functions, string functions, regex. Pattern/action model: `condition { action }`. Cực mạnh cho tabular data. `gawk` (GNU awk) thêm nhiều features.

**`xargs` và parallel execution:** `xargs` convert stdin thành arguments. `-P N` chạy N processes song song — cực kỳ hữu ích khi cần process nhiều file. `-n 1` mỗi lần một argument.

---

## Các lệnh / Cú pháp chính

| Lệnh | Mô tả | Ví dụ |
|------|-------|-------|
| `cat file` | In nội dung file | `cat /etc/hosts` |
| `cat -n file` | In với số dòng | `cat -n script.sh` |
| `less file` | Xem file có thể scroll | `less /var/log/syslog` |
| `head -n 20 file` | 20 dòng đầu | `head -n 5 /etc/passwd` |
| `tail -n 20 file` | 20 dòng cuối | `tail -n 100 app.log` |
| `tail -f file` | Theo dõi file realtime | `tail -f /var/log/nginx/access.log` |
| `tail -F file` | Theo dõi, kể cả khi file rotate | `tail -F /var/log/syslog` |
| `grep "pattern" file` | Tìm dòng chứa pattern | `grep "ERROR" app.log` |
| `grep -r "pattern" dir/` | Tìm đệ quy | `grep -r "TODO" /src/` |
| `grep -i "pattern"` | Không phân biệt hoa/thường | `grep -i "error" log` |
| `grep -v "pattern"` | Đảo ngược — không chứa | `grep -v "DEBUG" app.log` |
| `grep -n "pattern"` | Hiện số dòng | `grep -n "def " script.py` |
| `grep -c "pattern"` | Đếm số dòng match | `grep -c "ERROR" app.log` |
| `grep -l "pattern"` | Chỉ in tên file | `grep -rl "TODO" /src/` |
| `grep -A 3 "pattern"` | In thêm 3 dòng sau match | `grep -A 5 "Exception" log` |
| `grep -B 3 "pattern"` | In thêm 3 dòng trước match | `grep -B 2 "ERROR" log` |
| `grep -C 3 "pattern"` | 3 dòng trước và sau | `grep -C 3 "crash" log` |
| `sed 's/old/new/g' file` | Thay thế toàn bộ | `sed 's/foo/bar/g' file.txt` |
| `sed -i 's/old/new/g' file` | Sửa in-place | `sed -i 's/localhost/0.0.0.0/g' config` |
| `sed -n '10,20p' file` | In dòng 10–20 | `sed -n '5,10p' file.txt` |
| `sed '/pattern/d' file` | Xóa dòng chứa pattern | `sed '/^#/d' config.txt` |
| `awk '{print $1}' file` | In cột đầu | `awk '{print $1}' /etc/passwd` |
| `awk -F: '{print $1}' file` | Dùng : làm delimiter | `awk -F: '{print $1,$3}' /etc/passwd` |
| `awk 'NR==5' file` | In dòng thứ 5 | `awk 'NR==5' file.txt` |
| `cut -d: -f1 file` | Cắt cột với delimiter | `cut -d: -f1 /etc/passwd` |
| `sort file` | Sắp xếp | `sort names.txt` |
| `sort -n file` | Sắp xếp số | `sort -n numbers.txt` |
| `sort -rn file` | Số, đảo ngược | `sort -rn scores.txt` |
| `sort -k2 file` | Sort theo cột 2 | `sort -k2 data.txt` |
| `sort -u file` | Sort + unique | `sort -u names.txt` |
| `uniq file` | Loại bỏ dòng liên tiếp trùng | `sort file \| uniq` |
| `uniq -c file` | Đếm số lần xuất hiện | `sort log \| uniq -c \| sort -rn` |
| `uniq -d file` | Chỉ dòng có duplicate | `sort names \| uniq -d` |
| `wc -l file` | Đếm số dòng | `wc -l /etc/passwd` |
| `wc -w file` | Đếm số từ | `wc -w document.txt` |
| `tr 'a-z' 'A-Z'` | Translate characters | `echo "hello" \| tr 'a-z' 'A-Z'` |
| `tr -d '\r'` | Xóa ký tự | `tr -d '\r' < windows.txt > unix.txt` |
| `xargs` | Convert stdin → arguments | `find . -name "*.log" \| xargs rm` |

---

## Ví dụ thực tế

```bash
# ===== cat, head, tail =====
cat /etc/passwd                      # xem toàn bộ
cat file1.txt file2.txt > combined   # ghép file
head -n 5 /etc/passwd                # 5 dòng đầu
tail -n 20 /var/log/syslog           # 20 dòng cuối
tail -f /var/log/nginx/access.log    # theo dõi log realtime (Ctrl+C để dừng)
tail -F /var/log/app.log             # theo dõi kể cả khi logrotate tạo file mới

# ===== grep =====
# Tìm cơ bản
grep "ERROR" /var/log/app.log
grep -i "error\|warning" /var/log/app.log    # -i: ignore case, \|: OR trong BRE

# Regex
grep "^user" /etc/passwd             # bắt đầu bằng "user"
grep "\.py$" filelist.txt            # kết thúc bằng .py
grep "[0-9]\{3\}" phone_list.txt     # chứa ít nhất 3 chữ số liên tiếp
grep -E "[0-9]{3}-[0-9]{4}" phones   # ERE: không cần escape {}

# Tìm trong code
grep -rn "TODO\|FIXME\|HACK" /src/ --include="*.py"
grep -rl "password" /etc/ 2>/dev/null   # tìm file chứa "password"

# Context lines — hữu ích khi debug
grep -C 5 "NullPointerException" app.log | tail -30

# Đếm errors theo loại
grep -oE "ERROR [A-Z_]+" app.log | sort | uniq -c | sort -rn

# ===== sed =====
# Thay thế cơ bản
sed 's/foo/bar/' file.txt            # chỉ lần đầu trên mỗi dòng
sed 's/foo/bar/g' file.txt           # tất cả occurrences
sed 's/foo/bar/gi' file.txt          # case-insensitive, global

# In-place edit với backup
sed -i.bak 's/127.0.0.1/0.0.0.0/g' config.yaml
# Tạo config.yaml.bak và sửa config.yaml

# Xóa dòng
sed '/^#/d' config.txt               # xóa dòng comment
sed '/^$/d' file.txt                 # xóa dòng trống
sed '5d' file.txt                    # xóa dòng thứ 5
sed '5,10d' file.txt                 # xóa dòng 5-10

# In dòng cụ thể
sed -n '10,20p' file.txt             # in dòng 10-20
sed -n '/START/,/END/p' file.txt     # in từ START đến END

# Thêm dòng
sed '5i\NEW LINE' file.txt           # chèn trước dòng 5
sed '5a\NEW LINE' file.txt           # thêm sau dòng 5
sed '$a\LAST LINE' file.txt          # thêm dòng cuối

# Đổi tên hàng loạt (nội dung file)
sed -i "s/v1\.0/v2\.0/g" *.yaml

# ===== awk =====
# Cơ bản
awk '{print $1}' file.txt            # in cột đầu (space là delimiter)
awk -F: '{print $1, $3}' /etc/passwd # user và UID
awk -F, '{print $2}' data.csv        # CSV cột thứ 2

# Điều kiện
awk '$3 > 1000' /etc/passwd          # user với UID > 1000
awk '/ERROR/ {print $0}' app.log     # dòng chứa ERROR (giống grep)
awk 'NR>=10 && NR<=20' file.txt      # dòng 10-20

# Tính toán
awk '{sum += $1} END {print "Total:", sum}' numbers.txt
awk '{sum += $5; count++} END {print "Avg:", sum/count}' data.txt

# BEGIN/END blocks
awk 'BEGIN {print "=== Report ==="} 
     /ERROR/ {count++} 
     END {print "Total errors:", count}' app.log

# Nhiều delimiters và format output
awk -F: 'NR>1 {printf "User: %-15s UID: %d\n", $1, $3}' /etc/passwd

# Field separator trong output
awk -F: 'BEGIN {OFS=","} {print $1, $3, $6}' /etc/passwd > output.csv

# One-liner hữu ích: tính tổng cột 3 khi cột 1 = "SALE"
awk '$1 == "SALE" {total += $3} END {print total}' sales.txt

# ===== cut =====
cut -d: -f1 /etc/passwd              # chỉ username
cut -d: -f1,3 /etc/passwd            # username và UID
cut -c1-10 file.txt                  # ký tự 1-10 của mỗi dòng
cut -d',' -f2- data.csv              # từ cột 2 trở đi

# ===== sort & uniq =====
sort -t: -k3 -n /etc/passwd          # sort theo UID (cột 3, phân cách :)
sort -k2,2 -k1,1 data.txt            # sort theo cột 2, tie-break theo cột 1
sort -u names.txt                    # sort unique

# Pattern hay: đếm tần suất
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20
# Top 20 IP addresses trong access log

# ===== tr =====
echo "Hello World" | tr 'a-z' 'A-Z'  # HELLO WORLD
echo "hello" | tr -d 'l'             # heo — xóa ký tự
tr '\n' ',' < list.txt               # thay newline bằng comma
cat file.txt | tr -s ' '             # squeeze multiple spaces thành 1

# ===== wc =====
wc -l /etc/passwd                    # số dòng
wc -w essay.txt                      # số từ
find /src -name "*.py" | xargs wc -l | tail -1  # tổng dòng code Python

# ===== xargs =====
find . -name "*.log" | xargs rm -f              # xóa tất cả .log
find . -name "*.py"  | xargs grep -l "TODO"     # file .py có TODO
echo "file1.txt file2.txt" | xargs -n1 cat      # mỗi lần một file
find . -name "*.jpg" | xargs -P4 -I{} convert {} {}.png  # parallel convert
```

---

## Kết hợp lệnh nâng cao (Pipes & Patterns)

```bash
# ===== Log analysis =====
# Tìm 10 IP tấn công nhiều nhất
grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3)}' \
  | sort | uniq -c | sort -rn | head -10

# Xem error rate theo giờ
grep "ERROR" app.log \
  | awk '{print $1, substr($2,1,2)}' \
  | sort | uniq -c

# Lọc response code 5xx từ nginx access log
awk '$9 ~ /^5/' /var/log/nginx/access.log \
  | awk '{print $9}' | sort | uniq -c | sort -rn

# ===== Data processing =====
# Extract emails từ file
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' contacts.txt | sort -u

# Extract IP addresses
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' logfile | sort -u

# CSV: tính tổng cột price (cột 3) khi status = "sold" (cột 2)
awk -F, '$2 == "sold" {sum += $3} END {printf "Total: $%.2f\n", sum}' sales.csv

# Chuyển CSV thành SQL INSERT
awk -F, 'NR>1 {printf "INSERT INTO table VALUES (\"%s\", \"%s\", %s);\n", $1, $2, $3}' data.csv

# ===== File processing =====
# Thay đổi extension hàng loạt trong file
sed -i 's/\.jpeg/.jpg/g' filelist.txt

# Tạo danh sách file không trùng từ nhiều log
cat *.log | grep "UPLOAD" | awk '{print $5}' | sort -u

# Kiểm tra config file có syntax lạ
grep -Pn "[^\x00-\x7F]" config.yaml   # tìm non-ASCII characters

# Monitor log và alert
tail -F /var/log/app.log | grep --line-buffered "CRITICAL" | while read line; do
  echo "ALERT: $line" | mail -s "Critical Error" admin@example.com
done
```

---

## Lỗi thường gặp (Common Pitfalls)

**1. `grep` không tìm thấy vì regex đặc biệt chưa escape**
```bash
grep "192.168.1.1" file        # . match bất kỳ ký tự!
grep "192\.168\.1\.1" file     # ĐÚNG: escape dấu chấm
grep -F "192.168.1.1" file     # -F: fixed string, không dùng regex
```

**2. `sed -i` không backup trên macOS vs Linux**
```bash
sed -i 's/foo/bar/g' file      # Linux: OK
sed -i '' 's/foo/bar/g' file   # macOS: cần '' sau -i
# Portable:
sed -i.bak 's/foo/bar/g' file  # tạo .bak, hoạt động trên cả hai
```

**3. `awk` print vs printf**
```bash
awk '{print $1, $2}' file      # tự thêm newline, dùng OFS cho separator
awk '{printf "%s\t%s\n", $1, $2}' file  # control format hoàn toàn
```

**4. `sort | uniq` vs `sort -u`**
```bash
sort file | uniq    # đúng: uniq CHỈ xóa dòng LIÊN TIẾP trùng nhau
sort -u file        # sort kết hợp unique — thường nhanh hơn
uniq file           # SAI nếu không sort trước — chỉ xóa adjacent duplicates
```

**5. `tail -f` vs `tail -F`**
```bash
tail -f /var/log/app.log   # follow inode: dừng khi file bị rotate/xóa
tail -F /var/log/app.log   # follow tên file: tự mở file mới khi rotate
# Với log rotation, luôn dùng -F
```

**6. Dùng `cat | grep` thay vì `grep file`**
```bash
cat file.txt | grep "pattern"   # UUOC (Useless Use Of Cat)
grep "pattern" file.txt         # ĐÚNG: trực tiếp và nhanh hơn
```

**7. `awk` NF và $NF**
```bash
awk '{print NF}' file      # số lượng fields trong dòng
awk '{print $NF}' file     # field cuối cùng
awk '{print $(NF-1)}' file # field áp cuối
```

---

## Câu hỏi phỏng vấn hay gặp

**Q1: Sự khác biệt giữa `grep`, `egrep`, `fgrep`?**
> `grep` dùng BRE (Basic Regular Expressions) — `+`, `?`, `|` cần escape. `egrep` (= `grep -E`) dùng ERE — không cần escape. `fgrep` (= `grep -F`) tìm literal string, không interpret regex — nhanh nhất. Trong script hiện đại, dùng `grep -E` và `grep -F` thay vì `egrep`/`fgrep`.

**Q2: Giải thích `awk 'BEGIN{} /pattern/{} END{}'`?**
> `BEGIN{}` chạy một lần trước khi đọc bất kỳ input nào (init variables, print header). Pattern/action `{}`chạy với mỗi dòng match pattern. `END{}` chạy một lần sau khi đọc hết input (tính total, print summary). Nếu không có pattern, action chạy với mọi dòng.

**Q3: Dùng sed để xóa comment và dòng trống từ config file?**
```bash
sed -e '/^[[:space:]]*#/d' -e '/^[[:space:]]*$/d' config.txt
# Hoặc gộp:
sed '/^\s*[#;]/d; /^\s*$/d' config.txt
```

**Q4: `cut` vs `awk` cho cắt cột — khi nào dùng cái nào?**
> `cut` đơn giản, nhanh cho delimiter fixed và cột cố định. `awk` mạnh hơn khi cần: nhiều điều kiện, tính toán, format output, xử lý whitespace variable (awk tự split trên whitespace), hoặc khi delimiter thay đổi. Với CSV có quoted fields, không nên dùng `cut`.

**Q5: Làm sao tìm dòng xuất hiện trong file A nhưng không có trong file B?**
```bash
comm -23 <(sort file_a) <(sort file_b)
# Hoặc:
grep -Fxvf file_b file_a
# -F: fixed string, -x: whole line, -v: invert, -f: patterns from file
```

**Q6: Giải thích `xargs -P` và khi nào dùng?**
> `-P N` chạy tối đa N processes song song. Hữu ích khi mỗi lệnh tốn thời gian và independent (không shared state). Ví dụ: `find . -name "*.jpg" | xargs -P8 -I{} convert {} {}_thumb.jpg` — convert 8 ảnh cùng lúc. Tuy nhiên output có thể interleaved — cẩn thận khi cần ordered output.
