# Tổng quan Networking — Mạng máy tính

---

## Roadmap học Networking

### Bước 1 — Mô hình & Giao thức cơ bản
- **OSI Model** — 7 tầng, chức năng từng tầng, ví dụ protocol
- **TCP/IP Model** — 4 tầng, so sánh với OSI
- **IP Address** — IPv4 vs IPv6, subnet mask, CIDR notation
- **MAC Address** — L2 address, ARP protocol

### Bước 2 — Tầng Transport
- **TCP** — 3-way handshake, 4-way termination, flow control, congestion control
- **UDP** — connectionless, khi nào dùng UDP thay TCP
- **Port** — well-known ports (80, 443, 22, 53, 25...)
- **Socket** — IP + Port, TCP vs UDP socket

### Bước 3 — Tầng Application
- **HTTP/HTTPS** — request/response, methods (GET/POST/PUT/DELETE), status codes
- **DNS** — resolution process, recursive vs iterative, caching
- **TLS/SSL** — handshake, certificate, symmetric vs asymmetric encryption
- **WebSocket** — so sánh với HTTP, khi nào dùng

### Bước 4 — Networking nâng cao
- **Load Balancing** — L4 vs L7, round robin, least connections
- **CDN** — edge server, caching strategy
- **Firewall & NAT** — packet filtering, SNAT/DNAT
- **VPN** — tunneling, IPSec, OpenVPN

---

## OSI Model — Bảng 7 tầng

| Tầng | Tên | Chức năng | Protocol/Thiết bị |
|------|-----|-----------|-------------------|
| 7 | Application | Giao tiếp với ứng dụng | HTTP, FTP, SMTP, DNS |
| 6 | Presentation | Mã hóa, nén, format | TLS, SSL, JPEG |
| 5 | Session | Quản lý phiên kết nối | NetBIOS, RPC |
| 4 | Transport | End-to-end delivery, port | TCP, UDP |
| 3 | Network | Routing, IP address | IP, ICMP, Router |
| 2 | Data Link | Frame, MAC address | Ethernet, Switch |
| 1 | Physical | Bit transmission | Cable, Hub, NIC |

**Ghi nhớ nhanh (từ dưới lên):** "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way"

---

## TCP vs UDP

| | TCP | UDP |
|-|-----|-----|
| Kết nối | Connection-oriented (3-way handshake) | Connectionless |
| Độ tin cậy | Guaranteed delivery, ordering | Best effort |
| Tốc độ | Chậm hơn | Nhanh hơn |
| Header size | 20 bytes | 8 bytes |
| Flow control | Có (sliding window) | Không |
| Dùng khi | HTTP, Email, File transfer | Video stream, Gaming, DNS |

---

## HTTP Status Codes — Nhóm quan trọng

| Code | Ý nghĩa | Ví dụ |
|------|---------|-------|
| 200 | OK | GET thành công |
| 201 | Created | POST tạo resource mới |
| 301 | Moved Permanently | Redirect vĩnh viễn |
| 304 | Not Modified | Cache còn hợp lệ |
| 400 | Bad Request | Request sai format |
| 401 | Unauthorized | Chưa authenticate |
| 403 | Forbidden | Đã auth nhưng không có quyền |
| 404 | Not Found | Resource không tồn tại |
| 429 | Too Many Requests | Rate limit |
| 500 | Internal Server Error | Server lỗi |
| 503 | Service Unavailable | Server overload/down |

---

## Mã giả — Hỏi output là gì?

### Bài 1 — TCP 3-Way Handshake (dạng trace)

```
Client                    Server
  |                          |
  |--- SYN (seq=100) ------->|
  |                          |
  |<-- SYN-ACK (seq=200,  ---|
  |    ack=101) -----------  |
  |                          |
  |--- ACK (ack=201) ------->|
  |                          |
  [Connection Established]

Câu hỏi: Sau khi handshake, Client gửi data với seq=101.
Server nhận xong, ACK số bao nhiêu?
```

> **Output/Đáp án:** ACK = seq + length of data  
> Nếu data = 50 bytes → ACK = 151  
> **Giải thích:** ACK number = seq number của byte tiếp theo server mong đợi. Client gửi bytes 101→150 (50 bytes) → server ACK = 151.

