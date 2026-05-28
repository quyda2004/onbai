# Socket — Giao tiếp mạng ở tầng thấp nhất

---

## Giải thích cho người mới hoàn toàn

Tưởng tượng bạn muốn nói chuyện điện thoại với bạn bè. Để gọi được, cần hai thứ:
1. **Số điện thoại** (địa chỉ IP): biết gọi đến nhà nào
2. **Số phòng** (Port): biết gặp ai trong nhà đó (ví dụ: gia đình có ông bà, bố mẹ, con cái — mỗi người dùng một "nhánh máy" khác nhau)

**Socket** là chiếc điện thoại hai đầu đó. Nó là "ổ cắm" kết nối hai chương trình lại với nhau qua mạng — một đầu ở máy A, một đầu ở máy B.

Khi bạn dùng ứng dụng chat, xem video YouTube, hay gửi email — tất cả đều chạy qua socket bên dưới. Lập trình viên dùng socket để viết phần "đường dây điện thoại" đó.

**WebSocket** khác với socket thông thường: WebSocket giống như điện thoại có loa ngoài — cả hai bên có thể nói chuyện cùng lúc mà không cần ai "gọi trước" mỗi lần.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Socket là gì về mặt kỹ thuật?

Socket là **abstraction của OS** đại diện cho một endpoint giao tiếp mạng. Về cơ bản là một file descriptor (Linux: `int fd`) kết hợp với:
- **Protocol family**: AF_INET (IPv4), AF_INET6 (IPv6), AF_UNIX (local)
- **Socket type**: SOCK_STREAM (TCP), SOCK_DGRAM (UDP), SOCK_RAW
- **Địa chỉ**: IP + Port number (5-tuple: protocol, src_ip, src_port, dst_ip, dst_port)

Socket API (Berkeley Sockets) được định chuẩn bởi POSIX, available trên tất cả Unix-like OS và Windows (WinSock).

### TCP Socket Lifecycle

```
SERVER SIDE                          CLIENT SIDE
                                     
socket()                             socket()
   |                                    |
bind()                                  |
   |                                    |
listen()                                |
   |                                    |
accept() ← BLOCKING ←─────────── connect() ← 3-way handshake
   |                                    |
recv() / send()                     send() / recv()
   |                                    |
close()                              close()

Kernel queues (trong listen()):
┌─────────────────────────────────────┐
│ SYN Queue (incomplete connections)  │ ← SYN received, waiting SYN-ACK ACK
│   [backlog/2 entries typically]     │
└─────────────────────────────────────┘
┌─────────────────────────────────────┐
│ Accept Queue (completed connections)│ ← 3-way handshake done, waiting accept()
│   [backlog entries]                 │
└─────────────────────────────────────┘
```

**Quan trọng**: `listen(backlog)` — `backlog` là kích thước của accept queue, không phải số client tổng cộng. Nếu accept queue đầy, client mới nhận SYN timeout hoặc RST.

### UDP Socket Lifecycle

```
SERVER                               CLIENT
                                     
socket()                             socket()
   |                                    |
bind()                             (bind tùy chọn)
   |                                    |
recvfrom() ← BLOCKING               sendto(server_addr, data)
   |                                    |
sendto(client_addr, response)       recvfrom() ← data hoặc timeout
```

UDP không có "connection" — `sendto()` và `recvfrom()` mang cả địa chỉ. Không có trạng thái kết nối.

### Blocking vs Non-blocking Socket

**Blocking (default)**:
```
recv() ──► kernel ──► dữ liệu chưa có ──► thread bị suspend ──► dữ liệu đến ──► thread được resume
```
- Thread bị block hoàn toàn, không làm được gì khác
- Đơn giản để lập trình, nhưng cần 1 thread/connection

**Non-blocking**:
```python
import socket
sock = socket.socket()
sock.setblocking(False)

try:
    data = sock.recv(1024)
except BlockingIOError:
    # Không có data ngay lúc này, xử lý việc khác
    pass
```
- `recv()` trả về `EAGAIN`/`EWOULDBLOCK` ngay nếu không có data
- Phải poll liên tục → CPU lãng phí nếu không kết hợp với I/O multiplexing

