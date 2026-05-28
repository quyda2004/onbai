# Linux Process Management — Quản lý Tiến trình

---

## Giải thích cho người mới

Mỗi chương trình đang chạy là một **process** (tiến trình). Giống như nhiều ứng dụng đang mở trong Windows — mỗi cái có một ID riêng (PID). Linux cho bạn xem, kiểm soát, tắt chúng từ terminal.

Ví dụ thực tế:
- `ps aux` — xem tất cả process đang chạy (như Task Manager)
- `kill 1234` — tắt process có PID 1234
- `top` — xem real-time (như tab Performance trong Task Manager)

---

## Giải thích nâng cao

**Process states:**
```
R  Running / Runnable (đang chạy hoặc chờ CPU)
S  Sleeping (đang chờ event, interruptible)
D  Uninterruptible Sleep (chờ I/O, không thể kill bình thường)
Z  Zombie (đã chết nhưng chưa được parent "thu dọn" qua wait())
T  Stopped (bị dừng bởi signal SIGSTOP)
```

**Signals quan trọng:**
| Signal | Số | Mô tả | Bắt được? |
|--------|-----|-------|-----------|
| SIGHUP | 1 | Hangup — reload config | Có |
| SIGINT | 2 | Interrupt (Ctrl+C) | Có |
| SIGQUIT | 3 | Quit + core dump (Ctrl+\\) | Có |
| SIGKILL | 9 | Kill ngay lập tức, **không thể bắt/block** | Không |
| SIGTERM | 15 | Terminate gracefully (default `kill`) | Có |
| SIGSTOP | 19 | Stop process (không thể bắt) | Không |
| SIGCONT | 18 | Continue stopped process | Có |

**Foreground vs Background:**
- **Foreground**: process chiếm terminal, bạn phải chờ nó xong
- **Background**: process chạy ngầm, terminal vẫn dùng được

**Orphan process**: parent chết trước → process được adopt bởi `init/systemd` (PID 1).
**Zombie process**: child chết nhưng parent chưa gọi `wait()` → entry vẫn còn trong process table.

---

## BẢNG LỆNH THỰC HÀNH

### ps — Process Status
```bash
# Xem process của user hiện tại
ps                             # PID TTY TIME CMD (minimal)
ps u                           # user-oriented format
ps aux                         # ALL processes, detailed
ps aux | head -1 && ps aux | grep nginx  # header + grep

# Columns trong ps aux:
# USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
# VSZ = virtual memory size
# RSS = resident set size (RAM thực dùng)
# STAT = process state

ps -ef                         # full-format listing
ps -ef | grep nginx            # tìm process nginx

ps --pid 1234                  # xem process cụ thể
ps --ppid 1234                 # xem child processes của PID 1234
ps -u alice                    # processes của user alice
ps -C nginx                    # processes tên nginx
ps aux --sort=-%cpu            # sort theo CPU (nhiều nhất đầu)
ps aux --sort=-%mem            # sort theo memory
ps auxf                        # forest view — hiện parent-child tree

# Xem process tree
pstree                         # tree tất cả
pstree -p                      # kèm PID
pstree -u                      # kèm username
pstree alice                   # tree của user alice
pstree -p 1234                 # tree từ PID cụ thể
```

### top / htop — Real-time Process Viewer
```bash
top                            # real-time (refresh mỗi 3 giây)

# Trong top:
# q     thoát
# k     kill process (nhập PID)
# r     renice (đổi priority)
# f     chọn fields hiển thị
# o     sort theo field
# 1     hiện từng CPU core
# m     toggle memory display
# M     sort theo memory
# P     sort theo CPU (default)
# u     filter theo user
# /     tìm kiếm process

top -u alice                   # chỉ processes của alice
top -n 1 -b                    # snapshot 1 lần, batch mode (cho script)
top -n 1 -b | head -20         # top 20 processes

# htop — đẹp hơn, interactive hơn (cần install)
htop                           # F10 để thoát
htop -u alice                  # filter user
htop -p 1234,5678              # chỉ xem PID cụ thể
```

