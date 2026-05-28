# TCP/IP — Giao thức truyền thông cốt lõi của Internet

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn cần chuyển một bộ phim lớn (10GB) từ máy tính của mình sang máy bạn qua mạng. Bạn không thể gửi nguyên cả file một lúc — giống như không thể gửi một chiếc xe ô tô qua bưu điện nguyên vẹn. Bạn phải **tháo rời từng bộ phận** (chia thành các gói nhỏ), ghi số thứ tự lên mỗi bộ phận, gửi đi, rồi bên kia **lắp ráp lại** đúng thứ tự.

Đó chính xác là **TCP** — chia nhỏ dữ liệu thành "gói" (packet), đánh số, gửi đi, xác nhận đã nhận, và lắp ráp lại ở đầu kia. Nếu có gói bị thất lạc, TCP gửi lại.

**IP** là "hệ thống địa chỉ" — mỗi máy có một địa chỉ IP duy nhất, giống như địa chỉ nhà. IP biết "gói hàng này phải đến đâu".

**UDP** thì ngược lại — gửi rất nhanh nhưng không đảm bảo (như gửi tin nhắn, mất thì thôi). Dùng cho video call, game online.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### TCP — Transmission Control Protocol

TCP là **connection-oriented**, **reliable**, **ordered** protocol chạy ở Transport Layer (L4).

#### Three-Way Handshake (Kết nối)

```
Client                    Server
  |                          |
  |------- SYN (seq=x) ----> |   Client muốn kết nối, gửi số sequence ngẫu nhiên x
  |                          |
  |<-- SYN-ACK (seq=y, ack=x+1) -- |   Server đồng ý, gửi số sequence y + ACK x+1
  |                          |
  |--- ACK (seq=x+1, ack=y+1) --> |   Client xác nhận, kết nối thiết lập
  |                          |
  |======= Data Transfer ========>|
```

Tại sao cần 3 bước thay vì 2? Vì cần xác nhận **cả hai chiều** đều có thể gửi và nhận.

#### Four-Way Termination (Đóng kết nối)

```
Client                    Server
  |--- FIN ----------------> |   Client muốn đóng
  |<-- ACK ---------------- |   Server xác nhận
  |<-- FIN ---------------- |   Server cũng muốn đóng
  |--- ACK ----------------> |   Client xác nhận → TIME_WAIT 2MSL
```

**TIME_WAIT**: Client chờ 2×MSL (Maximum Segment Lifetime ≈ 60–120s) để đảm bảo ACK cuối đến Server.

#### Reliability Mechanisms

| Cơ chế | Mô tả |
|--------|-------|
| Sequence number | Đánh số từng byte, bên nhận sắp xếp lại đúng thứ tự |
| ACK (Acknowledgment) | Bên nhận xác nhận đã nhận đến byte nào |
| Retransmission | Nếu không nhận ACK sau timeout → gửi lại |
| Checksum | Phát hiện lỗi bit trong segment |
| Duplicate detection | Số sequence giúp phát hiện và bỏ duplicate |

#### Flow Control — Sliding Window

Tránh sender gửi quá nhanh làm receiver bị tràn bộ nhớ (buffer overflow):

```
Receiver advertises: rwnd = 64KB  (còn 64KB buffer)
Sender có thể gửi tối đa 64KB mà không cần chờ ACK
Sau mỗi ACK, window slide forward
```

#### Congestion Control

Tránh làm nghẽn mạng khi nhiều sender cùng gửi:

| Phase | Cơ chế | Mô tả |
|-------|--------|-------|
| Slow Start | cwnd = 1 MSS, tăng gấp đôi mỗi RTT | Khởi đầu thận trọng |
| Congestion Avoidance | cwnd tăng +1 MSS mỗi RTT | Sau khi vượt ssthresh |
| Fast Retransmit | 3 duplicate ACKs → gửi lại ngay | Không cần chờ timeout |
| Fast Recovery | Giảm cwnd xuống một nửa (thay vì về 1) | Sau fast retransmit |

**AIMD (Additive Increase, Multiplicative Decrease):** tăng tuyến tính, giảm theo hệ số (×0.5 khi mất gói).

#### TCP Header quan trọng

```
Source Port (16 bit) | Destination Port (16 bit)
Sequence Number (32 bit)
Acknowledgment Number (32 bit)
Data Offset | Flags (SYN, ACK, FIN, RST, PSH, URG) | Window Size
Checksum | Urgent Pointer
Options (MSS, Window Scale, SACK, Timestamps)
```

### UDP — User Datagram Protocol

