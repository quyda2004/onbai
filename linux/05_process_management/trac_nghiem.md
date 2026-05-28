# Trắc nghiệm — Linux Process Management

> **Tổng số câu:** 20 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Lệnh nào xem tất cả process đang chạy trong hệ thống?

- A. `ls -p`
- B. `ps aux`
- C. `jobs -a`
- D. `proc list`

> **Đáp án: B** — `ps aux`: `a` = all users, `u` = user-oriented format, `x` = processes không có terminal. Cung cấp PID, %CPU, %MEM, COMMAND.

---

**Câu 2:** `kill -9 1234` làm gì?

- A. Gửi SIGTERM cho PID 1234 (graceful stop)
- B. Gửi SIGKILL cho PID 1234 (force kill, không thể bắt/block)
- C. Kill process tên "1234"
- D. Pause process 1234

> **Đáp án: B** — Signal 9 = SIGKILL. Process không thể bắt hoặc ignore SIGKILL → kernel kill ngay lập tức. Mặc định `kill` (không có -số) gửi SIGTERM (15) cho phép cleanup.

---

**Câu 3:** `Ctrl+Z` khi process đang chạy ở foreground làm gì?

- A. Tắt process
- B. Suspend (dừng tạm) process và trả terminal về
- C. Đưa process lên background
- D. Pause output của process

> **Đáp án: B** — `Ctrl+Z` gửi SIGSTOP → process vào state T (stopped). Sau đó: `bg` để resume ở background, `fg` để đưa lại foreground, `jobs` để xem. `Ctrl+C` mới kill (SIGINT).

---

**Câu 4:** `ps aux --sort=-%cpu | head -6` làm gì?

- A. Hiện 6 processes nặng CPU nhất
- B. Sort tất cả processes theo CPU rồi kill top 6
- C. Hiện 6 processes nhẹ CPU nhất
- D. Hiện CPU usage trong 6 giây

> **Đáp án: A** — `--sort=-%cpu` sort theo %CPU giảm dần (dấu `-` là reverse). `head -6` lấy 6 dòng đầu (1 dòng là header, 5 là processes). Cột %CPU trong `ps aux` là $3.

---

**Câu 5:** Zombie process là gì?

- A. Process chạy với quyền root
- B. Process đã chết nhưng entry vẫn còn trong process table vì parent chưa gọi wait()
- C. Process bị suspend
- D. Process chiếm quá nhiều RAM

> **Đáp án: B** — Khi child process chết, kernel giữ entry trong process table cho đến khi parent gọi `wait()` để "thu dọn". Nếu parent không gọi → zombie (trạng thái Z trong `ps`). Zombie không tốn CPU/RAM nhưng tốn PID slot.

---

**Câu 6:** `pgrep nginx` làm gì?

- A. Kill tất cả processes tên nginx
- B. In PID của tất cả processes có tên chứa "nginx"
- C. Hiện thông tin chi tiết nginx process
- D. Tìm file cấu hình nginx

> **Đáp án: B** — `pgrep` (process grep) tìm và in PID theo tên. `pgrep -l nginx` kèm tên, `pgrep -a nginx` kèm full command. Khác với `pkill` mới kill.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** SIGTERM vs SIGKILL — khi nào dùng SIGKILL?

- A. Luôn dùng SIGKILL vì chắc chắn hơn
- B. Chỉ dùng SIGKILL khi process không respond với SIGTERM — SIGTERM cho phép cleanup, SIGKILL không
- C. SIGKILL cho user processes, SIGTERM cho system processes
- D. SIGTERM và SIGKILL giống nhau

> **Đáp án: B** — Best practice: thử SIGTERM trước (graceful: đóng connection, flush buffer, release locks). Chờ vài giây. Nếu vẫn chạy → SIGKILL. `kill PID` → chờ → `kill -9 PID`.

---

**Câu 8:** `nohup python server.py &` khác `python server.py &` thế nào?

- A. `nohup` chạy nhanh hơn
- B. Khi đóng terminal, `&` sẽ nhận SIGHUP và chết; `nohup` ignore SIGHUP → tiếp tục chạy
- C. `nohup` auto-restart khi crash
- D. Không có sự khác biệt

