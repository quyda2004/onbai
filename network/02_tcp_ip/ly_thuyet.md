# TCP/IP — Giao thức truyền thông cốt lõi của Internet

---

## Giải thích cho người mới hoàn toàn

Tưởng tượng bạn gửi một bức thư dài cho bạn bè ở xa. Vì phong bì chỉ chứa được một tờ giấy, bạn phải cắt bức thư thành nhiều mảnh nhỏ, đánh số thứ tự từng mảnh (tờ 1/10, tờ 2/10...) rồi gửi từng phong bì đi.

Người nhận thu thập đủ 10 phong bì, sắp xếp theo số thứ tự và ghép lại thành bức thư gốc. Nếu phong bì số 5 bị thất lạc, người nhận sẽ nhắn lại "tôi chưa nhận được tờ số 5" và bạn gửi lại tờ đó.

Đó chính xác là cách **TCP (Transmission Control Protocol)** hoạt động:
- Dữ liệu được cắt thành các **segment** nhỏ
- Mỗi segment được đánh số (sequence number)
- Người nhận xác nhận đã nhận (acknowledgment)
- Nếu mất gói tin → gửi lại tự động

**IP (Internet Protocol)** đóng vai trò như hệ thống bưu điện: nó biết cách định tuyến từng phong bì từ địa chỉ nguồn đến địa chỉ đích, dù phải qua nhiều trạm trung gian.

**TCP/IP** là sự kết hợp: IP lo việc định tuyến, TCP lo việc đảm bảo dữ liệu đến đúng và đủ.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### TCP là giao thức connection-oriented, reliable, ordered

TCP hoạt động ở **Transport Layer (Layer 4)** của mô hình OSI, cung cấp:
- **Reliable delivery**: mọi byte đều được xác nhận, mất thì gửi lại
- **Ordered delivery**: dữ liệu đến đúng thứ tự nhờ sequence number
- **Flow control**: tránh sender làm ngập receiver
- **Congestion control**: tránh làm nghẽn mạng

### TCP Header Structure (20 bytes tối thiểu)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
|       |           |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (nếu có)                           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

Các field quan trọng:
- **Source/Destination Port** (16-bit mỗi cái): xác định process trên host
- **Sequence Number** (32-bit): số thứ tự byte đầu tiên trong segment này
- **Acknowledgment Number** (32-bit): byte tiếp theo mà receiver mong muốn nhận
- **Window Size** (16-bit): số byte receiver có thể nhận thêm (flow control)
- **Flags** (6-bit): URG, ACK, PSH, RST, SYN, FIN
- **Checksum** (16-bit): kiểm tra lỗi header + data

### 3-Way Handshake — Thiết lập kết nối

```
Client                          Server
  |                               |
  |  SYN (seq=x)                  |
  |------------------------------>|   [Client: SYN_SENT]
  |                               |   [Server: SYN_RECEIVED]
  |  SYN-ACK (seq=y, ack=x+1)    |
  |<------------------------------|
  |  [Client: ESTABLISHED]        |
  |  ACK (ack=y+1)                |
  |------------------------------>|   [Server: ESTABLISHED]
  |                               |
  |  (Truyền dữ liệu)             |
```

- **SYN**: Client gửi ISN (Initial Sequence Number) x ngẫu nhiên
- **SYN-ACK**: Server xác nhận x+1, đồng thời gửi ISN y của mình
- **ACK**: Client xác nhận y+1 — kết nối hai chiều được thiết lập

ISN ngẫu nhiên để chống **TCP sequence prediction attack**.

### 4-Way Termination — Đóng kết nối

```
Client                          Server
  |                               |
  |  FIN (seq=u)                  |
  |------------------------------>|   [Client: FIN_WAIT_1]
  |                               |   [Server: CLOSE_WAIT]
  |  ACK (ack=u+1)                |
  |<------------------------------|   [Client: FIN_WAIT_2]
  |                               |
  |  (Server tiếp tục gửi data)   |
  |                               |
  |  FIN (seq=v)                  |
  |<------------------------------|   [Server: LAST_ACK]
  |  [Client: TIME_WAIT 2*MSL]    |
  |  ACK (ack=v+1)                |
  |------------------------------>|   [Server: CLOSED]
  |  (Sau 2*MSL)                  |
  |  [Client: CLOSED]             |
```

