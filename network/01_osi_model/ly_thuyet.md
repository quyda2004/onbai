# OSI Model — Mô hình 7 tầng

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn gửi một bức thư tay cho người bạn ở thành phố khác. Quá trình đó trải qua nhiều bước:

1. Bạn **viết nội dung** thư (tạo dữ liệu)
2. Bạn **bỏ vào phong bì**, ghi địa chỉ người nhận (đóng gói)
3. Bạn mang ra **bưu cục** (giao cho hệ thống vận chuyển)
4. Bưu cục **phân loại**, chọn **xe tải / máy bay** phù hợp (chọn đường đi)
5. Xe tải / máy bay **chở thư** theo đúng tuyến đường (truyền vật lý)
6. Bưu cục đầu kia **nhận và phân phát** đến đúng nhà
7. Người nhận **mở phong bì, đọc nội dung**

Mạng máy tính hoạt động y hệt vậy — dữ liệu của bạn đi qua **7 tầng (layer)** trước khi đến đích. Mỗi tầng có một nhiệm vụ riêng, chuyên biệt. Đây là mô hình **OSI (Open Systems Interconnection)**.

---

## Giải thích cho người đã biết lập trình (nâng cao)

OSI Model là **framework lý thuyết** (không phải implementation thực tế) do ISO tạo ra năm 1984 để chuẩn hóa cách các hệ thống mạng khác nhau giao tiếp. Trong thực tế, **TCP/IP model** (4 tầng) mới được implement, nhưng OSI vẫn là ngôn ngữ chung để debug, thiết kế, và phân tích network.

### 7 Tầng OSI — Chi tiết

| Tầng | Tên | PDU | Protocol tiêu biểu | Thiết bị | Chức năng |
|------|-----|-----|--------------------|----------|-----------|
| 7 | Application | Data | HTTP, FTP, SMTP, DNS, SSH | Gateway, Firewall (L7) | Giao tiếp với ứng dụng người dùng |
| 6 | Presentation | Data | SSL/TLS, JPEG, MPEG, ASCII | — | Mã hóa, nén, chuyển đổi định dạng |
| 5 | Session | Data | NetBIOS, RPC, PPTP | — | Quản lý phiên (session) giữa các ứng dụng |
| 4 | Transport | Segment (TCP) / Datagram (UDP) | TCP, UDP | Firewall (L4) | End-to-end delivery, port, flow control |
| 3 | Network | Packet | IP, ICMP, OSPF, BGP | Router | Logical addressing, routing |
| 2 | Data Link | Frame | Ethernet, Wi-Fi (802.11), ARP | Switch, Bridge | MAC addressing, error detection (CRC) |
| 1 | Physical | Bits | Ethernet cable, Fiber, Wi-Fi signal | Hub, Repeater, NIC | Truyền bit vật lý qua môi trường |

### Encapsulation & Decapsulation

**Gửi dữ liệu (Encapsulation):** Mỗi tầng thêm header (và đôi khi trailer) vào dữ liệu từ tầng trên.

```
Application Data
    ↓ + HTTP header
[HTTP header | Data]                     → L7 PDU: Data
    ↓ + TCP header
[TCP header | HTTP header | Data]        → L4 PDU: Segment
    ↓ + IP header
[IP header | TCP header | HTTP header | Data]     → L3 PDU: Packet
    ↓ + Ethernet header + trailer (FCS)
[ETH | IP | TCP | HTTP | Data | FCS]    → L2 PDU: Frame
    ↓
01010101...                              → L1 PDU: Bits
```

**Nhận dữ liệu (Decapsulation):** Ngược lại — mỗi tầng bóc header của mình.

### Tại sao phân tầng?

- **Separation of concerns**: thay đổi L2 (ví dụ: đổi từ Ethernet sang Wi-Fi) không ảnh hưởng L3 trở lên
- **Interoperability**: thiết bị từ hãng khác nhau vẫn giao tiếp được
- **Troubleshooting có hệ thống**:
  - `ping` thành công → L1, L2, L3 OK
  - TCP connect fail → L4 vấn đề (firewall, port closed)
  - HTTP 403 → L7 vấn đề (authorization)
- **Modular development**: team OS, team driver, team app làm độc lập

### OSI vs TCP/IP Model

```
OSI Model          TCP/IP Model
┌──────────────┐   ┌──────────────┐
│ Application  │   │              │
│ Presentation │ → │ Application  │  (HTTP, FTP, DNS, SMTP)
│ Session      │   │              │
├──────────────┤   ├──────────────┤
│ Transport    │ → │ Transport    │  (TCP, UDP)
├──────────────┤   ├──────────────┤
│ Network      │ → │ Internet     │  (IP, ICMP)
├──────────────┤   ├──────────────┤
│ Data Link    │ → │ Network      │
│ Physical     │   │ Access       │  (Ethernet, Wi-Fi)
└──────────────┘   └──────────────┘
```

TCP/IP là implementation thực tế; OSI là công cụ tư duy.

---

## Định nghĩa chính xác

