# Trắc nghiệm — Shell Scripting

> **Tổng số câu:** 22 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–7)

**Câu 1:** Dòng `#!/bin/bash` ở đầu script có tác dụng gì?

- A. Comment — không có tác dụng gì
- B. Shebang — báo kernel dùng `/bin/bash` để thực thi file này
- C. Khai báo biến môi trường BASH
- D. Import thư viện bash

> **Đáp án: B** — Shebang (shebang = `#!`): kernel đọc dòng này và dùng interpreter được chỉ định để chạy script. `#!/usr/bin/env python3` dùng python3, `#!/bin/sh` dùng POSIX sh.

---

**Câu 2:** Lệnh nào cho phép chạy `script.sh`?

- A. `bash script.sh` (không cần permission)
- B. `chmod +x script.sh` rồi `./script.sh`
- C. Cả A và B đều đúng
- D. Chỉ root mới chạy được script

> **Đáp án: C** — `bash script.sh` chạy trực tiếp bằng bash, không cần execute bit. `./script.sh` cần execute bit (`chmod +x`). Cả hai đều hợp lệ.

---

**Câu 3:** Biến `$?` chứa gì?

- A. PID của script hiện tại
- B. Số lượng arguments
- C. Exit code (return code) của lệnh vừa chạy xong
- D. Tên script

> **Đáp án: C** — `$?` = exit status của lệnh trước. 0 = success, 1-255 = error. Dùng để kiểm tra lệnh có thành công không: `grep ... ; echo $?`.

---

**Câu 4:** Đoạn code sau có lỗi gì?

```bash
name = "Alice"
echo $name
```

- A. Thiếu dấu `;` cuối dòng
- B. Có space quanh `=` — bash nghĩ `name` là command với argument `=` và `"Alice"`
- C. Không thể gán string có space
- D. Phải dùng `let name = "Alice"`

> **Đáp án: B** — Trong bash, gán biến KHÔNG có space: `name="Alice"`. Nếu có space, bash parse `name` là command, `=` và `"Alice"` là arguments → lỗi "command not found".

---

**Câu 5:** `$1`, `$2`, `$#` lần lượt là gì?

- A. Biến toán học, biến 2, hash
- B. Argument thứ 1, argument thứ 2, tổng số arguments
- C. PID, PPID, process count
- D. Stdin, stdout, stderr

> **Đáp án: B** — Positional parameters: `$0`=tên script, `$1`=arg1, `$2`=arg2, `$#`=số args, `$@`=tất cả args (array), `$*`=tất cả args (string).

---

**Câu 6:** Lệnh `read -p "Enter name: " username` làm gì?

- A. Đọc file "Enter name:"
- B. Hiện prompt "Enter name:" và chờ user nhập, lưu vào biến `username`
- C. Set biến username thành "Enter name:"
- D. Đọc username từ /etc/passwd

> **Đáp án: B** — `read -p "prompt" var`: `-p` là prompt string, hiển thị trước khi chờ nhập. Input được lưu vào `var`. `-s` để ẩn input (password), `-t 5` để timeout 5 giây.

---

**Câu 7:** Cú pháp đúng để so sánh hai số nguyên trong bash?

- A. `if [ $a == $b ]`
- B. `if [ $a -eq $b ]`
- C. `if [ $a = $b ]`
- D. `if (( $a === $b ))`

> **Đáp án: B** — Với `[ ]`, so sánh số dùng: `-eq` (equal), `-ne` (not equal), `-lt` (less than), `-le`, `-gt`, `-ge`. Dùng `=` hoặc `==` trong `[ ]` là string comparison, không phải numeric.

---

## Phần 2 — Trung bình (câu 8–15)

**Câu 8:** Sự khác biệt giữa `[ ]` và `[[ ]]` trong bash?

- A. `[[ ]]` chỉ dùng trong bash, hỗ trợ regex, glob, `&&`, `||`; `[ ]` là POSIX portable
- B. `[[ ]]` chậm hơn `[ ]`
- C. `[ ]` hỗ trợ more operators
- D. Không có sự khác biệt

> **Đáp án: A** — `[[ ]]` bash-specific: hỗ trợ `=~` (regex), `==` với glob, `&&`/`||` thay vì `-a`/`-o`, không cần quote biến (an toàn hơn với whitespace). `[ ]` POSIX portable, chạy được trên mọi sh.

---

**Câu 9:** Vòng lặp nào đọc từng dòng của file an toàn (kể cả dòng có space)?

- A. `for line in $(cat file.txt)`
- B. `cat file.txt | for line`
- C. `while IFS= read -r line; do ... done < file.txt`
- D. `read -f file.txt`

