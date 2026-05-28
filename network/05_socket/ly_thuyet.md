# Socket — Giao tiếp mạng tầng thấp

---

## Giải thích cho người mới hoàn toàn

Socket giống như **ổ cắm điện**. Ổ cắm có hai đầu — một đầu ở tường (server), một đầu ở phích cắm (client). Khi bạn cắm phích vào ổ, điện chạy qua — hai bên giao tiếp được với nhau.

Trong mạng máy tính: **socket** là điểm cuối (endpoint) của một kết nối. Một socket được xác định bởi:
- **Địa chỉ IP** — tương đương địa chỉ nhà
- **Port** — tương đương số phòng trong tòa nhà
- **Protocol** — TCP hoặc UDP

Khi một ứng dụng web server "mở socket" ở port 80, nó giống như người ngồi ở bàn tiếp tân và chờ khách đến gõ cửa.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Socket Types

| Type | Protocol | Đặc điểm | Dùng khi |
|------|----------|----------|----------|
| `SOCK_STREAM` | TCP | Connection-oriented, reliable, ordered, stream | HTTP, SSH, database |
| `SOCK_DGRAM` | UDP | Connectionless, unreliable, datagram | DNS, VoIP, gaming |
| `SOCK_RAW` | IP trực tiếp | Bypass TCP/UDP layer, cần root | Network tools (ping, nmap) |
| `AF_UNIX` | Unix Domain Socket | IPC trong cùng machine, không qua network stack | Nginx ↔ PHP-FPM, Docker |

### Socket Lifecycle (TCP)

```
Server                              Client
  |                                   |
socket()                          socket()
  |                                   |
bind(host, port)                      |
  |                                   |
listen(backlog)                       |
  |                                   |
accept() ←--- SYN ------  connect() --+
  |          SYN-ACK --→              |
  |          ACK  ←---                |
  |                                   |
recv() / send()  ←→  send() / recv()  |
  |                                   |
close()  ←--- FIN -----  close() ----+
```

**backlog**: số connection đang chờ trong queue (chưa được `accept()`). Nếu queue đầy → client nhận RST hoặc timeout.

### Blocking vs Non-blocking Socket

**Blocking (mặc định):**
```python
data = sock.recv(1024)  # thread bị block ở đây cho đến khi có data
```
- Đơn giản, dễ code
- Mỗi connection cần 1 thread → không scale với nhiều connection đồng thời

**Non-blocking:**
```python
sock.setblocking(False)
try:
    data = sock.recv(1024)
except BlockingIOError:
    pass  # không có data, thử lại sau
```
- Không block thread
- Phải polling liên tục → CPU waste
- Giải pháp: dùng I/O multiplexing

### I/O Multiplexing — Xử lý nhiều socket với 1 thread

| API | OS | Mô tả | Độ phức tạp |
|-----|----|-------|------------|
| `select()` | POSIX | Monitor ≤ 1024 fds, copy fd_set kernel/userspace mỗi lần | O(n) |
| `poll()` | POSIX | Không giới hạn fd, vẫn O(n) scan | O(n) |
| `epoll()` | Linux | Event-driven, chỉ trả về fd có event, O(1) per event | O(1) |
| `kqueue()` | BSD/macOS | Tương đương epoll trên macOS | O(1) |
| `IOCP` | Windows | I/O Completion Ports, async | O(1) |

**epoll flow:**
```python
epoll = select.epoll()
epoll.register(server_sock.fileno(), select.EPOLLIN)
# ... thêm client fds khi connect ...
events = epoll.poll(timeout=1)  # chỉ trả về fd có event, không scan toàn bộ
for fd, event in events:
    if event & select.EPOLLIN:
        # đọc data từ fd
```

### C10K Problem

Bài toán: làm thế nào handle **10,000 concurrent connections** với 1 server?

| Approach | Vấn đề | Giải pháp |
|----------|--------|-----------|
| Thread-per-connection | 10K threads = 10GB RAM (1MB stack/thread), context switch overhead | Thread pool |
| Thread pool | Pool size cố định, blocking I/O trong thread làm thread bị lock | Async I/O |
| Async + epoll | 1 thread xử lý nhiều connection, không block | Nginx, Node.js, asyncio |
| Async + coroutine | Code giống sync, chạy async | Python asyncio, Go goroutine |