### kill / killall / pkill — Tắt process
```bash
# kill — gửi signal đến PID
kill 1234                      # SIGTERM (graceful stop)
kill -9 1234                   # SIGKILL (force kill)
kill -15 1234                  # SIGTERM (explicit)
kill -HUP 1234                 # SIGHUP (reload config)
kill -l                        # liệt kê tất cả signals

# killall — kill theo tên
killall nginx                  # kill tất cả process tên nginx
killall -9 nginx               # force kill
killall -u alice               # kill tất cả processes của alice
killall -s HUP nginx           # gửi SIGHUP (reload)

# pkill — kill theo pattern
pkill nginx                    # kill processes match "nginx"
pkill -9 firefox               # force kill
pkill -u alice                 # kill processes của user alice
pkill -f "python script.py"    # match full command line
pkill -TERM -P 1234            # kill children của PID 1234

# pgrep — tìm PID theo pattern (không kill)
pgrep nginx                    # in PID của nginx processes
pgrep -l nginx                 # in PID + name
pgrep -a nginx                 # in PID + full command
pgrep -u alice                 # processes của alice
pgrep -f "python script.py"    # match full command
```

### Foreground / Background
```bash
command &                      # chạy ngầm (background)
command > output.log 2>&1 &    # chạy ngầm, redirect output

Ctrl+C                         # kill foreground process
Ctrl+Z                         # suspend (dừng) foreground process → trở thành stopped

jobs                           # xem background + stopped jobs
jobs -l                        # kèm PID
bg                             # resume job gần nhất ở background
bg %2                          # resume job số 2 ở background
fg                             # đưa background job gần nhất ra foreground
fg %2                          # đưa job số 2 ra foreground

# Ví dụ workflow:
python train.py     # bắt đầu chạy
Ctrl+Z              # suspend
bg                  # tiếp tục ở background
fg                  # đưa lại foreground
```

### nohup / disown — Chạy sau khi đóng terminal
```bash
nohup command &                # chạy ngầm, không bị SIGHUP khi đóng terminal
nohup command > output.log 2>&1 &  # redirect output
nohup python server.py &

# Với process đang chạy (đã bg)
disown %1                      # tách job 1 khỏi shell
disown -h %1                   # tách + ignore SIGHUP
disown -a                      # tách tất cả jobs

# screen / tmux — tốt hơn nohup
screen -S myname               # tạo screen session tên "myname"
screen -d -m command           # detached screen với command
screen -r myname               # reconnect
screen -ls                     # liệt kê sessions

tmux new -s myname             # tạo tmux session
tmux detach (Ctrl+B, D)        # detach
tmux attach -t myname          # reattach
tmux ls                        # liệt kê sessions
```

### nice / renice — Priority
```bash
# Priority (nice value): -20 (cao nhất) đến 19 (thấp nhất), mặc định 0
# Chỉ root mới có thể set nice < 0

nice -n 10 command             # chạy với priority thấp (nice 10)
nice -n -5 command             # chạy với priority cao (cần root)
sudo nice -n -10 command

renice 10 -p 1234              # đổi priority của process 1234
renice -5 -p 1234              # cần root cho nice âm
renice 10 -u alice             # đổi priority tất cả processes của alice

# Xem nice value
ps -eo pid,ni,comm | grep nginx
top                            # cột NI
```

### lsof — List Open Files
```bash
lsof                           # tất cả open files (rất nhiều)
lsof /etc/nginx/nginx.conf     # process nào đang dùng file này
lsof -u alice                  # open files của user alice
lsof -p 1234                   # open files của PID 1234
lsof -i                        # network connections (tất cả)
lsof -i :80                    # process dùng port 80
lsof -i :80 -i :443            # port 80 hoặc 443
lsof -i tcp                    # TCP connections
lsof -i tcp:22                 # TCP port 22
lsof +D /var/log               # tất cả open files trong /var/log
lsof -c nginx                  # open files của process tên nginx
```

