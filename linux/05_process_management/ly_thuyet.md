# Linux Process Management — Quản lý Tiến trình

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng máy tính như một nhà máy. Mỗi chương trình đang chạy là một "công nhân" (process) được giao một việc cụ thể. Nhà máy (hệ điều hành) phân chia thời gian của các máy móc (CPU) cho từng công nhân một cách công bằng.

Mỗi công nhân có một **mã số nhân viên** (PID — Process ID). Khi một công nhân tạo ra công nhân mới (ví dụ trình duyệt mở tab mới), người tạo gọi là **cha** (parent), người được tạo gọi là **con** (child process).

`ps` giống như danh sách điểm danh công nhân. `top`/`htop` là bảng điều khiển trực quan — bạn thấy ai đang dùng nhiều máy móc nhất. `kill` không có nghĩa là "giết" — thực ra nó gửi **tín hiệu** cho process: "dừng lại nhẹ nhàng" (SIGTERM) hoặc "dừng ngay lập tức" (SIGKILL).

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Process vs Thread:** Process là đơn vị cô lập — có address space riêng, file descriptor table riêng. Thread là đơn vị thực thi trong process — share address space, FD table, nhưng có stack riêng. Linux dùng `clone()` syscall cho cả hai, khác nhau ở flags.

**Process states (scheduler perspective):**
- `R` (Running/Runnable): đang chạy hoặc trong run queue chờ CPU
- `S` (Sleeping interruptible): chờ event (I/O, signal) — có thể bị interrupt
- `D` (Sleeping uninterruptible): chờ I/O không thể interrupt — thường là disk I/O. Process `D` không thể bị kill bằng SIGKILL! Đây là dấu hiệu disk/NFS hung.
- `Z` (Zombie): process đã exit nhưng parent chưa gọi `wait()` để lấy exit code. Zombie không dùng tài nguyên nhưng chiếm PID entry.
- `T` (Stopped): bị dừng bởi SIGSTOP/SIGTSTP hoặc debugger

**Signals:** Kernel deliver signal bằng cách set bit trong `task_struct`. Khi process quay lại userspace, kernel kiểm tra pending signals. `SIGKILL` và `SIGSTOP` không thể bị catch/ignore — kernel xử lý trực tiếp. `SIGTERM` process có thể catch để cleanup. `SIGHUP` truyền thống là "terminal closed" — nhiều daemon dùng để reload config.

**`/proc/[PID]/`:** Virtual directory exposing kernel data về process. `cmdline` là null-separated args. `status` là human-readable process info. `fd/` là symlinks tới open file descriptors. `maps` là memory map. `limits` là resource limits.

**Nice và priority:** Linux scheduler dùng CFS (Completely Fair Scheduler). Nice value (-20 đến +19) ảnh hưởng đến weight trong CFS. Negative nice = higher weight = more CPU. Chỉ root set negative nice.

---

## Process States

| State | Symbol | Ý nghĩa | Cách xử lý |
|-------|--------|---------|-----------|
| Running | `R` | Đang chạy hoặc trong run queue | Bình thường |
| Sleeping | `S` | Chờ event, có thể interrupt | Bình thường |
| Disk Sleep | `D` | Chờ I/O, không thể interrupt | Có thể là vấn đề disk/NFS |
| Zombie | `Z` | Đã exit, chờ parent | Parent có bug, hoặc reap bằng kill parent |
| Stopped | `T` | Bị dừng (SIGSTOP/SIGTSTP) | `fg` hoặc `kill -CONT PID` |
| Tracing | `t` | Bị trace bởi debugger | Bình thường khi debug |

## Signals Quan trọng

| Signal | Number | Ý nghĩa | Có thể catch? |
|--------|--------|---------|--------------|
| SIGHUP | 1 | Hangup / Reload config | Có |
| SIGINT | 2 | Interrupt (Ctrl+C) | Có |
| SIGQUIT | 3 | Quit with core dump | Có |
| SIGKILL | 9 | Kill ngay lập tức | KHÔNG |
| SIGTERM | 15 | Terminate gracefully | Có |
| SIGSTOP | 19 | Stop process | KHÔNG |
| SIGCONT | 18 | Continue stopped process | Có |
| SIGCHLD | 17 | Child process changed state | Có |
| SIGUSR1 | 10 | User-defined signal 1 | Có |
| SIGUSR2 | 12 | User-defined signal 2 | Có |