> **Đáp án: C** — `IFS=` (empty) giữ nguyên leading/trailing whitespace. `-r` không xử lý backslash escape. `< file.txt` redirect trực tiếp, không fork subprocess. `for line in $(cat file)` bị word splitting, phá vỡ với space.

---

**Câu 10:** `${var:-default}` có tác dụng gì?

- A. Gán "default" vào `var` nếu `var` chưa set
- B. In "default" nếu `var` chưa set hoặc rỗng, nhưng KHÔNG thay đổi `var`
- C. Xóa `var` và thay bằng "default"
- D. Lỗi nếu `var` chưa set, hiện "default"

> **Đáp án: B** — `${var:-default}` = "nếu var unset/empty thì dùng default". Không sửa var. `${var:=default}` mới gán luôn vào var. `${var:?msg}` exit với error. `${var:+alt}` dùng alt nếu var đã set.

---

**Câu 11:** `local` trong function dùng để làm gì?

```bash
x=10
foo() {
    local x=99
    echo $x
}
foo
echo $x
```

- A. Báo lỗi — `x` đã tồn tại
- B. In 99 rồi 10 — `local` tạo biến scope riêng trong function, không ảnh hưởng global
- C. In 99 rồi 99 — `local` là alias
- D. In 10 rồi 99

> **Đáp án: B** — `local` giới hạn scope của biến trong function. `local x=99` tạo x mới trong function, không đụng đến global `x=10`. Luôn dùng `local` trong function để tránh side effects.

---

**Câu 12:** `trap cleanup EXIT` làm gì?

- A. Bắt tín hiệu Ctrl+C và chạy cleanup
- B. Chạy function `cleanup` khi script exit vì bất kỳ lý do nào (kể cả lỗi, Ctrl+C)
- C. Chạy cleanup trước khi script bắt đầu
- D. Xóa tất cả traps khi exit

> **Đáp án: B** — `trap cmd SIGNAL`: `EXIT` là pseudo-signal, trigger khi script kết thúc bất kỳ lý do nào. Dùng để cleanup: xóa temp files, unlock, close connections. `trap cleanup INT TERM` bắt Ctrl+C và kill.

---

**Câu 13:** `set -euo pipefail` có nghĩa là gì?

- A. Enable extended globbing, unicode, và pipe fail
- B. `-e`: exit khi có lỗi; `-u`: lỗi khi dùng biến unset; `-o pipefail`: pipeline fail nếu bất kỳ command nào fail
- C. Set encoding, output, và pipe options
- D. Enable error logging, undo, và pipe filtering

> **Đáp án: B** — Best practice khi viết script: `-e` (errexit): script dừng khi command fail. `-u` (nounset): lỗi nếu dùng biến chưa khai báo. `pipefail`: `cmd1 | cmd2` fail nếu cmd1 fail (không chỉ cmd2).

---

**Câu 14:** Phân tích: `$@` vs `$*` khi có double-quote?

```bash
args() { for a in "$@"; do echo "[$a]"; done; }
args "hello world" "foo"
```

- A. `"$@"` và `"$*"` hoạt động giống nhau
- B. `"$@"` giữ nguyên từng argument; `"$*"` nối thành 1 string
- C. `"$*"` giữ nguyên từng argument; `"$@"` nối thành 1 string
- D. Cả hai đều fail khi có space

> **Đáp án: B** — `"$@"` expand thành `"hello world" "foo"` (2 args riêng). `"$*"` expand thành `"hello world foo"` (1 string). Luôn dùng `"$@"` khi forward arguments để giữ nguyên.

---

**Câu 15:** Lệnh `getopts` dùng để làm gì?

- A. Lấy tùy chọn từ /etc/options
- B. Parse command-line options dạng `-a value -b` trong script
- C. Get shell options đang active
- D. Tạo menu options cho user

> **Đáp án: B** — `getopts "ab:c" opt` parse: `-a` (flag), `-b value` (với argument), `-c` (flag). Trong vòng lặp `while getopts ...`, `$OPTARG` chứa value của option có `:`. Chuẩn POSIX, không hỗ trợ long options (`--name`).

---

## Phần 3 — Nâng cao (câu 16–22)

**Câu 16:** Script sau có bug gì?

```bash
#!/bin/bash
DIR="/tmp/work"
rm -rf "$DIR"/*
```

- A. Không có bug
- B. Nếu `$DIR` rỗng, lệnh trở thành `rm -rf /*` — xóa toàn bộ hệ thống
- C. `rm -rf` không hoạt động với wildcard
- D. Phải dùng `rm -r` thay vì `rm -rf`