**TIME_WAIT** (2 * MSL = 2 * 60s = 120s): Chờ để đảm bảo ACK cuối cùng đến nơi và các segment cũ trong mạng hết hạn. Đây là lý do tại sao restart server nhanh đôi khi gặp lỗi "Address already in use" → dùng `SO_REUSEADDR`.

### Flow Control — Sliding Window

```
Sender window size = min(receiver_window, congestion_window)

Receiver quảng bá rwnd (receive window) trong mỗi ACK:
  rwnd = receive_buffer_size - (LastByteRcvd - LastByteRead)

Sender không được gửi quá:
  LastByteSent - LastByteAcked <= min(cwnd, rwnd)
```

- Receiver buffer đầy → rwnd = 0 → sender dừng gửi
- Receiver đọc xong dữ liệu → gửi Window Update

**Zero Window Probe**: Sender định kỳ gửi 1 byte để hỏi xem window đã mở chưa.

### Congestion Control — 4 Thuật toán

**1. Slow Start**
```
cwnd = 1 MSS
mỗi ACK nhận được: cwnd += 1 MSS  (tăng theo hàm mũ)
khi cwnd >= ssthresh: chuyển sang Congestion Avoidance
```

**2. Congestion Avoidance**
```
mỗi RTT (sau khi nhận đủ ACK của 1 window): cwnd += 1 MSS  (tăng tuyến tính)
```

**3. Fast Retransmit**
```
Nhận 3 duplicate ACKs → mất gói → gửi lại ngay (không chờ timeout)
ssthresh = cwnd / 2
cwnd = ssthresh + 3 MSS
```

**4. AIMD (Additive Increase, Multiplicative Decrease)**
- Tăng: cộng thêm 1 MSS mỗi RTT (additive increase)
- Giảm: chia đôi khi phát hiện mất gói (multiplicative decrease)

### Well-Known Port Numbers

| Port | Giao thức | Mô tả |
|------|-----------|-------|
| 20   | FTP-Data  | FTP data transfer |
| 21   | FTP       | FTP control |
| 22   | SSH       | Secure Shell |
| 23   | Telnet    | Remote terminal (không mã hóa) |
| 25   | SMTP      | Gửi email |
| 53   | DNS       | Domain Name System |
| 67/68| DHCP      | Dynamic Host Configuration |
| 80   | HTTP      | Web không mã hóa |
| 110  | POP3      | Nhận email |
| 143  | IMAP      | Nhận email (sync) |
| 443  | HTTPS     | Web mã hóa TLS |
| 3306 | MySQL     | Database |
| 5432 | PostgreSQL| Database |
| 6379 | Redis     | Cache |
| 27017| MongoDB   | Database |

---

## Định nghĩa chính xác

**TCP (Transmission Control Protocol)**: Giao thức tầng transport (RFC 793, cập nhật RFC 9293) cung cấp truyền dữ liệu tin cậy, có thứ tự, kiểm soát luồng và kiểm soát tắc nghẽn giữa hai tiến trình trên mạng IP.

**IP (Internet Protocol)**: Giao thức tầng network (RFC 791 cho IPv4, RFC 8200 cho IPv6) cung cấp định địa chỉ và định tuyến best-effort (không đảm bảo độ tin cậy) giữa các host.

**MSS (Maximum Segment Size)**: Kích thước tối đa của TCP payload trong một segment, thường = MTU - 40 bytes (IP header 20 + TCP header 20). Ethernet MTU = 1500 → MSS = 1460 bytes.

**RTT (Round-Trip Time)**: Thời gian để một packet đi từ sender đến receiver và ACK trở về.

---

## Bảng / Sơ đồ kỹ thuật

### TCP vs UDP So sánh đầy đủ