### I/O Multiplexing — select/poll/epoll

**Vấn đề**: Xử lý 10,000 connection với 1 thread?

**select** (POSIX, tất cả OS):
```python
import select

readable, _, _ = select.select([sock1, sock2, sock3], [], [], timeout=1.0)
for s in readable:
    data = s.recv(1024)
```
- Giới hạn FD_SETSIZE (thường 1024) file descriptors
- O(n) mỗi call: scan toàn bộ fd_set
- Phải copy fd_set từ userspace vào kernel mỗi lần

**poll** (POSIX):
```python
import select
poll_obj = select.poll()
poll_obj.register(sock.fileno(), select.POLLIN)

events = poll_obj.poll(1000)  # timeout ms
for fd, event in events:
    if event & select.POLLIN:
        # Data available
```
- Không giới hạn FD số lượng
- Vẫn O(n) scan

**epoll** (Linux only, BEST):
```python
import select

epoll = select.epoll()
epoll.register(server_sock.fileno(), select.EPOLLIN)
conn_map = {}

while True:
    events = epoll.poll(timeout=1)  # O(1) - chỉ return ready FDs
    for fd, event in events:
        if fd == server_sock.fileno():
            conn, addr = server_sock.accept()
            conn.setblocking(False)
            epoll.register(conn.fileno(), select.EPOLLIN)
            conn_map[conn.fileno()] = conn
        elif event & select.EPOLLIN:
            data = conn_map[fd].recv(1024)
            if data:
                conn_map[fd].sendall(data)
            else:
                epoll.unregister(fd)
                conn_map[fd].close()
                del conn_map[fd]
```
- O(1): kernel thông báo FD nào ready, không scan toàn bộ
- Edge-triggered (EPOLLET) vs Level-triggered mode
- Nền tảng của nginx, Node.js, asyncio

### Socket Options

```python
import socket

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# SO_REUSEADDR: cho phép bind lại port ngay sau khi đóng (bỏ qua TIME_WAIT)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# SO_REUSEPORT: nhiều socket bind cùng port (load balancing giữa processes)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEPORT, 1)

# SO_KEEPALIVE: gửi keepalive probe khi connection idle
sock.setsockopt(socket.SOL_SOCKET, socket.SO_KEEPALIVE, 1)
# Trên Linux, cấu hình keepalive timing:
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPIDLE, 60)   # Idle 60s trước khi probe
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPINTVL, 10)  # Probe mỗi 10s
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPCNT, 6)     # Tối đa 6 probe

# TCP_NODELAY: tắt Nagle's algorithm (gửi ngay, không buffer)
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)

# SO_SNDBUF / SO_RCVBUF: kích thước buffer
sock.setsockopt(socket.SOL_SOCKET, socket.SO_SNDBUF, 65536)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_RCVBUF, 65536)

# Timeout
sock.settimeout(30.0)  # seconds (None = blocking, 0 = non-blocking)
```

### WebSocket vs HTTP Socket