- **Connectionless**: không có handshake, không trạng thái
- **Unreliable**: không đảm bảo giao hàng, không đảm bảo thứ tự
- **Low overhead**: header chỉ 8 bytes (vs TCP 20+ bytes)
- **No congestion control**: gửi nhanh nhất có thể

### IP — Internet Protocol

**IPv4:**
- 32-bit address, viết dạng 4 octet: `192.168.1.1`
- Tổng ≈ 4.3 tỷ địa chỉ (đã hết từ 2011)
- Có NAT (Network Address Translation) để "chia sẻ" IP

**IPv6:**
- 128-bit address: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- Tổng ≈ 3.4 × 10³⁸ địa chỉ
- Không cần NAT
- Built-in IPSec

**Subnetting & CIDR:**

```
192.168.1.0/24
  ├── Network: 192.168.1.0
  ├── Subnet Mask: 255.255.255.0
  ├── Host range: 192.168.1.1 – 192.168.1.254
  ├── Broadcast: 192.168.1.255
  └── Số host: 2^8 - 2 = 254

10.0.0.0/8    → 16.7 triệu hosts
172.16.0.0/12 → 1 triệu hosts
192.168.0.0/16 → 65,534 hosts (private ranges)
```

### Ports & Sockets

- **Well-known ports** (0–1023): FTP=21, SSH=22, Telnet=23, SMTP=25, DNS=53, HTTP=80, HTTPS=443
- **Registered ports** (1024–49151): MySQL=3306, PostgreSQL=5432, Redis=6379, MongoDB=27017
- **Dynamic/Ephemeral ports** (49152–65535): OS gán cho client connections

**Socket = (IP, Port, Protocol)** — định danh duy nhất của một connection.

---

## Định nghĩa chính xác

**TCP** (Transmission Control Protocol, RFC 9293): connection-oriented, reliable, stream-based transport protocol. Đảm bảo delivery, ordering, và error checking.

**UDP** (User Datagram Protocol, RFC 768): connectionless, unreliable, datagram-based transport protocol. Cung cấp minimal service — multiplexing qua port và checksum tùy chọn.

**IP** (Internet Protocol, RFC 791 cho IPv4, RFC 8200 cho IPv6): network-layer protocol xử lý logical addressing và packet routing. Connectionless và best-effort delivery.

---

## Đặc điểm kỹ thuật / So sánh TCP vs UDP

| Đặc điểm | TCP | UDP |
|-----------|-----|-----|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Đảm bảo (ACK + retransmission) | Không đảm bảo |
| Ordering | Có (sequence number) | Không |
| Flow control | Có (sliding window) | Không |
| Congestion control | Có (slow start, AIMD) | Không |
| Header size | 20–60 bytes | 8 bytes |
| Overhead | Cao | Thấp |
| Latency | Cao hơn | Thấp hơn |
| Use case | HTTP, SMTP, FTP, SSH | DNS, VoIP, video stream, gaming |
| Throughput | Thấp hơn (do ACK) | Cao hơn |
| Broadcast/Multicast | Không | Có |

---

## Code mẫu