**OSI Model** (Open Systems Interconnection Model) là mô hình tham chiếu do ISO (International Organization for Standardization) định nghĩa trong ISO/IEC 7498-1, mô tả cách các hệ thống truyền thông mạng giao tiếp thông qua 7 tầng trừu tượng. Mỗi tầng cung cấp dịch vụ cho tầng trên và sử dụng dịch vụ của tầng dưới, giao tiếp với tầng cùng cấp ở máy đối diện thông qua các protocol tương ứng.

---

## Đặc điểm kỹ thuật / So sánh

| Tiêu chí | OSI Model | TCP/IP Model |
|----------|-----------|--------------|
| Số tầng | 7 | 4 |
| Ra đời | 1984 (ISO) | 1970s (DARPA) |
| Mục đích | Lý thuyết, tham chiếu | Thực tế, implementation |
| Tính linh hoạt | Strict layering | Một số overlap |
| Phổ biến | Dạy học, troubleshoot | Dùng thực tế trên Internet |
| Protocol cụ thể | Không gắn liền | TCP, IP, UDP, ... |

---

## Code mẫu

```python
import socket
import struct

# ── Ví dụ: Xem thông tin network interface (L2/L3)
import psutil
for interface, addrs in psutil.net_if_addrs().items():
    print(f"\nInterface: {interface}")
    for addr in addrs:
        if addr.family == socket.AF_INET:
            print(f"  IPv4: {addr.address}")  # L3
        elif addr.family == socket.AF_INET6:
            print(f"  IPv6: {addr.address}")  # L3
        elif addr.family == psutil.AF_LINK:
            print(f"  MAC:  {addr.address}")  # L2

# ── Ví dụ: Raw socket để xem IP header (L3)
# (cần quyền admin/root)
# raw = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_TCP)
# data, addr = raw.recvfrom(65535)
# ip_header = data[:20]
# (version, ihl, tos, total_length, ...) = struct.unpack('!BBHHHBBH4s4s', ip_header)

# ── Ví dụ: DNS lookup — L7 protocol trên L4 UDP
hostname = "google.com"
ip_address = socket.gethostbyname(hostname)
print(f"\nDNS (L7 via UDP L4): {hostname} → {ip_address}")

# ── Ví dụ: TCP connect — kiểm tra L4
def check_port(host, port, timeout=3):
    """Kiểm tra port có mở không — test L4 Transport"""
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(timeout)
    result = sock.connect_ex((host, port))
    sock.close()
    return result == 0

print(f"Port 80 (HTTP):  {'open' if check_port('google.com', 80) else 'closed'}")
print(f"Port 443 (HTTPS): {'open' if check_port('google.com', 443) else 'closed'}")
print(f"Port 22 (SSH):    {'open' if check_port('google.com', 22) else 'closed'}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**OSI Model dùng khi:**
- Debug network issue: xác định lỗi ở tầng nào để thu hẹp phạm vi
- Thiết kế hệ thống mạng: phân tích protocol nào cần dùng ở tầng nào
- Học và giải thích cách mạng hoạt động
- Phỏng vấn kỹ thuật

**Không dùng khi:**
- Implementation thực tế — dùng TCP/IP stack của hệ điều hành
- Tầng 5 (Session) và 6 (Presentation) rất mờ nhạt trong thực tế, thường được gộp vào L7

---

## So sánh với các mô hình liên quan

| | OSI (7 tầng) | TCP/IP (4 tầng) | DoD Model |
|-|-------------|-----------------|-----------|
| Xuất xứ | ISO, 1984 | DARPA, 1970s | US Dept. of Defense |
| Tầng | 7 | 4 | 4 |
| Thực tế | Lý thuyết | Thực tế | Thực tế |
| Session/Presentation | Tách biệt | Gộp vào Application | Gộp vào Application |
| Physical | Tầng 1 | Gộp vào Network Access | Gộp vào Network Access |

---

## Lỗi thường gặp (Common Pitfalls)

- **Nhầm tầng của ARP**: ARP hoạt động ở ranh giới L2/L3 (dịch IP sang MAC), thường được xếp vào L2.
- **Nhầm tầng của SSL/TLS**: SSL/TLS trong OSI là L6 (Presentation), nhưng trong TCP/IP thực tế nằm giữa Application và Transport.
- **Nghĩ OSI là implementation**: OSI chỉ là mô hình tham chiếu, không có OS nào implement đúng 7 tầng tách biệt hoàn toàn.
- **Bỏ qua L5 và L6**: Trong thực tế, session management (cookies, tokens) và presentation (JSON, TLS) đều nằm trong application code — không có software layer riêng.
- **Nhầm switch với router**: Switch hoạt động ở L2 (MAC address), Router ở L3 (IP address). Layer-3 switch thì xử lý cả hai.

---

## Câu hỏi phỏng vấn hay gặp

- Kể tên 7 tầng OSI theo thứ tự từ thấp đến cao.
- PDU của tầng Transport, Network, Data Link là gì?
- Sự khác biệt giữa Switch và Router — tầng nào chúng hoạt động?
- Tại sao OSI có 7 tầng mà TCP/IP chỉ có 4?
- Encapsulation và Decapsulation là gì?
- Khi ping thành công nhưng HTTP request thất bại — lỗi ở tầng nào?
- ARP hoạt động ở tầng nào và làm gì?
- Tại sao phân tầng lại quan trọng trong thiết kế mạng?