---

## Các lệnh / Cú pháp chính

| Lệnh | Mô tả | Ví dụ |
|------|-------|-------|
| `ps aux` | Tất cả processes (BSD style) | `ps aux` |
| `ps -ef` | Tất cả processes (POSIX style) | `ps -ef` |
| `ps aux --forest` | Hiện cây process | `ps aux --forest` |
| `ps -p PID` | Xem process cụ thể | `ps -p 1234` |
| `pgrep name` | Tìm PID theo tên | `pgrep nginx` |
| `top` | Realtime process monitor | `top` |
| `htop` | Interactive process monitor | `htop` |
| `kill PID` | Gửi SIGTERM | `kill 1234` |
| `kill -9 PID` | Gửi SIGKILL (force) | `kill -9 1234` |
| `kill -HUP PID` | Reload config (SIGHUP) | `kill -HUP $(cat nginx.pid)` |
| `pkill name` | Kill theo tên | `pkill firefox` |
| `killall name` | Kill tất cả process tên đó | `killall chrome` |
| `nice -n 10 cmd` | Chạy lệnh với nice +10 | `nice -n 19 backup.sh` |
| `renice -n 5 -p PID` | Thay đổi nice của process đang chạy | `renice -n 10 -p 1234` |
| `jobs` | Xem background jobs của shell | `jobs` |
| `bg %1` | Đưa job vào background | `bg %1` |
| `fg %1` | Đưa job về foreground | `fg %1` |
| `nohup cmd &` | Chạy background, thoát terminal | `nohup ./script.sh &` |
| `disown %1` | Tách job khỏi shell | `disown %1` |
| `lsof -p PID` | File đang mở bởi process | `lsof -p 1234` |
| `lsof -i :80` | Process dùng port 80 | `lsof -i :8080` |
| `strace -p PID` | Trace system calls | `strace -p 1234` |
| `screen` | Terminal multiplexer | `screen -S mysession` |
| `tmux` | Terminal multiplexer (modern) | `tmux new -s work` |

---

## Ví dụ thực tế