> **Đáp án: B** — Khi đóng terminal, shell gửi SIGHUP cho tất cả child processes → chúng chết. `nohup` ignore SIGHUP và redirect stdin/stdout. Sau này dùng `tmux` hoặc `screen` thay vì `nohup` vì có thể reattach.

---

**Câu 9:** Load average `3.5` trên máy có 4 CPU cores có nghĩa là gì?

- A. 3.5% CPU usage
- B. Trung bình 3.5 processes trong run queue — máy đang dùng ~87.5% capacity
- C. 3.5 processes đang chạy đồng thời
- D. Máy overloaded hoàn toàn

> **Đáp án: B** — Load average là số trung bình processes trong run queue (running + waiting for CPU). Load = số cores nghĩa là 100% utilized. 3.5 / 4 cores = 87.5% load — bình thường, không overloaded. Load > số cores → bắt đầu có queue.

---

**Câu 10:** `nice -n 15 python batch.py` có tác dụng gì?

- A. Chạy python với 15 threads
- B. Chạy python với priority thấp (nice 15) — ưu tiên nhường CPU cho processes khác
- C. Giới hạn python dùng tối đa 15% CPU
- D. Chạy python sau 15 giây

> **Đáp án: B** — Nice value: -20 (cao nhất) đến 19 (thấp nhất). `nice -n 15` = low priority. Kernel scheduler ưu tiên processes có nice thấp. Hữu ích cho batch jobs không cần response nhanh.

---

**Câu 11:** `lsof -i :3000` cho biết điều gì?

- A. Liệt kê tất cả files trong thư mục 3000
- B. Process nào đang lắng nghe hoặc kết nối trên port 3000
- C. Kiểm tra xem port 3000 có mở không
- D. Kill process dùng port 3000

> **Đáp án: B** — `lsof -i :port` liệt kê tất cả network connections trên port đó: tên process, PID, user, type (TCP/UDP), state (LISTEN/ESTABLISHED). Hữu ích khi cần biết process nào chiếm port.

---

**Câu 12:** `systemctl reload nginx` vs `systemctl restart nginx` — khác nhau thế nào?

- A. Không khác gì, cùng kết quả
- B. `reload` đọc lại config mà không stop service (zero downtime); `restart` stop hoàn toàn rồi start lại
- C. `reload` chỉ dùng cho nginx, restart dùng chung
- D. `reload` nhanh hơn vì không check config

> **Đáp án: B** — `reload` gửi SIGHUP (hoặc mechanism tương đương), process tự reload config mà không close connections hiện tại. `restart` = stop + start → có downtime ngắn. Dùng `reload` khi có thể, `restart` khi cần thiết.

---

**Câu 13:** Lệnh `jobs -l` hiển thị gì?

- A. Tất cả processes trong hệ thống
- B. Background và stopped jobs của shell hiện tại, kèm PID
- C. Jobs đang chạy của tất cả users
- D. Job scheduler (cron) entries

> **Đáp án: B** — `jobs` chỉ hiển thị jobs thuộc shell session hiện tại. `[1]+ Running python server.py &`. `-l` kèm PID. `fg %1` đưa job 1 ra foreground.

---

**Câu 14:** Process state `D` (Uninterruptible Sleep) có vấn đề gì?

- A. Process đang bị pause bởi user
- B. Process đang chờ I/O (thường là disk) và không thể bị interrupt hoặc kill bằng SIGKILL
- C. Process đã chết nhưng chưa cleanup
- D. Process đang dùng 100% CPU

> **Đáp án: B** — State D = process đang trong system call chờ I/O, không thể interrupt. `kill -9` KHÔNG hiệu quả — kernel chờ I/O xong. Thường do hung NFS mount, disk I/O chậm, hoặc driver bug. Cách giải quyết: fix I/O issue (unmount hung filesystem, khởi động lại).

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Script sau có vấn đề gì?

```bash
pid=$(pgrep myapp)
kill $pid
```

