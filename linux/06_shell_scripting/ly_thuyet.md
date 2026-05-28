# Linux Shell Scripting — Viết Script Bash

---

## Giải thích cho người mới

Shell script giống như **ghi lại các bước** bạn làm thủ công rồi cho máy tính tự chạy. Ví dụ: mỗi ngày bạn phải gõ 10 lệnh để backup file → viết script, mai chỉ cần chạy 1 lệnh, máy tự làm hết.

Script là file text thường, đuôi `.sh`, dòng đầu là `#!/bin/bash` (shebang — báo hệ thống dùng bash để chạy).

---

## Giải thích nâng cao

**Bash vs sh vs zsh**: `bash` (Bourne Again Shell) là chuẩn phổ biến nhất trên Linux. `sh` là POSIX shell, subset của bash. `zsh` thêm nhiều tính năng. Script portable nên dùng `#!/bin/sh` và tránh bash-specific syntax.

**Exit code**: mọi command trả về exit code (0 = success, non-zero = error). `$?` chứa exit code của lệnh trước. `set -e` làm script dừng khi có lỗi.

**Shell expansion**: bash expand nhiều thứ trước khi chạy: `$var`, `$(cmd)`, `${var}`, `*`, `~`. Hiểu expansion tránh được nhiều bug.

---

## BẢNG LỆNH THỰC HÀNH

### Cấu trúc file script

```bash
#!/bin/bash
# Shebang: báo dùng bash để chạy
# Dòng bắt đầu bằng # là comment

# Best practices ngay đầu script:
set -e          # exit ngay khi có lỗi (command trả về non-zero)
set -u          # lỗi khi dùng biến chưa khai báo
set -o pipefail # pipeline fail nếu bất kỳ command nào fail
# Hoặc gộp:
set -euo pipefail

# Tạo và chạy script:
touch script.sh
chmod +x script.sh
./script.sh

# Chạy không cần executable bit:
bash script.sh
sh script.sh

# Debug mode:
bash -x script.sh       # in từng lệnh trước khi chạy
bash -n script.sh       # check syntax, không chạy
set -x                  # bật debug trong script
set +x                  # tắt debug
```

---

### Biến (Variables)

```bash
# Khai báo và gán (KHÔNG có space quanh =)
name="Alice"
age=25
path="/home/alice"

# Đọc biến
echo $name
echo "$name"      # luôn quote biến để tránh word splitting
echo "${name}"    # dùng {} khi ghép với text

# Biến đặc biệt
echo $0           # tên script
echo $1 $2 $3     # arguments (positional parameters)
echo $@           # tất cả arguments (array)
echo $*           # tất cả arguments (string)
echo $#           # số lượng arguments
echo $$           # PID của shell hiện tại
echo $?           # exit code của lệnh trước
echo $!           # PID của background process gần nhất

# Readonly variable
readonly PI=3.14159
PI=3              # lỗi: readonly variable

# Unset variable
unset name

# Default value
echo "${name:-default}"          # nếu name chưa set/rỗng → in "default"
echo "${name:=default}"          # nếu name chưa set/rỗng → gán "default" cho name
echo "${name:?Error message}"    # nếu name chưa set/rỗng → lỗi + message
echo "${name:+other}"            # nếu name đã set → in "other"

# String operations
str="Hello, World!"
echo ${#str}              # 13 (độ dài string)
echo ${str:0:5}           # "Hello" (substring từ index 0, dài 5)
echo ${str:7}             # "World!" (từ index 7 đến cuối)
echo ${str/World/Linux}   # "Hello, Linux!" (replace lần đầu)
echo ${str//l/L}          # "HeLLo, WorLd!" (replace all)
echo ${str,,}             # "hello, world!" (lowercase)
echo ${str^^}             # "HELLO, WORLD!" (uppercase)

# Trim prefix/suffix
file="backup_2024.tar.gz"
echo ${file#*.}           # "2024.tar.gz" (bỏ prefix đến . đầu tiên)
echo ${file##*.}          # "gz" (bỏ prefix đến . cuối cùng)
echo ${file%.tar.gz}      # "backup_2024" (bỏ suffix .tar.gz)
echo ${file%.*}           # "backup_2024.tar" (bỏ suffix sau . cuối)

# Arrays
fruits=("apple" "banana" "cherry")
echo ${fruits[0]}         # "apple"
echo ${fruits[@]}         # tất cả elements
echo ${#fruits[@]}        # 3 (số elements)
fruits+=("date")          # thêm element
unset fruits[1]           # xóa element index 1
echo ${!fruits[@]}        # 0 2 3 (indices còn lại)

# Associative array (bash 4+)
declare -A config
config["host"]="localhost"
config["port"]="5432"
echo ${config["host"]}
echo ${!config[@]}        # tất cả keys
echo ${config[@]}         # tất cả values
```