### WebSocket vs HTTP

WebSocket: **bidirectional, persistent** connection. Bắt đầu từ HTTP upgrade:

```
Client → Server:
GET /ws HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13

Server → Client:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

Sau đó, cả hai chiều tự do gửi frames mà không cần request.

### Unix Domain Socket vs Network Socket

| | Unix Domain Socket | Network Socket |
|-|-------------------|---------------|
| Transport | File system path | IP + Port |
| Overhead | Không có network stack | Có network stack |
| Speed | ~2x nhanh hơn | Chậm hơn |
| Scope | Cùng machine | Bất kỳ đâu |
| Ví dụ | `/var/run/nginx.sock`, `/tmp/mysql.sock` | `127.0.0.1:3306` |

---

## Định nghĩa chính xác

**Socket** là abstraction của OS đại diện cho một endpoint của communication channel. Được tạo bởi `socket()` syscall, trả về **file descriptor**. Socket API (Berkeley Sockets, POSIX) là interface chuẩn để network programming. Socket được xác định bởi 5-tuple: `(src_ip, src_port, dst_ip, dst_port, protocol)`.

---

## Đặc điểm kỹ thuật / So sánh

| Đặc điểm | TCP Socket | UDP Socket | Unix Socket |
|-----------|-----------|-----------|-------------|
| Connection | Có | Không | Có (SOCK_STREAM) |
| Reliable | Có | Không | Có |
| Address | IP + Port | IP + Port | File path |
| Network | Có | Có | Không (local only) |
| Overhead | Medium | Low | Lowest |
| Use case | Client-server | Real-time | IPC trên cùng host |

---

## Code mẫu

```python
import socket
import select
import threading
import ssl

# ══════════════════════════════════════════════════
# 1. TCP Echo Server (blocking, multi-thread)
# ══════════════════════════════════════════════════
def handle_client(conn, addr):
    print(f"[+] Connected: {addr}")
    try:
        while True:
            data = conn.recv(4096)
            if not data:
                break
            conn.sendall(data)  # echo
    finally:
        conn.close()
        print(f"[-] Disconnected: {addr}")

def tcp_server_threaded(host='127.0.0.1', port=9000):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as srv:
        srv.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        srv.bind((host, port))
        srv.listen(10)
        print(f"[TCP Server] {host}:{port}")
        while True:
            conn, addr = srv.accept()
            t = threading.Thread(target=handle_client, args=(conn, addr))
            t.daemon = True
            t.start()

# ══════════════════════════════════════════════════
# 2. Non-blocking với select() — I/O Multiplexing
# ══════════════════════════════════════════════════
def tcp_server_select(host='127.0.0.1', port=9001):
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind((host, port))
    server.listen(10)
    server.setblocking(False)

    inputs = [server]   # đang monitor read
    outputs = []        # đang monitor write
    message_queues = {}

    print(f"[Select Server] {host}:{port}")
    while inputs:
        readable, writable, exceptional = select.select(inputs, outputs, inputs, timeout=1)

        for s in readable:
            if s is server:
                conn, addr = server.accept()
                conn.setblocking(False)
                inputs.append(conn)
                print(f"[+] {addr}")
            else:
                data = s.recv(1024)
                if data:
                    if s not in message_queues:
                        message_queues[s] = []
                    message_queues[s].append(data)
                    if s not in outputs:
                        outputs.append(s)
                else:
                    # Connection closed
                    if s in outputs:
                        outputs.remove(s)
                    inputs.remove(s)
                    s.close()

        for s in writable:
            if s in message_queues and message_queues[s]:
                data = message_queues[s].pop(0)
                s.sendall(data)
            else:
                outputs.remove(s)

# ══════════════════════════════════════════════════
# 3. UDP Server + Client
# ══════════════════════════════════════════════════
def udp_server(host='127.0.0.1', port=9002):
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as srv:
        srv.bind((host, port))
        print(f"[UDP Server] {host}:{port}")
        while True:
            data, addr = srv.recvfrom(1024)
            print(f"Received from {addr}: {data.decode()}")
            srv.sendto(data, addr)