---

### Bài 2 — DNS Resolution (trace từng bước)

```
User gõ: www.example.com

Bước 1: Browser check cache → MISS
Bước 2: OS check /etc/hosts → MISS
Bước 3: Query DNS Resolver (8.8.8.8)
Bước 4: Resolver query Root DNS → trả về .com nameserver
Bước 5: Resolver query .com TLD → trả về example.com nameserver
Bước 6: Resolver query example.com NS → trả về 93.184.216.34
Bước 7: Resolver trả về 93.184.216.34 cho Client
Bước 8: Browser kết nối TCP tới 93.184.216.34:443

Hỏi: Bao nhiêu DNS query được thực hiện trong quá trình này?
```

> **Đáp án:** 3 query (Root → TLD → Authoritative)  
> **Giải thích:** Resolver hỏi 3 server theo thứ tự. Lần sau sẽ cache → chỉ cần 0 query cho TTL duration.

---

### Bài 3 — Subnet Calculation

```
Network: 192.168.1.0/24

Câu hỏi:
a) Có bao nhiêu host hợp lệ?
b) Địa chỉ broadcast là gì?
c) 192.168.1.200 có trong subnet này không?
```

> **Đáp án:**  
> a) 2^8 - 2 = **254 hosts** (trừ network address và broadcast)  
> b) **192.168.1.255**  
> c) **Có** — 192.168.1.x với /24 bao gồm 192.168.1.1 → 192.168.1.254

---

### Bài 4 — HTTP Request/Response trace

```
Client gửi:
GET /api/users/5 HTTP/1.1
Host: example.com
Authorization: Bearer abc123

Server trả về:
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=3600

{"id": 5, "name": "Alice"}

Hỏi:
a) Request này dùng method gì?
b) Status code bao nhiêu?
c) Browser có thể cache response này bao lâu?
d) Nếu user không có token hợp lệ, server nên trả code gì?
```

> **Đáp án:**  
> a) **GET** — đọc dữ liệu  
> b) **200 OK** — thành công  
> c) **3600 giây = 1 giờ** (max-age=3600)  
> d) **401 Unauthorized** — chưa/sai authentication

---

### Bài 5 — TCP vs UDP chọn cái nào?

```
Phân loại các ứng dụng sau nên dùng TCP hay UDP:

1. Gửi email
2. Video call (Zoom, Meet)
3. Tải file từ server
4. Online game (FPS)
5. DNS query
6. Streaming video (Netflix)
7. SSH remote terminal
8. Live auction (đấu giá trực tiếp)
```

> **Đáp án:**
> 1. TCP — email phải đến đúng thứ tự, không mất
> 2. UDP — chấp nhận mất vài frame, cần realtime
> 3. TCP — file phải toàn vẹn
> 4. UDP — latency quan trọng hơn reliability
> 5. UDP — query nhỏ, retransmit nếu cần
> 6. UDP/QUIC — buffer được, cần throughput cao
> 7. TCP — mỗi keystroke phải đến đúng thứ tự
> 8. TCP — giá đấu không được sai/mất

---

## Bảng Port quan trọng cần thuộc

| Port | Protocol | Dùng cho |
|------|----------|---------|
| 20/21 | FTP | File transfer |
| 22 | SSH | Remote terminal |
| 25 | SMTP | Gửi email |
| 53 | DNS | Domain resolution |
| 80 | HTTP | Web không mã hóa |
| 110 | POP3 | Nhận email |
| 143 | IMAP | Nhận email (sync) |
| 443 | HTTPS | Web có mã hóa |
| 3306 | MySQL | Database |
| 5432 | PostgreSQL | Database |
| 6379 | Redis | Cache |
| 27017 | MongoDB | Database |

---

## Câu hỏi tự test nhanh

1. OSI có bao nhiêu tầng? TCP/IP có bao nhiêu tầng? → 7 / 4
2. HTTP mặc định dùng port nào? HTTPS? → 80 / 443
3. 3-way handshake gồm những bước gì? → SYN → SYN-ACK → ACK
4. DNS dùng TCP hay UDP? → UDP (port 53), TCP khi transfer zone
5. 403 vs 401 khác nhau thế nào? → 401 chưa auth, 403 đã auth nhưng không có quyền
