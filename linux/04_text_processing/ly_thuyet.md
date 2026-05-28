# Linux Text Processing — Xử lý Văn bản

---

## Giải thích cho người mới

Trong Linux, hầu hết mọi thứ là **text file** (config, logs, code...). Vì vậy Linux có rất nhiều công cụ xử lý text mạnh mẽ. Thay vì mở Word/Notepad để đọc log, bạn dùng terminal để:
- Tìm kiếm (`grep`) — như Ctrl+F trong notepad nhưng mạnh hơn nhiều
- Thay thế (`sed`) — như Find & Replace nhưng có thể tự động hóa
- Tính toán theo cột (`awk`) — như Excel nhưng từ terminal

---

## Giải thích nâng cao

**Pipeline philosophy**: mỗi công cụ làm **một việc tốt**, kết hợp qua pipe (`|`). Đây là Unix philosophy — nhỏ, chuyên biệt, có thể kết hợp.

**Regular Expressions (Regex)**: ngôn ngữ mô tả pattern. `grep`, `sed`, `awk` đều dùng regex. BRE (Basic RE) và ERE (Extended RE, dùng `-E`) có syntax hơi khác nhau.

---

## BẢNG LỆNH THỰC HÀNH

### grep — Global Regular Expression Print
```bash
# Cú pháp: grep [options] pattern [file...]

grep "error" file.log          # tìm dòng chứa "error"
grep "error" *.log             # tìm trong nhiều file
grep -r "TODO" .               # tìm recursive trong thư mục
grep -r "TODO" . --include="*.py"  # chỉ trong file .py

grep -i "error" file.log       # case-insensitive
grep -v "debug" file.log       # inverse — dòng KHÔNG chứa "debug"
grep -n "error" file.log       # hiện số dòng
grep -c "error" file.log       # đếm số dòng match
grep -l "TODO" *.py            # chỉ in tên file có match
grep -L "TODO" *.py            # file KHÔNG có match

grep -A 3 "ERROR" file.log     # 3 dòng AFTER match
grep -B 3 "ERROR" file.log     # 3 dòng BEFORE match
grep -C 3 "ERROR" file.log     # 3 dòng context (before + after)

grep -E "error|warning" file.log      # Extended RE: OR
grep -E "^ERROR:" file.log            # bắt đầu dòng bằng ERROR:
grep -E "[0-9]{3}-[0-9]{4}" file      # số điện thoại pattern
grep -P "\d{4}-\d{2}-\d{2}" file      # Perl regex (PCRE)

grep -w "log" file.txt         # whole word (không match "logging")
grep -x "exact line" file      # toàn bộ dòng phải match
grep -o "pattern" file         # chỉ in phần match (không cả dòng)
grep -m 5 "error" file.log     # dừng sau 5 match

# Với pipeline
cat /var/log/auth.log | grep "Failed" | grep -v "test"
ps aux | grep "nginx" | grep -v grep
```

**Regex cơ bản trong grep:**
```
.       bất kỳ ký tự nào
*       0 hoặc nhiều ký tự trước
+       1 hoặc nhiều (cần -E)
?       0 hoặc 1 (cần -E)
^       đầu dòng
$       cuối dòng
[abc]   a hoặc b hoặc c
[^abc]  không phải a, b, c
[a-z]   a đến z
\w      word character (= [a-zA-Z0-9_])
\d      digit (cần -P)
\s      whitespace
```

### sed — Stream EDitor
```bash
# Cú pháp: sed [options] 'command' [file]

# Substitution (thay thế)
sed 's/old/new/' file.txt         # thay lần đầu tiên trên mỗi dòng
sed 's/old/new/g' file.txt        # thay tất cả (global)
sed 's/old/new/2' file.txt        # thay lần thứ 2
sed 's/old/new/gi' file.txt       # case-insensitive + global
sed -i 's/old/new/g' file.txt     # edit in-place (sửa file gốc)
sed -i.bak 's/old/new/g' file.txt # edit in-place + backup (.bak)
sed -E 's/[0-9]+/NUM/g' file.txt  # Extended RE

# Xóa dòng
sed '/pattern/d' file.txt         # xóa dòng chứa pattern
sed '/^$/d' file.txt              # xóa dòng trống
sed '/^#/d' file.txt              # xóa comment lines
sed '5d' file.txt                 # xóa dòng 5
sed '5,10d' file.txt              # xóa dòng 5 đến 10
sed '$d' file.txt                 # xóa dòng cuối

# In dòng
sed -n '5p' file.txt              # chỉ in dòng 5
sed -n '5,10p' file.txt           # in dòng 5 đến 10
sed -n '/pattern/p' file.txt      # in dòng match pattern

# Thêm dòng
sed '5a\new line' file.txt        # thêm sau dòng 5
sed '5i\new line' file.txt        # thêm trước dòng 5
sed '/pattern/a\new line' file    # thêm sau dòng match pattern

# Nhiều lệnh
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt
sed 's/foo/bar/g; s/baz/qux/g' file.txt

# Ứng dụng thực tế
sed 's/http:/https:/g' config.txt       # đổi http thành https
sed '/^#/d; /^$/d' config.txt           # bỏ comment và dòng trống
sed 's/\s*$//g' file.txt               # bỏ trailing whitespace
sed -n '100,200p' largefile.txt         # xem dòng 100-200
```