---

### Input / Output

```bash
# Đọc input từ user
read name                    # đọc vào biến name
read -p "Enter name: " name  # với prompt
read -s -p "Password: " pwd  # -s: silent (không echo)
read -t 5 name               # timeout 5 giây
read -n 1 key                # đọc đúng 1 ký tự
read -a arr                  # đọc thành array

# Đọc từng dòng của file
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

# Đọc từ pipe
cat file.txt | while IFS= read -r line; do
    echo "$line"
done

# echo vs printf
echo "Hello $name"           # thêm newline tự động
echo -n "No newline"         # không thêm newline
echo -e "Tab:\there"         # interpret escape sequences

printf "Name: %s, Age: %d\n" "$name" "$age"    # format string
printf "%-20s %5d\n" "item" 42                  # align: left 20 chars, right 5 chars
printf "%08.2f\n" 3.14                          # 00003.14

# Here-document
cat << EOF
This is line 1
Name: $name
EOF

cat << 'EOF'                 # single-quote: không expand variables
This is $name (literal)
EOF

# Here-string
grep "pattern" <<< "search in this string"
```

---

### Điều kiện (Conditionals)

```bash
# if-elif-else
if [ condition ]; then
    commands
elif [ other_condition ]; then
    commands
else
    commands
fi

# Test operators — [ ] là POSIX, [[ ]] là bash (tốt hơn)
# String comparisons
[ "$a" = "$b" ]    # bằng (POSIX dùng =)
[[ "$a" == "$b" ]] # bằng (bash, hỗ trợ glob)
[[ "$a" != "$b" ]] # không bằng
[ -z "$a" ]        # chuỗi rỗng
[ -n "$a" ]        # chuỗi không rỗng
[[ "$a" =~ ^[0-9]+$ ]]  # regex match (bash only)

# Numeric comparisons (dùng -eq, -ne, -lt, -le, -gt, -ge)
[ "$a" -eq "$b" ]  # equal
[ "$a" -ne "$b" ]  # not equal
[ "$a" -lt "$b" ]  # less than
[ "$a" -le "$b" ]  # less or equal
[ "$a" -gt "$b" ]  # greater than
[ "$a" -ge "$b" ]  # greater or equal

# File tests
[ -f file ]        # tồn tại và là file thường
[ -d dir ]         # tồn tại và là directory
[ -e path ]        # tồn tại (bất kỳ loại)
[ -r file ]        # có thể đọc
[ -w file ]        # có thể ghi
[ -x file ]        # có thể execute
[ -s file ]        # tồn tại và không rỗng
[ -L link ]        # là symbolic link
[ file1 -nt file2 ]  # file1 mới hơn file2
[ file1 -ot file2 ]  # file1 cũ hơn file2

# Logic operators
[ "$a" -gt 0 ] && [ "$a" -lt 10 ]   # AND (POSIX)
[[ "$a" -gt 0 && "$a" -lt 10 ]]     # AND (bash)
[ "$a" -gt 10 ] || [ "$a" -lt 0 ]   # OR
[ ! -f file ]                         # NOT

# Ví dụ thực tế
if [ -z "$1" ]; then
    echo "Error: No argument provided"
    exit 1
fi

if [[ "$USER" == "root" ]]; then
    echo "Running as root"
fi

# Case statement
case "$1" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart|reload)          # multiple patterns
        echo "Restarting..."
        ;;
    *)                        # default
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

---

### Vòng lặp (Loops)

```bash
# for loop — iterate over list
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

for file in *.txt; do
    echo "Processing: $file"
done

for item in "${array[@]}"; do
    echo "$item"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "i = $i"
done