> **Đáp án: B** — Nếu `DIR=""` → `rm -rf ""/\*` = `rm -rf /*` → thảm họa. An toàn hơn: `[[ -n "$DIR" && -d "$DIR" ]] && rm -rf "${DIR:?}/"*`. `${DIR:?}` exit nếu DIR rỗng.

---

**Câu 17:** Tại sao `for f in $(ls *.txt)` bị coi là bad practice?

- A. `ls` không có trong PATH
- B. Word splitting: filename có space bị tách thành nhiều từ; `ls` output có thể bị interpret sai
- C. `*.txt` glob không hoạt động với `ls`
- D. Không có lý do, đây là cách đúng

> **Đáp án: B** — File `"my file.txt"` → `$(ls)` trả về `my file.txt` → shell split thành `my` và `file.txt` → 2 file không tồn tại. Đúng: `for f in *.txt; do ...` — shell globbing an toàn với filename có space.

---

**Câu 18:** Phân tích output của script sau:

```bash
#!/bin/bash
set -e
mkdir /tmp/test_dir
cd /tmp/nonexistent_dir    # thư mục không tồn tại
echo "This should not print"
```

- A. In "This should not print" vì `cd` không phải lỗi nghiêm trọng
- B. Script exit sau `cd` fail vì `set -e` — không in gì sau đó
- C. Script tiếp tục từ `/tmp/test_dir`
- D. Script crash với segfault

> **Đáp án: B** — `set -e` (errexit): script dừng ngay khi bất kỳ command nào trả về non-zero exit code. `cd /tmp/nonexistent_dir` fail → script exit, "This should not print" không được thực thi.

---

**Câu 19:** Cách đúng để kiểm tra command có tồn tại không trước khi dùng?

- A. `if [ -f /usr/bin/docker ]`
- B. `if command -v docker &>/dev/null`
- C. `if which docker`
- D. `if type docker`

> **Đáp án: B** — `command -v cmd` tìm trong PATH, return 0 nếu tìm thấy. Portable hơn `which` (không phải lúc nào cũng có). `[ -f /usr/bin/docker ]` hardcode path. `command -v` cũng tìm aliases và functions, `type` tương tự nhưng verbose hơn.

---

**Câu 20:** Script nhận argument và in ra usage nếu không có. Cách nào đúng?

```bash
#!/bin/bash
# Cách A:
if [ "$#" -eq 0 ]; then
    echo "Usage: $0 <filename>" >&2
    exit 1
fi

# Cách B:
[ "$#" -eq 0 ] && echo "Usage: $0 <filename>" >&2 && exit 1
```

- A. Chỉ Cách A đúng
- B. Chỉ Cách B đúng
- C. Cả hai đúng nhưng Cách A rõ ràng hơn
- D. Cả hai sai

> **Đáp án: C** — Cả hai hoạt động đúng. Cách A dùng if-fi block rõ ràng hơn cho logic phức tạp. Cách B dùng short-circuit evaluation (`&&`), ngắn gọn hơn. Lưu ý: `>&2` ghi ra stderr (đúng cho error/usage messages).

---

**Câu 21:** Hàm sau trả về giá trị như thế nào?

```bash
get_count() {
    local count=$(ls /tmp/*.log 2>/dev/null | wc -l)
    echo "$count"
}
num=$(get_count)
echo "Count: $num"
```

- A. Dùng `return $count` để trả về
- B. Dùng `echo "$count"` — caller capture qua command substitution `$(get_count)`
- C. Function không thể trả về số
- D. Phải dùng global variable

> **Đáp án: B** — Bash function không thể return giá trị như Python (chỉ `return` được exit code 0-255). Pattern chuẩn: function `echo` giá trị → caller dùng `$(function_name)` để capture. Hoặc dùng global variable (ít được khuyến khích).

---

**Câu 22:** Phân tích heredoc sau và cho biết output:

```bash
name="World"
cat << 'EOF'
Hello, $name!
EOF
```

- A. `Hello, World!`
- B. `Hello, $name!` (literal)
- C. Lỗi syntax
- D. `Hello, !`

> **Đáp án: B** — Single-quoted heredoc `<< 'EOF'` không expand variables — mọi thứ là literal. Double-quoted `<< "EOF"` hoặc unquoted `<< EOF` mới expand `$name` → "Hello, World!". Dùng `'EOF'` khi muốn giữ nguyên ký tự đặc biệt.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | C      | 13  | B      |
| 3   | C      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | B      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | A      | 19  | B      |
| 9   | C      | 20  | C      |
| 10  | B      | 21  | B      |
| 11  | B      | 22  | B      |