### awk — Xử lý file theo cột/record
```bash
# Cú pháp: awk [options] 'program' [file]
# Mỗi dòng là 1 record, mỗi từ (space-separated) là 1 field
# $0 = toàn dòng, $1 = cột 1, $2 = cột 2, $NF = cột cuối, NR = số dòng

# In các cột
awk '{print $1}' file.txt          # in cột 1
awk '{print $1, $3}' file.txt      # in cột 1 và 3
awk '{print $NF}' file.txt         # in cột cuối
awk '{print NR, $0}' file.txt      # thêm số dòng

# Delimiter tùy chỉnh
awk -F: '{print $1, $3}' /etc/passwd     # : là delimiter
awk -F',' '{print $2}' data.csv          # CSV
awk 'BEGIN{FS=":"; OFS=","} {print $1,$3}' /etc/passwd  # đổi delimiter

# Điều kiện
awk '$3 > 100' file.txt            # in dòng nếu cột 3 > 100
awk '/pattern/ {print $2}' file    # in cột 2 của dòng match pattern
awk 'NR==5' file.txt               # in dòng 5
awk 'NR>=5 && NR<=10' file.txt     # in dòng 5-10
awk '$1=="ERROR" {print}' log.txt  # cột 1 bằng "ERROR"
awk 'length($0) > 80' file.txt     # dòng dài hơn 80 ký tự

# Tính toán
awk '{sum += $3} END {print sum}' data.txt      # tổng cột 3
awk '{sum += $3; count++} END {print sum/count}' data.txt  # trung bình
awk 'END {print NR}' file.txt      # đếm số dòng

# BEGIN và END blocks
awk 'BEGIN {print "Start"} {print $1} END {print "End"}' file.txt

# Ứng dụng thực tế
ps aux | awk '{print $1, $2, $3}'                  # user, pid, cpu
df -h | awk 'NR>1 {print $5, $6}'                  # disk usage %
awk -F: '$3 >= 1000 {print $1}' /etc/passwd         # regular users (uid >= 1000)
awk '{print $7}' access.log | sort | uniq -c | sort -rn | head -10  # top URLs
cat /proc/cpuinfo | awk '/^model name/ {print; exit}'  # CPU model
```

### sort — Sắp xếp
```bash
sort file.txt                  # sắp xếp alphabetically
sort -r file.txt               # reverse
sort -n file.txt               # numeric sort (1, 2, 10 thay vì 1, 10, 2)
sort -rn file.txt              # numeric reverse
sort -k2 file.txt              # sort theo cột 2
sort -k2 -n file.txt           # sort theo cột 2, numeric
sort -k2,2 -k1,1 file.txt      # sort theo cột 2, nếu bằng thì theo cột 1
sort -t: -k3 -n /etc/passwd    # sort theo cột 3, delimiter :
sort -u file.txt               # sort + loại bỏ duplicate (= sort | uniq)
sort -h file.txt               # human-readable sort (10K, 1M, 2G)
du -sh * | sort -rh            # files by size, lớn nhất đầu
```

### uniq — Loại bỏ / Đếm duplicate (phải sort trước!)
```bash
sort file.txt | uniq           # loại bỏ duplicate lines
sort file.txt | uniq -c        # đếm số lần xuất hiện
sort file.txt | uniq -d        # chỉ in lines có duplicate
sort file.txt | uniq -u        # chỉ in lines KHÔNG có duplicate
sort file.txt | uniq -c | sort -rn  # sort by frequency
sort file.txt | uniq -i        # case-insensitive
```

### cut — Cắt cột/ký tự
```bash
cut -d: -f1 /etc/passwd        # cột 1, delimiter :
cut -d: -f1,3 /etc/passwd      # cột 1 và 3
cut -d, -f2 data.csv           # cột 2 trong CSV
cut -c1-5 file.txt             # ký tự 1 đến 5 của mỗi dòng
cut -c5- file.txt              # từ ký tự 5 đến cuối
```