# for với seq
for i in $(seq 1 10); do echo $i; done
for i in $(seq 0 2 10); do echo $i; done   # 0 2 4 6 8 10

# while loop
count=0
while [ $count -lt 5 ]; do
    echo "Count: $count"
    ((count++))
done

# Đọc file với while
while IFS= read -r line; do
    echo "Processing: $line"
done < input.txt

# until loop (ngược while — chạy cho đến khi điều kiện đúng)
until ping -c1 google.com &>/dev/null; do
    echo "Waiting for network..."
    sleep 2
done
echo "Network is up!"

# break và continue
for i in {1..10}; do
    [ $i -eq 5 ] && break     # dừng loop
    [ $i -eq 3 ] && continue  # skip iteration này
    echo $i
done

# Loop với pipe
cat urls.txt | while read url; do
    curl -s "$url" > /dev/null && echo "OK: $url" || echo "FAIL: $url"
done
```

---

### Functions

```bash
# Khai báo function
greet() {
    local name="$1"          # local: biến chỉ tồn tại trong function
    echo "Hello, $name!"
}

# Gọi function
greet "Alice"

# Return value
add() {
    local result=$(( $1 + $2 ))
    echo $result             # "return" giá trị qua stdout
}
sum=$(add 3 5)
echo "Sum: $sum"

# Return code (exit status)
is_file() {
    [ -f "$1" ]              # return 0 nếu là file, 1 nếu không
}

if is_file "/etc/passwd"; then
    echo "File exists"
fi

# Function với local scope
counter=0
increment() {
    local counter=10         # local — không ảnh hưởng global
    echo "Local: $counter"
}
increment
echo "Global: $counter"     # vẫn là 0

# Recursive function
factorial() {
    local n=$1
    if [ $n -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $((n-1)))
        echo $((n * prev))
    fi
}
factorial 5    # 120

# Function library — source từ file khác
# lib.sh:
LOG_FILE="/var/log/myapp.log"
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# main.sh:
source lib.sh       # hoặc: . lib.sh
log "Script started"
```

---

### Arithmetic

```bash
# $(( )) — arithmetic expansion
a=10
b=3
echo $((a + b))    # 13
echo $((a - b))    # 7
echo $((a * b))    # 30
echo $((a / b))    # 3 (integer division)
echo $((a % b))    # 1 (modulo)
echo $((a ** b))   # 1000 (power)

# Increment/decrement
((count++))
((count--))
((count += 5))
((count *= 2))

# let (cũ hơn, ít dùng)
let "a = 5 + 3"

# bc — floating point
echo "scale=2; 10/3" | bc    # 3.33
echo "scale=4; sqrt(2)" | bc # 1.4142

# Kiểm tra số
if [[ "$1" =~ ^[0-9]+$ ]]; then
    echo "$1 is a number"
fi
```

---

### Error Handling

```bash
# Exit codes
exit 0      # success
exit 1      # general error
exit 2      # misuse of command

# Kiểm tra lỗi
if ! command; then
    echo "Command failed"
    exit 1
fi

# || operator
mkdir /tmp/test || { echo "mkdir failed"; exit 1; }

# Trap — xử lý signals và cleanup
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/lockfile
}
trap cleanup EXIT         # chạy khi script exit (dù success hay error)
trap cleanup INT TERM     # chạy khi Ctrl+C hoặc kill

# Trap ERR — chạy khi có lỗi
on_error() {
    echo "Error on line $1"
    exit 1
}
trap 'on_error $LINENO' ERR

# set -e + trap ERR kết hợp
set -e
trap 'echo "Error at line $LINENO"; exit 1' ERR

# Error handling function
die() {
    echo "ERROR: $1" >&2    # ghi ra stderr
    exit "${2:-1}"           # exit code, mặc định 1
}

[ -f "$config" ] || die "Config file not found: $config" 2
```

---

### Script thực tế hoàn chỉnh

```bash
#!/bin/bash
set -euo pipefail

# ── Constants
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
LOG_FILE="/var/log/backup.log"
BACKUP_DIR="/backup"
SOURCE_DIR="/home"
DATE=$(date '+%Y%m%d_%H%M%S')

# ── Logging
log() { echo "[$(date '+%H:%M:%S')] $*" | tee -a "$LOG_FILE"; }
log_err() { echo "[ERROR] $*" >&2; }