def udp_client(host='127.0.0.1', port=9002):
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as sock:
        sock.sendto(b"Hello UDP!", (host, port))
        data, _ = sock.recvfrom(1024)
        print(f"Echo: {data.decode()}")

# ══════════════════════════════════════════════════
# 4. SSL/TLS Client
# ══════════════════════════════════════════════════
def tls_client(host='google.com', port=443):
    ctx = ssl.create_default_context()
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as raw_sock:
        raw_sock.connect((host, port))
        with ctx.wrap_socket(raw_sock, server_hostname=host) as tls_sock:
            print(f"TLS version: {tls_sock.version()}")
            print(f"Cipher: {tls_sock.cipher()}")
            # Gửi HTTP GET thủ công qua TLS
            tls_sock.sendall(
                f"GET / HTTP/1.1\r\nHost: {host}\r\nConnection: close\r\n\r\n".encode()
            )
            response = b""
            while True:
                chunk = tls_sock.recv(4096)
                if not chunk:
                    break
                response += chunk
            print(response[:200].decode(errors='replace'))

# ══════════════════════════════════════════════════
# 5. Unix Domain Socket (IPC)
# ══════════════════════════════════════════════════
import os

SOCKET_PATH = "/tmp/test.sock"

def unix_server():
    if os.path.exists(SOCKET_PATH):
        os.unlink(SOCKET_PATH)
    with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as srv:
        srv.bind(SOCKET_PATH)
        srv.listen(1)
        conn, _ = srv.accept()
        data = conn.recv(1024)
        print(f"[Unix Server] Received: {data.decode()}")
        conn.sendall(b"ACK")
        conn.close()

def unix_client():
    with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as sock:
        sock.connect(SOCKET_PATH)
        sock.sendall(b"Hello via Unix socket!")
        response = sock.recv(1024)
        print(f"[Unix Client] Response: {response.decode()}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**TCP Socket — dùng khi:**
- Cần reliable data transfer: web server, database, file transfer
- Implement giao thức custom cần ordering và reliability

**UDP Socket — dùng khi:**
- Latency quan trọng hơn reliability: game, VoIP, video stream
- Implement protocol trên UDP: DNS, QUIC, DTLS

**Unix Domain Socket — dùng khi:**
- IPC (Inter-Process Communication) trên cùng machine
- Performance-critical: Nginx ↔ app server, Redis trong container

**Non-blocking + select/epoll — dùng khi:**
- Cần handle nhiều connection đồng thời mà không tạo nhiều thread
- Implement async server, event loop

---

## So sánh với các abstraction cấp cao

| | Raw Socket | HTTP Library | WebSocket Library | asyncio |
|-|-----------|-------------|-------------------|---------|
| Tầng | L4 (Transport) | L7 (Application) | L7 trên HTTP | Event loop |
| Độ phức tạp | Cao | Thấp | Thấp | Medium |
| Flexibility | Tối đa | Hạn chế | Hạn chế | Tốt |
| Use case | Custom protocol | REST API | Real-time | Async apps |

---

## Lỗi thường gặp (Common Pitfalls)

- **Không `SO_REUSEADDR`**: sau khi server crash, port vẫn ở TIME_WAIT, không bind được ngay — phải chờ ~60s.
- **Không đọc hết data**: `recv(1024)` không đảm bảo nhận đủ 1024 bytes — loop cho đến khi nhận đủ.
- **Không đóng socket**: resource leak, file descriptor exhaustion.
- **Không handle partial send**: `send()` có thể không gửi hết data — dùng `sendall()`.
- **Blocking socket trong event loop**: một blocking call block toàn bộ event loop — dùng non-blocking hoặc thread pool.
- **Không set timeout**: server chết, client hang mãi — set `sock.settimeout(seconds)`.

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích TCP socket lifecycle: socket → bind → listen → accept → recv/send → close.
- Blocking vs non-blocking socket — sự khác biệt?
- select() vs epoll() — tại sao epoll hiệu quả hơn?
- C10K problem là gì? Giải pháp là gì?
- WebSocket upgrade từ HTTP diễn ra như thế nào?
- Unix Domain Socket khác Network Socket ở điểm nào?
- Tại sao dùng `SO_REUSEADDR`?
- `send()` vs `sendall()` — tại sao cần `sendall()`?