### tr — Translate/Delete ký tự
```bash
tr 'a-z' 'A-Z' < file.txt     # chuyển lowercase → uppercase
tr 'A-Z' 'a-z' < file.txt     # chuyển uppercase → lowercase
tr -d '\n' < file.txt          # xóa newlines
tr -d ' ' < file.txt           # xóa spaces
tr -s ' ' < file.txt           # replace multiple spaces thành 1
tr ':' ',' < /etc/passwd       # thay : bằng ,
tr -cd '[:print:]' < file      # xóa non-printable characters
echo "hello world" | tr ' ' '\n'  # mỗi từ 1 dòng
```

### paste — Merge files theo cột
```bash
paste file1.txt file2.txt      # merge 2 file theo cột (tab-separated)
paste -d, file1.txt file2.txt  # delimiter ,
paste -s file.txt              # merge tất cả dòng thành 1 dòng
```

### tee — Đọc stdin, ghi ra file VÀ stdout
```bash
command | tee output.txt               # ghi và tiếp tục pipeline
command | tee output.txt | wc -l       # save + đếm dòng
command | tee -a output.txt            # append mode
command | tee file1 file2              # ghi ra nhiều file
sudo command | tee /etc/config         # ghi file cần root qua sudo
```

### xargs — Build và execute từ stdin
```bash
find . -name "*.txt" | xargs wc -l         # đếm dòng tất cả .txt
find . -name "*.log" | xargs rm            # xóa tất cả .log
find . -name "*.py" | xargs grep "TODO"    # tìm TODO trong .py files
echo "file1 file2 file3" | xargs ls -la   # ls từng file
cat urls.txt | xargs curl -O              # download từng URL

xargs -n 1 command < file.txt  # chạy command với từng dòng
xargs -P 4 command < file.txt  # parallel với 4 processes
xargs -I {} command {} arg2    # {} là placeholder cho mỗi item
find . -name "*.log" | xargs -I {} mv {} /archive/
```

### diff — So sánh file
```bash
diff file1.txt file2.txt           # so sánh 2 file
diff -u file1.txt file2.txt        # unified format (dễ đọc hơn)
diff -r dir1/ dir2/                # so sánh 2 thư mục
diff -i file1 file2                # case-insensitive
diff -w file1 file2                # bỏ qua whitespace
diff -y file1 file2                # side-by-side
diff --color file1 file2           # với màu

# patch — áp dụng diff
diff -u old.txt new.txt > changes.patch
patch old.txt < changes.patch
```

### Pipeline thực tế
```bash
# Top 10 IP access nhiều nhất từ nginx log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# Đếm error codes từ log
grep " 5[0-9][0-9] " access.log | awk '{print $9}' | sort | uniq -c

# Tìm process nặng nhất
ps aux | sort -k3 -rn | head -10

# Xóa dòng trống và comment trong config
grep -v '^#' config.conf | grep -v '^$'

# Replace trong nhiều file
find . -name "*.conf" -exec sed -i 's/old/new/g' {} \;

# Đếm số dòng code Python
find . -name "*.py" | xargs wc -l | tail -1

# Xem log 5 phút gần nhất
awk -v d="$(date -d '5 minutes ago' '+%d/%b/%Y:%H:%M')" '$4 > "["d' access.log
```

---

## Khi nào dùng gì

| Tình huống | Lệnh |
|-----------|------|
| Tìm text trong file | `grep "pattern" file` |
| Tìm trong nhiều file | `grep -r "pattern" .` |
| Thay thế text | `sed 's/old/new/g'` |
| Xử lý cột/bảng | `awk` |
| Sort output | `sort -n` hoặc `sort -rh` |
| Đếm tần suất | `sort \| uniq -c \| sort -rn` |
| Cắt cột | `cut -d: -f1` |
| Đổi ký tự | `tr 'a-z' 'A-Z'` |
| Chạy lệnh với nhiều args | `xargs` |

---

## Lỗi thường gặp

```bash
# uniq không loại bỏ được duplicate
# → uniq chỉ loại bỏ consecutive duplicates, PHẢI sort trước
sort file.txt | uniq            # đúng

# sed -i trên macOS khác Linux
sed -i '' 's/old/new/g' file   # macOS cần '' sau -i
sed -i 's/old/new/g' file      # Linux OK

# awk: NF vs NR
NF = number of fields (cột)
NR = number of records (dòng/row)
$NF = giá trị cột cuối
NR == 5 = dòng thứ 5

# grep: dùng -E cho extended regex
grep "[0-9]+" file              # SALL: + không hoạt động trong BRE
grep -E "[0-9]+" file           # ĐÚNG: dùng -E
grep -P "\d+" file              # Perl regex
```

---

## Câu hỏi phỏng vấn hay gặp

- `grep -v` làm gì? Cho ví dụ.
- Giải thích `sort | uniq -c | sort -rn`.
- `sed 's/old/new/g'` vs `sed 's/old/new/'` — khác nhau thế nào?
- Làm thế nào để in cột 3 của file CSV bằng `awk`?
- `xargs` dùng để làm gì? Khi nào cần?