```python
import socket
import threading

# ══════════════════════════════════════════════
# TCP Echo Server + Client
# ══════════════════════════════════════════════

def tcp_server(host='127.0.0.1', port=9000):
    """TCP Server: lắng nghe và echo lại"""
    server_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server_sock.bind((host, port))
    server_sock.listen(5)
    print(f"[TCP Server] Listening on {host}:{port}")

    conn, addr = server_sock.accept()
    print(f"[TCP Server] Connected by {addr}")

    while True:
        data = conn.recv(1024)
        if not data:
            break
        print(f"[TCP Server] Received: {data.decode()}")
        conn.sendall(data)  # echo lại

    conn.close()
    server_sock.close()


def tcp_client(host='127.0.0.1', port=9000):
    """TCP Client: kết nối và gửi message"""
    client_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_sock.connect((host, port))  # three-way handshake xảy ra ở đây

    messages = ["Hello, TCP!", "Second message", "Third"]
    for msg in messages:
        client_sock.sendall(msg.encode())
        response = client_sock.recv(1024)
        print(f"[TCP Client] Echo: {response.decode()}")

    client_sock.close()  # four-way termination xảy ra ở đây


# ══════════════════════════════════════════════
# UDP Echo Server + Client
# ══════════════════════════════════════════════

def udp_server(host='127.0.0.1', port=9001):
    """UDP Server: không cần handshake"""
    server_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    server_sock.bind((host, port))
    print(f"[UDP Server] Listening on {host}:{port}")

    data, addr = server_sock.recvfrom(1024)
    print(f"[UDP Server] Received from {addr}: {data.decode()}")
    server_sock.sendto(data, addr)
    server_sock.close()


def udp_client(host='127.0.0.1', port=9001):
    """UDP Client: gửi không cần connect trước"""
    client_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    client_sock.sendto(b"Hello, UDP!", (host, port))
    data, _ = client_sock.recvfrom(1024)
    print(f"[UDP Client] Echo: {data.decode()}")
    client_sock.close()


# ══════════════════════════════════════════════
# IP / Network utilities
# ══════════════════════════════════════════════
import ipaddress

def subnet_info(cidr: str):
    """Phân tích thông tin subnet từ CIDR notation"""
    network = ipaddress.IPv4Network(cidr, strict=False)
    print(f"Network:    {network.network_address}")
    print(f"Broadcast:  {network.broadcast_address}")
    print(f"Netmask:    {network.netmask}")
    print(f"Num hosts:  {network.num_addresses - 2}")
    print(f"First host: {list(network.hosts())[0]}")
    print(f"Last host:  {list(network.hosts())[-1]}")

subnet_info("192.168.1.0/24")
subnet_info("10.0.0.0/8")

# ── Chạy TCP demo
# server_thread = threading.Thread(target=tcp_server)
# server_thread.start()
# tcp_client()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**TCP — dùng khi:**
- Dữ liệu phải đến đầy đủ, đúng thứ tự: web (HTTP), email (SMTP/IMAP), file transfer (FTP/SFTP)
- Cần reliability: database queries, API calls, SSH
- Không ưu tiên tốc độ tuyệt đối

**TCP — không dùng khi:**
- Real-time audio/video (VoIP, video call) — packet cũ đến trễ còn tệ hơn mất hẳn
- Gaming (latency quan trọng hơn reliability)
- DNS (query nhỏ, UDP đủ dùng, dùng TCP chỉ khi response > 512 bytes)

**UDP — dùng khi:**
- Latency quan trọng hơn reliability
- Ứng dụng có thể tự xử lý mất gói (video codec tự recover)
- Multicast/Broadcast (streaming IPTV, mDNS)
- DNS, DHCP, SNMP

---

## So sánh với giao thức liên quan

| | TCP | UDP | QUIC | SCTP |
|-|-----|-----|------|------|
| Transport | TCP | UDP | UDP | IP trực tiếp |
| Reliable | Có | Không | Có | Có |
| Multiplexing | Không | Không | Có (streams) | Có |
| HOL blocking | Có | Không | Không | Không |
| Use case | General | Real-time | HTTP/3 | Telecom |

---

## Lỗi thường gặp (Common Pitfalls)

- **TIME_WAIT quá nhiều**: server xử lý nhiều connection ngắn sẽ bị hết ephemeral ports. Giải pháp: `SO_REUSEADDR`, `TCP_QUICKACK`, hoặc connection pooling.
- **Không đặt TCP_NODELAY cho game/VoIP**: mặc định Nagle's algorithm gộp các packet nhỏ → thêm latency không cần thiết.
- **Nhầm tưởng TCP đảm bảo toàn vẹn ứng dụng**: TCP chỉ đảm bảo byte stream đến nguyên vẹn, không đảm bảo message boundary. Phải tự implement framing (length prefix, delimiter).
- **UDP "mất gói là ổn"**: nếu 30% gói bị mất, video call vẫn crash. Phải implement FEC (Forward Error Correction) hoặc NACK.
- **Không tắt connection**: không close socket sau khi dùng → resource leak, port exhaustion.
- **Hardcode port 80/443**: process thường cần quyền root để bind port < 1024. Dùng reverse proxy (Nginx) thay thế.

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích TCP three-way handshake. Tại sao cần 3 bước thay vì 2?
- TCP four-way termination là gì? Tại sao cần 4 bước?
- TCP vs UDP — phân biệt và ví dụ use case.
- Flow control vs Congestion control khác nhau như thế nào?
- Slow start trong TCP congestion control hoạt động như thế nào?
- TIME_WAIT là gì? Tại sao tồn tại? Xử lý ra sao khi có quá nhiều?
- Subnetting: `/24` có bao nhiêu host? `/16`?
- IPv4 vs IPv6 — tại sao cần chuyển sang IPv6?
- Well-known port của HTTP, HTTPS, SSH, DNS là gì?
- Nagle's algorithm là gì? Khi nào cần tắt nó?