| Tiêu chí | HTTP (TCP Socket) | WebSocket |
|----------|-------------------|-----------|
| Protocol | HTTP request/response | WebSocket protocol (RFC 6455) |
| Direction | Half-duplex (request-response) | Full-duplex (bidirectional) |
| Handshake | HTTP request | HTTP Upgrade handshake |
| Connection | Short-lived (HTTP/1.1 keepalive) | Long-lived, persistent |
| Overhead | Header mỗi request | Header nhỏ sau handshake |
| Use case | REST API, file download | Real-time chat, game, dashboard |
| Port | 80/443 | 80/443 (ws:// / wss://) |

WebSocket Handshake:
```http
# Client gửi HTTP Upgrade request:
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13

# Server response:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
# Sau đây là WebSocket frames, không còn HTTP
```

---

## Định nghĩa chính xác

**Socket**: Một endpoint truyền thông trong mạng máy tính, được xác định bởi một địa chỉ IP và một port number. Cung cấp interface lập trình (API) để các tiến trình trên các máy khác nhau có thể giao tiếp qua mạng theo mô hình Berkeley Sockets (POSIX).

**Port number**: Số 16-bit (0–65535) xác định tiến trình ứng dụng trên một host. Well-known ports: 0–1023, Registered ports: 1024–49151, Dynamic/ephemeral ports: 49152–65535.

**File descriptor**: Số nguyên đại diện cho một tài nguyên đang mở trong kernel (file, socket, pipe...). Trên Linux, socket là file descriptor.

---

## Bảng / Sơ đồ kỹ thuật

### So sánh I/O Models

| Model | Blocking? | CPU Usage | Scalability | Complexity |
|-------|-----------|-----------|-------------|-----------|
| Blocking I/O | Có | Thấp | Kém (1 thread/conn) | Thấp |
| Non-blocking (polling) | Không | Cao | Trung bình | Trung bình |
| select/poll | Không | Trung bình | Trung bình (O(n)) | Trung bình |
| epoll (Linux) | Không | Thấp | Cao (O(1)) | Cao |
| Async I/O (io_uring) | Không | Rất thấp | Rất cao | Rất cao |

### Socket Address Families

```
AF_INET  + SOCK_STREAM  → TCP over IPv4
AF_INET  + SOCK_DGRAM   → UDP over IPv4
AF_INET6 + SOCK_STREAM  → TCP over IPv6
AF_INET6 + SOCK_DGRAM   → UDP over IPv6
AF_UNIX  + SOCK_STREAM  → Unix Domain Socket (local IPC)
AF_UNIX  + SOCK_DGRAM   → Unix Domain Socket UDP-like
```

Unix Domain Socket nhanh hơn TCP localhost vì không đi qua network stack.

---

## Code mẫu

### TCP Server + Client đầy đủ (Python)

```python
# ============================================================
# tcp_server.py — Echo server với epoll (Linux) hoặc select
# ============================================================
import socket
import select
import sys

def run_tcp_server(host: str = '0.0.0.0', port: int = 9999):
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind((host, port))
    server.listen(128)  # backlog = 128
    server.setblocking(False)
    
    print(f"[*] Server listening on {host}:{port}")
    
    # I/O multiplexing với select (cross-platform)
    inputs = [server]
    
    while True:
        readable, _, exceptional = select.select(inputs, [], inputs, 1.0)
        
        for s in readable:
            if s is server:
                # Kết nối mới
                conn, addr = server.accept()
                conn.setblocking(False)
                inputs.append(conn)
                print(f"[+] New connection from {addr[0]}:{addr[1]}")
            else:
                # Dữ liệu từ client đã kết nối
                try:
                    data = s.recv(4096)
                    if data:
                        print(f"[<] Received {len(data)} bytes: {data[:50]!r}")
                        # Echo back
                        s.sendall(data)
                    else:
                        # EOF: client đóng kết nối
                        print(f"[-] Client disconnected")
                        inputs.remove(s)
                        s.close()
                except ConnectionResetError:
                    inputs.remove(s)
                    s.close()
        
        for s in exceptional:
            inputs.remove(s)
            s.close()


# ============================================================
# tcp_client.py — Client với timeout và proper error handling
# ============================================================
import socket
import struct

def message_with_length_prefix(data: bytes) -> bytes:
    """Thêm 4-byte length prefix để giải quyết TCP byte stream problem"""
    return struct.pack('>I', len(data)) + data

def recv_exactly(sock: socket.socket, n: int) -> bytes:
    """Nhận đúng n bytes từ socket"""
    data = bytearray()
    while len(data) < n:
        packet = sock.recv(n - len(data))
        if not packet:
            raise ConnectionError("Connection closed prematurely")
        data.extend(packet)
    return bytes(data)

def recv_message(sock: socket.socket) -> bytes:
    """Nhận một message hoàn chỉnh với length prefix"""
    raw_len = recv_exactly(sock, 4)
    msg_len = struct.unpack('>I', raw_len)[0]
    return recv_exactly(sock, msg_len)

def run_tcp_client(host: str = '127.0.0.1', port: int = 9999):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
        sock.settimeout(10)  # Connection timeout
        
        try:
            sock.connect((host, port))
            print(f"[+] Connected to {host}:{port}")
            
            # Lấy địa chỉ local của socket này
            local_addr = sock.getsockname()
            print(f"[*] Local address: {local_addr[0]}:{local_addr[1]}")
            
            # Gửi messages
            for i in range(3):
                message = f"Hello #{i} from client"
                sock.sendall(message.encode())
                
                response = sock.recv(4096)
                print(f"[>] Sent: {message}")
                print(f"[<] Recv: {response.decode()}")
        
        except socket.timeout:
            print("[-] Connection timed out")
        except ConnectionRefusedError:
            print(f"[-] Cannot connect to {host}:{port}")


if __name__ == '__main__':
    if len(sys.argv) > 1 and sys.argv[1] == 'client':
        run_tcp_client()
    else:
        run_tcp_server()
```

### UDP Server + Client

```python
# udp_demo.py
import socket
import threading

def udp_server(host: str = '0.0.0.0', port: int = 9998):
    """UDP server: không cần listen/accept"""
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as sock:
        sock.bind((host, port))
        print(f"[UDP] Server listening on {host}:{port}")
        
        while True:
            # recvfrom trả về (data, (client_ip, client_port))
            data, client_addr = sock.recvfrom(65535)  # Max UDP payload
            print(f"[UDP] From {client_addr}: {data.decode()}")
            
            # Gửi reply trực tiếp về client
            reply = f"UDP echo: {data.decode()}"
            sock.sendto(reply.encode(), client_addr)

def udp_client(host: str = '127.0.0.1', port: int = 9998):
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as sock:
        sock.settimeout(3.0)
        
        for i in range(3):
            message = f"UDP message {i}"
            # Không cần connect(), sendto trực tiếp
            sock.sendto(message.encode(), (host, port))
            
            try:
                data, server_addr = sock.recvfrom(65535)
                print(f"[UDP] Reply from {server_addr}: {data.decode()}")
            except socket.timeout:
                print(f"[UDP] Timeout waiting for reply #{i}")

# Server trong background thread
t = threading.Thread(target=udp_server, daemon=True)
t.start()

import time
time.sleep(0.1)
udp_client()
```

### Unix Domain Socket (IPC local)

```python
# unix_socket_demo.py
import socket
import os
import threading

SOCKET_PATH = '/tmp/my_app.sock'

def unix_server():
    # Xóa socket file cũ nếu còn
    if os.path.exists(SOCKET_PATH):
        os.unlink(SOCKET_PATH)
    
    with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as sock:
        sock.bind(SOCKET_PATH)
        sock.listen(5)
        print(f"[Unix] Listening on {SOCKET_PATH}")
        
        conn, _ = sock.accept()
        with conn:
            data = conn.recv(1024)
            print(f"[Unix] Received: {data.decode()}")
            conn.sendall(b"Pong!")

def unix_client():
    import time; time.sleep(0.1)
    with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as sock:
        sock.connect(SOCKET_PATH)
        sock.sendall(b"Ping!")
        reply = sock.recv(1024)
        print(f"[Unix] Reply: {reply.decode()}")

threading.Thread(target=unix_server, daemon=True).start()
unix_client()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng TCP socket khi:**
- Cần reliable, ordered delivery (hầu hết ứng dụng)
- File transfer, database connection, web server
- Khi dữ liệu không thể mất

**Dùng UDP socket khi:**
- Cần low latency hơn reliability (VoIP, game, DNS)
- Streaming (mất frame tốt hơn delay)
- Broadcast/Multicast trong LAN
- Tự xây reliable protocol (QUIC)

**Dùng Unix Domain Socket khi:**
- IPC giữa processes trên cùng máy (nginx ↔ PHP-FPM, app ↔ database)
- Nhanh hơn TCP localhost (không qua network stack)
- Không cần port number

**Dùng epoll thay select khi:**
- Linux và cần xử lý >1000 connections concurrent
- High-performance server (dùng asyncio/uvloop đã có epoll sẵn)

**Dùng abstraction cao hơn khi:**
- asyncio (Python), Netty (Java), libuv (Node.js): đã tích hợp epoll/kqueue/IOCP
- HTTP framework: không cần raw socket

---

## Lỗi thường gặp (Common Pitfalls)

1. **Partial send/recv**: `send()` có thể gửi ít hơn yêu cầu. `recv()` có thể trả về ít hơn `bufsize`. Luôn dùng `sendall()` và loop để nhận đủ.

2. **Message boundary với TCP**: TCP là byte stream không có message delimiter. Phải tự định nghĩa protocol: length-prefix, newline, HTTP-style header+body.

3. **Quên close socket**: Gây resource leak (file descriptor). Dùng `with socket.socket() as s:` hoặc try/finally.

4. **Không set SO_REUSEADDR**: Restart server gặp "Address already in use" do TIME_WAIT. Luôn set trước `bind()`.

5. **backlog quá nhỏ**: `listen(1)` với server nhiều client → SYN drop. Dùng 128 hoặc `socket.SOMAXCONN`.

6. **Không handle EINTR**: System call bị interrupt bởi signal. Trong Python 3, `socket` tự retry, nhưng trong C phải check `errno == EINTR`.

7. **Thread per connection không scalable**: 10,000 connections = 10,000 threads = RAM thấp, context switch nhiều. Dùng event-driven (epoll) hoặc asyncio.

8. **Nagle's Algorithm gây latency**: Nhỏ nhiều writes → delay trước khi gửi. Set `TCP_NODELAY = 1` cho interactive ứng dụng.

9. **UDP buffer overflow bị mất gói**: OS buffer đầy → gói bị drop silently. Tăng `SO_RCVBUF` và xử lý nhanh.

---

## Câu hỏi phỏng vấn hay gặp

1. **Socket là gì? Khác gì với port?**
   - Socket = IP + Port = endpoint hoàn chỉnh. Port là số xác định service trên host. Socket là abstraction OS để giao tiếp, bao gồm cả state (connected/listening) và buffers.

2. **Tại sao cần SO_REUSEADDR?**
   - TCP TIME_WAIT giữ socket 2*MSL sau khi đóng. Mà không có REUSEADDR, `bind()` trên port đó sẽ fail. SO_REUSEADDR cho phép bind ngay cả khi có TIME_WAIT socket.

3. **Làm sao xử lý 10,000 concurrent connections?**
   - Không dùng 1 thread/connection. Dùng I/O multiplexing: epoll (Linux) / kqueue (BSD) / IOCP (Windows). Hoặc async framework như asyncio, Netty, Node.js đã làm sẵn.

4. **TCP byte stream là gì và làm sao giải quyết?**
   - TCP không giữ message boundaries. Phải tự protocol: (a) length-prefix header, (b) delimiter như `\r\n`, (c) fixed-size messages.

5. **Khác nhau giữa select, poll, epoll?**
   - select: giới hạn 1024 fd, O(n), copy fd_set vào kernel mỗi lần. poll: không giới hạn fd, O(n). epoll: O(1), kernel callback khi fd ready, không copy, chỉ Linux.

6. **Nagle's Algorithm là gì?**
   - Buffer nhiều write nhỏ thành 1 segment lớn để tăng efficiency. Nhưng gây latency cho interactive app. Tắt bằng TCP_NODELAY.

7. **Khác nhau giữa TCP socket và WebSocket?**
   - TCP socket: transport layer, raw bytes. WebSocket: application layer protocol chạy trên HTTP, sau đó upgrade sang full-duplex, có framing. WebSocket dành cho web browser, TCP socket cho general purpose.

8. **Unix Domain Socket vs TCP localhost?**
   - UDS nhanh hơn: không qua TCP/IP stack, không checksum, không congestion control. Chỉ dùng được khi cùng máy. Nginx ↔ app thường dùng UDS.