### systemctl — Quản lý Services (systemd)
```bash
# Xem status
systemctl status nginx         # status chi tiết của nginx service
systemctl is-active nginx      # active hoặc inactive
systemctl is-enabled nginx     # enabled (start on boot) hoặc disabled

# Kiểm soát service
sudo systemctl start nginx     # khởi động
sudo systemctl stop nginx      # dừng
sudo systemctl restart nginx   # restart
sudo systemctl reload nginx    # reload config (không down service)
sudo systemctl enable nginx    # tự khởi động khi boot
sudo systemctl disable nginx   # không tự khởi động
sudo systemctl mask nginx      # chặn hoàn toàn (không thể start)
sudo systemctl unmask nginx    # bỏ chặn

# Liệt kê services
systemctl list-units --type=service          # running services
systemctl list-units --type=service --all    # tất cả
systemctl list-unit-files --type=service     # enabled/disabled status

# Logs của service
sudo journalctl -u nginx                # tất cả logs
sudo journalctl -u nginx -f             # follow (real-time)
sudo journalctl -u nginx --since today  # hôm nay
sudo journalctl -u nginx -n 50          # 50 dòng cuối
sudo journalctl -p err                  # chỉ error level
```

### Monitoring commands
```bash
# Memory
free -h                        # RAM usage (human-readable)
free -m                        # MB
cat /proc/meminfo              # chi tiết

# CPU
uptime                         # load average (1, 5, 15 phút)
cat /proc/loadavg              # load average + running/total processes
nproc                          # số CPU cores
cat /proc/cpuinfo              # thông tin CPU chi tiết

# I/O
iostat -x 1                    # disk I/O stats mỗi 1 giây
vmstat 1 5                     # system stats mỗi 1 giây, 5 lần
sar -u 1 5                     # CPU utilization

# Network
netstat -tuln                  # listening ports (cũ, cần net-tools)
ss -tuln                       # listening ports (mới, tốt hơn)
ss -tunp                       # kèm process name
ss -s                          # statistics

# Tổng quan
htop                           # tất cả trong 1
glances                        # system monitor đẹp (pip install glances)
```

---

## Khi nào dùng gì

| Tình huống | Lệnh |
|-----------|------|
| Xem tất cả process | `ps aux` hoặc `htop` |
| Tìm PID của nginx | `pgrep nginx` hoặc `ps aux \| grep nginx` |
| Kill gracefully | `kill PID` (SIGTERM) |
| Kill cứng | `kill -9 PID` (SIGKILL) |
| Reload config | `kill -HUP PID` hoặc `systemctl reload` |
| Xem port nào đang nghe | `ss -tuln` |
| Xem process dùng file/port | `lsof -i :80` |
| Chạy ngầm, không mất khi đóng terminal | `nohup cmd &` hoặc `tmux` |
| Quản lý service | `systemctl start/stop/status` |

---

## Lỗi thường gặp

```bash
# kill -9 không nên là lựa chọn đầu tiên
kill PID              # thử SIGTERM trước — graceful shutdown
kill -9 PID           # chỉ khi process không respond

# Process zombie không thể kill
kill -9 zombie_pid    # KHÔNG hiệu quả — zombie đã chết, chỉ cần wait() từ parent
# Giải pháp: kill parent process

# Nhầm kill PID của chính shell
kill -9 $$            # $$ là PID của shell hiện tại → đóng terminal!

# pkill quá rộng
pkill python          # kill TẤT CẢ python processes (cả của user khác?)
pkill -u alice python # chỉ kill python của alice
pkill -f "specific_script.py"  # match full command line
```

---

## Câu hỏi phỏng vấn hay gặp

- SIGTERM vs SIGKILL — khác nhau thế nào? Khi nào dùng SIGKILL?
- Zombie process là gì? Tại sao xảy ra?
- Load average 3.5 trên 4-core machine nghĩa là gì?
- `Ctrl+C` vs `Ctrl+Z` — khác nhau gì?
- `nohup` vs `tmux` — khi nào dùng cái nào?
- Tại sao không nên dùng `kill -9` ngay từ đầu?