| Tiêu chí | TCP | UDP |
|----------|-----|-----|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed (ACK + retransmit) | Best-effort, có thể mất gói |
| Ordering | Đảm bảo thứ tự | Không đảm bảo |
| Flow Control | Có (sliding window) | Không |
| Congestion Control | Có (slow start, AIMD) | Không |
| Header size | 20–60 bytes | 8 bytes |
| Speed | Chậm hơn (overhead) | Nhanh hơn |
| Use cases | HTTP, FTP, SSH, email | DNS, DHCP, streaming, gaming, VoIP |
| Broadcast/Multicast | Không hỗ trợ | Hỗ trợ |

### TCP State Machine

```
                  CLOSED
                 /      \
         [passive    [active
          open]       open]
               |      |
           LISTEN   SYN_SENT
               |      |
       [SYN]  |      | [SYN+ACK]
               |      |
         SYN_RECEIVED  |
               |      |
         [ACK] |      | [ACK]
               \      /
              ESTABLISHED
              /          \
   [close]  /            \ [close/FIN]
            |              |
       FIN_WAIT_1      CLOSE_WAIT
            |              |
       FIN_WAIT_2      LAST_ACK
            |              |
        TIME_WAIT -------> CLOSED
            |
          CLOSED
```

---

## Code mẫu

### TCP Server và Client cơ bản (Python)

```python
# tcp_server.py
import socket
import threading

def handle_client(conn, addr):
    """Xử lý từng client trong thread riêng"""
    print(f"[+] Kết nối từ {addr}")
    try:
        while True:
            # recv() blocking: chờ tối đa 1024 bytes
            data = conn.recv(1024)
            if not data:
                break  # Client đã đóng kết nối
            
            message = data.decode('utf-8')
            print(f"[{addr}] Nhận: {message}")
            
            # Echo lại dữ liệu
            response = f"Server echo: {message}"
            conn.sendall(response.encode('utf-8'))
    finally:
        conn.close()
        print(f"[-] Đóng kết nối {addr}")

def start_server(host='127.0.0.1', port=9999):
    # AF_INET = IPv4, SOCK_STREAM = TCP
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
        # Tránh lỗi "Address already in use" khi restart
        server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        
        server.bind((host, port))
        server.listen(5)  # backlog = 5 (hàng đợi SYN)
        print(f"[*] Server đang lắng nghe tại {host}:{port}")
        
        while True:
            conn, addr = server.accept()  # Blocking: chờ kết nối mới
            # Mỗi client chạy trong thread riêng
            t = threading.Thread(target=handle_client, args=(conn, addr))
            t.daemon = True
            t.start()

if __name__ == '__main__':
    start_server()
```

```python
# tcp_client.py
import socket

def start_client(host='127.0.0.1', port=9999):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client:
        # 3-way handshake xảy ra ở đây
        client.connect((host, port))
        print(f"[+] Đã kết nối đến {host}:{port}")
        
        messages = ["Xin chào!", "TCP/IP test", "bye"]
        for msg in messages:
            client.sendall(msg.encode('utf-8'))
            response = client.recv(1024).decode('utf-8')
            print(f"Server trả lời: {response}")
        
    # with block thoát → close() → FIN/ACK tự động

if __name__ == '__main__':
    start_client()
```

### Xem TCP connections trên Linux

```bash
# Xem tất cả TCP connections
ss -tnp

# Xem trạng thái TIME_WAIT
ss -tn state time-wait

# Xem port đang listen
ss -tlnp

# Dùng netstat (cũ hơn)
netstat -tnp

# Bắt gói TCP để quan sát 3-way handshake
sudo tcpdump -i lo -n tcp port 9999 -S
```

### Phân tích TCP với Python (scapy)