# ── Check dependencies
for cmd in tar gzip rsync; do
    command -v "$cmd" &>/dev/null || { log_err "$cmd not found"; exit 1; }
done

# ── Argument parsing
usage() {
    echo "Usage: $0 [-d destination] [-s source] [-h]"
    echo "  -d  Backup destination (default: $BACKUP_DIR)"
    echo "  -s  Source directory (default: $SOURCE_DIR)"
    echo "  -h  Show this help"
    exit 0
}

while getopts "d:s:h" opt; do
    case $opt in
        d) BACKUP_DIR="$OPTARG" ;;
        s) SOURCE_DIR="$OPTARG" ;;
        h) usage ;;
        *) usage ;;
    esac
done

# ── Validate
[ -d "$SOURCE_DIR" ] || { log_err "Source not found: $SOURCE_DIR"; exit 1; }
mkdir -p "$BACKUP_DIR"

# ── Main
BACKUP_FILE="$BACKUP_DIR/backup_${DATE}.tar.gz"

log "Starting backup: $SOURCE_DIR → $BACKUP_FILE"
tar -czf "$BACKUP_FILE" "$SOURCE_DIR" 2>/dev/null

SIZE=$(du -sh "$BACKUP_FILE" | cut -f1)
log "Backup complete: $BACKUP_FILE ($SIZE)"

# Giữ 7 bản backup gần nhất
ls -t "$BACKUP_DIR"/backup_*.tar.gz | tail -n +8 | xargs -r rm -v
log "Old backups cleaned"
```

---

## Khi nào dùng gì

| Tình huống | Dùng |
|-----------|------|
| Kiểm tra string | `[[ "$var" == "value" ]]` |
| Kiểm tra số | `[ "$n" -gt 0 ]` hoặc `(( n > 0 ))` |
| Đọc file từng dòng | `while IFS= read -r line; do` |
| Loop có index | `for ((i=0; i<n; i++))` |
| Giá trị mặc định | `${var:-default}` |
| Chạy khi lỗi | `command \|\| { handle_error; exit 1; }` |
| Cleanup khi exit | `trap cleanup EXIT` |
| Parse arguments | `getopts` |

---

## Lỗi thường gặp

```bash
# ❌ Thiếu quote → word splitting
if [ $name = "Alice" ]       # lỗi nếu name có space
if [ "$name" = "Alice" ]     # đúng

# ❌ Dùng == trong [ ] (POSIX)
if [ "$a" == "$b" ]          # chỉ đúng trên bash
if [ "$a" = "$b" ]           # POSIX đúng
if [[ "$a" == "$b" ]]        # bash đúng (hỗ trợ ==)

# ❌ Thiếu set -e
rm important_file
cp new_file /important/path  # chạy dù rm bị lỗi

# ❌ Space quanh = khi gán
name = "Alice"               # lỗi: bash hiểu là command "name"
name="Alice"                 # đúng

# ❌ Dùng $() vs backtick
result=`command`             # cũ, khó nested
result=$(command)            # đúng, có thể nested

# ❌ Không có IFS= và -r khi đọc file
while read line; do ...      # bỏ leading/trailing whitespace, \ bị xử lý
while IFS= read -r line; do  # đúng: giữ nguyên toàn bộ line

# ❌ Dùng ls trong script
for f in $(ls *.txt)         # nguy hiểm nếu filename có space
for f in *.txt               # đúng: shell globbing an toàn

# ❌ Không check biến rỗng trước rm -rf
rm -rf "$dir"                # nếu $dir rỗng → rm -rf "" → xóa cwd!
[ -n "$dir" ] && rm -rf "$dir"  # an toàn hơn
```

---

## Câu hỏi phỏng vấn hay gặp

- `$@` vs `$*` — khác nhau thế nào khi có quotes?
- `set -euo pipefail` có nghĩa gì?
- Tại sao phải `IFS= read -r` khi đọc file?
- Sự khác biệt giữa `[ ]` và `[[ ]]`?
- `local` trong function có tác dụng gì?
- Làm thế nào để parse command line arguments?
- `trap` dùng để làm gì? Cho ví dụ.
- Tại sao luôn phải quote biến trong bash?