```bash
# ===== ps — Process Status =====
ps aux
# USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root         1  0.0  0.1 225848  9188 ?        Ss   Jan01   0:04 /sbin/init

# Fields quan trọng:
# %CPU: CPU usage
# %MEM: RSS / total RAM
# VSZ: virtual memory size (bao gồm swap, mapped files)
# RSS: resident set size (thực sự trong RAM)
# STAT: S=sleep, R=run, D=disk wait, Z=zombie, T=stopped, s=session leader, +=foreground
# TIME: tổng CPU time đã dùng

ps -ef --forest  # hiện parent-child relationship
ps aux | grep nginx | grep -v grep  # tìm nginx processes
ps aux --sort=-%cpu | head -10      # top 10 CPU consumers
ps aux --sort=-%mem | head -10      # top 10 memory consumers

# ===== top — Interactive monitor =====
top
# Phím tắt trong top:
# P: sort by CPU
# M: sort by memory
# k: kill process (nhập PID)
# r: renice
# q: quit
# 1: hiện CPU usage từng core
# u: filter by user

# ===== kill & signals =====
# Graceful shutdown (SIGTERM — process tự cleanup)
kill 1234
kill -15 1234
kill -TERM 1234   # ba cách đều như nhau

# Force kill (SIGKILL — kernel kill ngay)
kill -9 1234
kill -KILL 1234

# Reload config (SIGTERM cho nginx = graceful reload workers)
sudo kill -HUP $(cat /var/run/nginx.pid)
# Hoặc:
sudo nginx -s reload

# Kill theo tên
pkill -f "python script.py"   # -f match toàn bộ command line
pkill -u alice                 # kill tất cả process của user alice
killall -9 firefox             # kill tất cả firefox

# ===== Background/Foreground =====
# Chạy lệnh trong background
sleep 100 &          # & đưa vào background, hiện [1] PID
jobs                 # [1]+  Running    sleep 100 &
fg %1                # đưa về foreground
bg %1                # nếu đang stopped (Ctrl+Z), tiếp tục ở background

# Ctrl+Z: suspend process hiện tại
# Ctrl+C: send SIGINT (terminate)

# ===== nohup & disown =====
# Chạy script ngay cả khi thoát terminal
nohup ./long_script.sh > output.log 2>&1 &

# Tách process đang chạy khỏi shell (kể cả đang ở foreground trước đó)
./script.sh &
disown %1           # giờ đóng terminal cũng không ảnh hưởng

# Cách kiểm tra nohup output
tail -f nohup.out

# ===== nice & renice =====
# Chạy backup với priority thấp nhất (không cạnh tranh với production)
nice -n 19 rsync -av /data/ /backup/

# Hạ priority của process đang chạy
renice -n 15 -p $(pgrep backup_script)

# Root có thể tăng priority (negative nice)
sudo renice -n -5 -p $(pgrep critical_service)

# ===== /proc filesystem =====
ls /proc/$$          # proc entry của shell hiện tại
cat /proc/$$/cmdline | tr '\0' ' '  # command line của shell
ls -la /proc/$$/fd   # open file descriptors
cat /proc/meminfo    # memory thông tin
cat /proc/cpuinfo    # CPU thông tin
cat /proc/loadavg    # load average: 1min 5min 15min

# Xem limits của process
cat /proc/$$/limits

# ===== lsof — List Open Files =====
lsof -p 1234         # tất cả file mở bởi PID 1234
lsof -u alice        # tất cả file mở bởi user alice
lsof -i :8080        # process đang listen/connect port 8080
lsof -i TCP:80       # TCP connections trên port 80
lsof | grep deleted  # file bị xóa nhưng vẫn đang mở (chiếm disk space!)

# Giải phóng disk space bị file deleted chiếm
lsof | grep "deleted"
# Tìm PID và restart service đó để release file handle

# ===== strace — System call tracer =====
strace ls /etc       # trace system calls của lệnh ls
strace -p 1234       # attach tới process đang chạy
strace -e trace=open,read,write ls  # chỉ trace open/read/write
strace -o /tmp/trace.log -p 1234 &  # ghi vào file

# ===== screen & tmux =====
# screen
screen -S mysession          # tạo session tên mysession
screen -ls                   # liệt kê sessions
screen -r mysession          # reattach
# Ctrl+A D: detach
# Ctrl+A C: new window
# Ctrl+A N/P: next/prev window

# tmux (modern, recommended)
tmux new -s work             # tạo session mới
tmux ls                      # liệt kê sessions
tmux attach -t work          # attach lại
# Ctrl+B D: detach
# Ctrl+B C: new window
# Ctrl+B %: split vertical
# Ctrl+B ": split horizontal
```

---

## Kết hợp lệnh nâng cao (Pipes & Patterns)

```bash
# Tìm process chiếm nhiều RAM nhất, hiện chi tiết
ps aux --sort=-%rss | awk 'NR<=11 {printf "%-10s %6s %6s %s\n", $1, $2, $6, $11}'

# Kill tất cả process của một user trừ shell của họ
pkill -u alice -t pts/1      # kill theo terminal
kill $(ps aux | awk '/alice/ && !/bash/ {print $2}')

# Monitor process: alert khi disappear
watch -n5 "ps aux | grep myapp | grep -v grep || echo 'ALERT: myapp died'"

# Restart service tự động nếu bị crash
while true; do
  if ! pgrep -x myapp > /dev/null; then
    echo "$(date): myapp crashed, restarting..." >> /var/log/watchdog.log
    /usr/local/bin/myapp &
  fi
  sleep 30
done

# Tìm zombie processes và parent của chúng
ps aux | awk '$8 ~ /Z/ {print $2}' | while read pid; do
  ppid=$(awk '/PPid/ {print $2}' /proc/$pid/status 2>/dev/null)
  echo "Zombie PID: $pid, Parent PID: $ppid"
  ps -p $ppid -o comm=
done

# Profiling: xem process dùng gì trong 10 giây
timeout 10 strace -p $PID -c 2>&1 | tail -20

# Capture core dump khi process crash
ulimit -c unlimited
# Sau khi crash, core dump ở working directory hoặc /var/crash/

# Process substitution cho diff realtime output
diff <(ps aux | sort) <(sleep 5; ps aux | sort) | grep "^[<>]"
```

---

## Lỗi thường gặp (Common Pitfalls)