```python
from scapy.all import *

# Bắt 10 gói TCP
packets = sniff(filter="tcp port 80", count=10)
for pkt in packets:
    if TCP in pkt:
        tcp = pkt[TCP]
        flags = tcp.flags
        print(f"Seq={tcp.seq}, Ack={tcp.ack}, Flags={flags}, Win={tcp.window}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng TCP khi:**
- Cần đảm bảo dữ liệu đến đúng và đủ (HTTP, file transfer, database)
- Thứ tự dữ liệu quan trọng (streaming media với buffering)
- Ứng dụng không thể tự xử lý mất gói
- Email (SMTP, IMAP, POP3)
- Remote access (SSH, Telnet)

**Dùng UDP thay vì TCP khi:**
- Cần độ trễ thấp hơn tính tin cậy (game real-time, VoIP)
- Ứng dụng tự xử lý mất gói (video call chấp nhận frame drop)
- DNS lookup (query nhỏ, retry ở application layer)
- Broadcast/Multicast (không thể dùng TCP)
- QUIC (HTTP/3) tự xây reliable transport trên UDP

**Không dùng TCP khi:**
- Dữ liệu nhỏ, nhiều lần, delay quan trọng hơn reliability
- Cần multicast
- IoT sensor gửi metric mà mất vài gói không sao

---

## Lỗi thường gặp (Common Pitfalls)

1. **"Address already in use"**: Quên set `SO_REUSEADDR` trước `bind()`. Xảy ra do TIME_WAIT state sau khi đóng server.

2. **Partial send**: `send()` không đảm bảo gửi hết dữ liệu. Luôn dùng `sendall()` hoặc kiểm tra return value.

3. **recv() không có delimiter**: TCP là byte stream, không có khái niệm "message boundary". Phải tự thiết kế protocol (length-prefix, newline delimiter...).

4. **Blocking forever**: `accept()` và `recv()` block vô hạn. Nên set timeout: `socket.settimeout(30)`.

5. **Half-open connection**: Một bên crash mà không gửi FIN → bên kia không biết. Dùng `SO_KEEPALIVE` hoặc application-level heartbeat.

6. **Nagle's Algorithm**: Tự động buffer các packet nhỏ để gộp. Gây delay với ứng dụng cần low-latency. Tắt bằng `TCP_NODELAY`.

7. **SYN flood attack**: Server tạo half-open connection cho mỗi SYN → cạn kiệt bộ nhớ. Giải pháp: SYN Cookies.

8. **Nhầm lẫn sequence number**: ACK number = sequence number của segment nhận được + length của data (không phải +1 trừ khi SYN/FIN).

---

## Câu hỏi phỏng vấn hay gặp

1. **Giải thích 3-way handshake và tại sao cần đúng 3 bước?**
   - 2 bước không đủ: Client không biết Server có nhận được ISN của mình không; cần ACK để xác nhận 2 chiều.

2. **Tại sao TCP termination cần 4 bước thay vì 3 bước?**
   - Vì TCP là full-duplex. Mỗi chiều phải đóng riêng (FIN + ACK). Server có thể vẫn còn data để gửi sau khi nhận FIN của Client.

3. **TIME_WAIT là gì và tại sao cần thiết?**
   - Đảm bảo ACK cuối cùng đến được Server và các segment cũ (delayed duplicates) hết hạn trên mạng. Thời gian = 2 * MSL (Maximum Segment Lifetime).

4. **Sự khác biệt giữa flow control và congestion control?**
   - Flow control: tránh receiver bị ngập (sender ↔ receiver). Congestion control: tránh mạng bị nghẽn (sender ↔ network).

5. **Slow start có thực sự "chậm" không?**
   - Không chậm theo nghĩa tuyến tính. Tăng theo hàm mũ (exponential) đến ssthresh, sau đó mới tuyến tính. Gọi là "slow" vì bắt đầu từ cwnd=1 thay vì full bandwidth.

6. **Tại sao UDP nhanh hơn TCP?**
   - Không handshake, không ACK, không retransmit, không flow/congestion control, header nhỏ hơn (8 vs 20 bytes).

7. **TCP có đảm bảo data không bị corrupted không?**
   - Có checksum nhưng chỉ 16-bit, không đủ mạnh. Ứng dụng cần TLS hoặc application-level integrity check.

8. **Điều gì xảy ra khi gửi data qua TCP connection đã bị đóng?**
   - Nhận `RST` (Connection Reset) → exception `ConnectionResetError` hoặc `BrokenPipeError`.