- A. Không có vấn đề
- B. Nếu myapp không chạy, `pgrep` trả về rỗng → `kill ` (không có arg) hoặc lỗi
- C. `pgrep` không tương thích với `kill`
- D. Phải dùng `pkill` thay vì pgrep + kill

> **Đáp án: B** — Nếu `pgrep` không tìm thấy process, `$pid` rỗng → `kill ` → kill không có argument có thể gây lỗi hoặc unexpected behavior. Cần: `if pgrep myapp > /dev/null; then kill $(pgrep myapp); fi` hoặc `pkill myapp`.

---

**Câu 16:** `sudo journalctl -u nginx -f --since "10 minutes ago"` làm gì?

- A. Xem 10 dòng log nginx gần nhất
- B. Follow log nginx của 10 phút gần nhất (real-time + history 10 phút)
- C. Restart nginx sau 10 phút
- D. Xóa log nginx từ 10 phút trước

> **Đáp án: B** — `journalctl -u nginx` xem logs của nginx service. `--since "10 minutes ago"` giới hạn từ 10 phút trước. `-f` follow real-time. Kết hợp: xem history 10 phút + theo dõi tiếp.

---

**Câu 17:** Tại sao không nên dùng `kill -9` là lựa chọn đầu tiên?

- A. Vì `-9` không hoạt động với root processes
- B. SIGKILL ngăn process cleanup: không flush buffers, không đóng connections, không release locks → có thể gây data corruption
- C. Vì `-9` cần quyền root
- D. `-9` chậm hơn SIGTERM

> **Đáp án: B** — SIGTERM cho phép signal handler chạy: flush data, close sockets, xóa temp files, release database locks. SIGKILL là emergency chop — kernel kill ngay không đợi. Database bị SIGKILL có thể bị corrupt transaction.

---

**Câu 18:** Lệnh `watch -n 2 'ps aux | sort -k3 -rn | head -10'` làm gì?

- A. Chạy lệnh ps 1 lần và refresh mỗi 2 giây
- B. Chạy lệnh trong dấu nháy đơn mỗi 2 giây, hiển thị cập nhật — như top nhưng tùy chỉnh được
- C. Monitor 2 process đầu tiên
- D. Sort ps output 2 lần

> **Đáp án: B** — `watch -n 2 'cmd'` chạy `cmd` mỗi 2 giây và refresh terminal. Hữu ích để monitor bất kỳ lệnh nào (không chỉ process). `watch -d` highlight differences.

---

**Câu 19:** Sau khi chạy `sleep 100 &`, làm thế nào để kill nó sau khi đóng terminal?

- A. Không thể, process đã orphan
- B. Dùng `pgrep sleep | xargs kill` từ terminal mới
- C. Chỉ reboot mới kill được
- D. Process tự chết khi terminal đóng

> **Đáp án: B** — Background process (`&`) vẫn chạy sau khi đóng terminal (trừ khi có `disown` và nhận SIGHUP). Từ terminal mới: `pgrep sleep` tìm PID, `kill PID` để stop. Hoặc `pkill sleep`.

---

**Câu 20:** `ss -tuln` hiển thị gì và tại sao tốt hơn `netstat -tuln`?

- A. Không có sự khác biệt
- B. Cả hai hiển thị listening ports; `ss` dùng netlink (kernel) trực tiếp — nhanh hơn và không deprecated như `netstat`
- C. `ss` chỉ hiển thị TCP, `netstat` hiển thị cả UDP
- D. `ss` cần quyền root, `netstat` thì không

> **Đáp án: B** — `-t` TCP, `-u` UDP, `-l` listening, `-n` numeric (không resolve hostname). `ss` (socket statistics) từ `iproute2` package, dùng kernel netlink → nhanh và chính xác hơn `netstat` (từ `net-tools`, deprecated). `-p` thêm process name.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 11  | B      |
| 2   | B      | 12  | B      |
| 3   | B      | 13  | B      |
| 4   | A      | 14  | B      |
| 5   | B      | 15  | B      |
| 6   | B      | 16  | B      |
| 7   | B      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | B      |
| 10  | B      | 20  | B      |