**1. `kill -9` ngay lập tức mà không thử SIGTERM trước**
```bash
# SIGKILL: process không có cơ hội cleanup (flush buffer, close connections)
# Workflow đúng:
kill PID                    # SIGTERM: thử graceful
sleep 5
kill -0 PID && kill -9 PID  # nếu còn sống thì force kill
```

**2. `ps aux | grep process` tự match lệnh grep**
```bash
ps aux | grep "nginx"        # có thể match cả "grep nginx"
ps aux | grep "[n]ginx"      # ĐÚNG: regex trick
ps aux | grep -v grep | grep nginx  # cách thông thường
pgrep nginx                  # TỐTNHẤT: dùng pgrep
```

**3. Process D state không thể kill**
```bash
# D state = uninterruptible sleep, thường do I/O hang (disk, NFS)
kill -9 PID   # KHÔNG CÓ TÁC DỤNG với D state
# Cần: fix I/O problem, umount NFS bị hung, hoặc reboot
```

**4. Zombie process — kill sai chỗ**
```bash
# Zombie đã dead, không thể kill
# Cần kill PARENT process để nó gọi wait()
ps -o ppid= -p ZOMBIE_PID   # tìm parent
kill -CHLD PPID             # gửi SIGCHLD — trigger parent gọi wait()
# Nếu parent ignore, kill parent
```

**5. `nohup` không hoạt động khi quên `&`**
```bash
nohup ./script.sh   # vẫn foreground! Ctrl+C vẫn kill
nohup ./script.sh & # ĐÚNG: background + ignore SIGHUP
```

**6. Nice value không tương đương với CPU percentage**
```bash
# Nice chỉ ảnh hưởng khi có CPU contention
# Nếu CPU rảnh, nice=19 vẫn chạy full speed
# nice chỉ có nghĩa khi nhiều process cùng compete CPU
```

---

## Câu hỏi phỏng vấn hay gặp

**Q1: Sự khác biệt giữa SIGTERM và SIGKILL?**
> SIGTERM (15): signal "mềm" — process có thể catch, handle, cleanup rồi exit (close connections, flush buffers). SIGKILL (9): kernel kill ngay lập tức, không thể catch/ignore/block. Chỉ dùng SIGKILL khi SIGTERM không có tác dụng sau timeout hợp lý.

**Q2: Zombie process là gì, tại sao nó tồn tại?**
> Khi process exit, kernel giữ lại một entry nhỏ trong process table chứa exit status, PID. Entry này tồn tại đến khi parent gọi `wait()` syscall để "reap" con. Zombie không dùng memory hay CPU, chỉ chiếm PID. Nhiều zombie = parent có bug hoặc đang bận. Fix: kill parent hoặc SIGCHLD.

**Q3: Load average trên Linux nghĩa là gì? Load 4.0 trên máy 4 core có tệ không?**
> Load average là số process đang R (running/runnable) + D (uninterruptible wait) trong 1/5/15 phút qua. Load = số cores nghĩa là CPU hoàn toàn busy (không idle). Load 4.0 trên 4 cores = 100% utilization — không tệ nếu sustainable. Load > 4.0 trên 4 cores = CPU bottleneck. Load cao do D state có thể là I/O bottleneck.

**Q4: `fork()` vs `exec()` syscall?**
> `fork()` tạo bản copy của process hiện tại (parent + child cùng chạy từ sau fork). `exec()` thay thế toàn bộ process image bằng program mới (không tạo process mới). Shell chạy lệnh: `fork()` tạo child, child gọi `exec()` để chạy lệnh. Parent gọi `wait()` để chờ child.

**Q5: Tại sao `kill -9` đôi khi không kill được process?**
> Hai trường hợp: 1) Process ở D state (uninterruptible sleep) — kernel không deliver SIGKILL khi process không chạy ở user mode. 2) Process bị "frozen" trong kernel code path quan trọng. Giải pháp: fix underlying I/O issue, hoặc reboot.

**Q6: `/proc/[PID]/fd` dùng để làm gì?**
> Là symlinks tới tất cả file descriptors đang mở. FD 0=stdin, 1=stdout, 2=stderr. Hữu ích: recover file đã bị `rm` nhưng vẫn đang được process giữ open: `cat /proc/PID/fd/FD_NUMBER > recovered_file`. Cũng dùng để debug: leak FD (quá nhiều symlinks), xem process đang kết nối socket nào.
