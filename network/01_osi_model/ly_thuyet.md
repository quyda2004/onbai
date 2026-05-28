# OSI Model — Mô hình 7 tầng

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn gửi một bức thư tay cho bạn ở thành phố khác. Quá trình đó trải qua nhiều bước:

1. Bạn **viết nội dung** thư (tạo dữ liệu)
2. Bạn **bỏ thư vào phong bì**, ghi địa chỉ người nhận (đóng gói)
3. Bạn mang ra **bưu cục** (giao cho hệ thống vận chuyển)
4. Bưu cục **phân loại**, chọn **xe tải / máy bay** phù hợp (chọn đường đi)
5. Xe tải / máy bay **chở thư** theo đúng tuyến đường (truyền vật lý)
6. Bưu cục đầu kia **nhận và phân phát** đến đúng nhà
7. Người nhận **mở phong bì, đọc nội dung**

Mạng máy tính hoạt động tương tự — dữ liệu của bạn đi qua 7 "tầng" khác nhau trước khi đến đích. Mỗi tầng có một nhiệm vụ riêng, chuyên biệt. Đây chính là mô hình **OSI (Open Systems Interconnection)**.

---

## Giải thích cho người đã biết lập trình (nâng cao)

OSI Model là một **framework lý thuyết** (không phải implementation thực tế) được tạo ra bởi ISO năm 1984 để chuẩn hóa cách các hệ thống mạng khác nhau giao tiếp với nhau. Trong thực tế, **TCP/IP model** (4 tầng) mới được implement, nhưng OSI vẫn là ngôn ngữ chung để debug, thiết kế, và phân tích network.

**Tại sao cần phân tầng?**
- **Separation of concerns**: mỗi tầng chỉ lo một việc, thay đổi một tầng không ảnh hưởng tầng khác
- **Interoperability**: thiết bị của các hãng khác nhau vẫn giao tiếp được
- **Troubleshooting**: khi có lỗi, xác định được lỗi ở tầng nào (ping OK = L1/L2/L3 OK; TCP connect fail = L4 issue)
- **Modular development**: team làm OS, team làm driver, team làm app — độc lập nhau

**Encapsulation / Decapsulation:**
- **Encapsulation** (gửi): L7 → L1, mỗi tầng **thêm header** (và đôi khi trailer) vào dữ liệu
- **Decapsulation** (nhận): L1 → L7, mỗi tầng **bóc header** của mình, đọc thông tin rồi chuyển lên

**PDU (Protocol Data Unit)** — tên gọi của data tại mỗi tầng:
```
L7/L6/L5: Data
L4:       Segment (TCP) / Datagram (UDP)
L3:       Packet
L2:       Frame
L1:       Bits
```

---

## Định nghĩa chính xác

**OSI Model** (Open Systems Interconnection Model) là mô hình tham chiếu do ISO (International Organization for Standardization) định nghĩa trong ISO/IEC 7498-1, mô tả cách các hệ thống truyền thông mạng giao tiếp thông qua 7 tầng trừu tượng, mỗi tầng cung cấp dịch vụ cho tầng trên và sử dụng dịch vụ của tầng dưới.

---

## Bảng / Sơ đồ kỹ thuật

### 7 tầng OSI chi tiết

| # | Tầng (Layer) | PDU | Protocol ví dụ | Thiết bị | Chức năng chính |
|---|-------------|-----|----------------|----------|-----------------|
| 7 | Application | Data | HTTP, FTP, SMTP, DNS, SSH | Gateway, Proxy | Giao diện với ứng dụng người dùng |
| 6 | Presentation | Data | SSL/TLS, JPEG, MPEG, ASCII, UTF-8 | Gateway | Mã hóa, nén, định dạng dữ liệu |
| 5 | Session | Data | NetBIOS, RPC, PPTP | Gateway | Quản lý phiên kết nối (session) |
| 4 | Transport | Segment/Datagram | TCP, UDP, SCTP | Firewall (L4) | Truyền tin cậy end-to-end, port |
| 3 | Network | Packet | IP, ICMP, OSPF, BGP | Router | Định tuyến (routing), địa chỉ logic |
| 2 | Data Link | Frame | Ethernet, Wi-Fi (802.11), ARP | Switch, Bridge | Địa chỉ vật lý (MAC), error detection |
| 1 | Physical | Bits | Ethernet cable, Fiber, Radio | Hub, Repeater, NIC | Truyền bits qua môi trường vật lý |

### Sơ đồ Encapsulation

```
Sender (Application → Physical)          Receiver (Physical → Application)
─────────────────────────────────        ─────────────────────────────────
[L7] DATA                                [L7] DATA
      ↓ + L7 header                            ↑ - L7 header
[L6] [L7H | DATA]                        [L6] [L7H | DATA]
      ↓ + L6 header                            ↑ - L6 header
[L5] [L6H | L7H | DATA]                  [L5] [L6H | L7H | DATA]
      ↓ + L4 header                            ↑ - L4 header
[L4] [L4H | L6H | L7H | DATA]           [L4] [L4H | L6H | L7H | DATA]
      ↓ + L3 header                            ↑ - L3 header
[L3] [L3H | L4H | ... | DATA]           [L3] [L3H | L4H | ... | DATA]
      ↓ + L2 header + trailer                  ↑ - L2 header + trailer
[L2] [L2H | L3H | ... | DATA | L2T]     [L2] [L2H | L3H | ... | DATA | L2T]
      ↓ convert to bits                        ↑ convert from bits
[L1] 01010101010101010101...             [L1] 01010101010101010101...
```

### So sánh OSI vs TCP/IP

| OSI Model (7 tầng) | TCP/IP Model (4 tầng) | Ghi chú |
|-------------------|-----------------------|---------|
| 7 - Application   |                       | |
| 6 - Presentation  | Application           | TCP/IP gộp 3 tầng trên |
| 5 - Session       |                       | |
| 4 - Transport     | Transport             | TCP, UDP |
| 3 - Network       | Internet              | IP, ICMP |
| 2 - Data Link     |                       | |
| 1 - Physical      | Network Access        | TCP/IP gộp 2 tầng dưới |

**OSI** = mô hình lý thuyết, dùng để học/debug/thiết kế  
**TCP/IP** = mô hình thực tế, được implement trong OS và thiết bị mạng

### Chi tiết từng tầng

#### Layer 1 — Physical (Vật lý)
- Truyền/nhận **raw bits** (0 và 1) qua môi trường vật lý
- Quan tâm đến: voltage levels, timing, bit rate, connector types, cable specs
- **Không** quan tâm đến ý nghĩa của bits
- Ví dụ: Ethernet cable CAT6 (1Gbps), Fiber optic (10Gbps), Wi-Fi radio waves

#### Layer 2 — Data Link (Liên kết dữ liệu)
- Truyền **frames** giữa hai node **kề nhau** (cùng mạng LAN)
- **MAC address** (48-bit, ví dụ: `AA:BB:CC:DD:EE:FF`) để định danh thiết bị
- **Error detection** bằng CRC (Cyclic Redundancy Check) — phát hiện lỗi trong quá trình truyền
- Gồm 2 sublayer: LLC (Logical Link Control) và MAC (Media Access Control)
- Thiết bị: **Switch** (học MAC table, forward frame đúng port)

#### Layer 3 — Network (Mạng)
- Định tuyến **packets** từ nguồn đến đích qua **nhiều mạng khác nhau**
- **IP address** (IPv4: 32-bit, IPv6: 128-bit) để định danh host trên Internet
- **Routing protocols**: OSPF (internal), BGP (internet-scale)
- **ICMP**: ping, traceroute (báo lỗi và thông tin mạng)
- Thiết bị: **Router** (đọc IP header, quyết định next hop)

#### Layer 4 — Transport (Giao vận)
- Cung cấp truyền thông **end-to-end** giữa các process (dùng port number)
- **TCP** (Transmission Control Protocol): reliable, ordered, connection-oriented
- **UDP** (User Datagram Protocol): unreliable, fast, connectionless
- **Port numbers**: xác định ứng dụng (HTTP=80, HTTPS=443, SSH=22, DNS=53)
- Multiplexing: nhiều ứng dụng dùng chung một IP qua port khác nhau

#### Layer 5 — Session (Phiên)
- Quản lý **phiên giao tiếp** giữa hai ứng dụng
- Thiết lập, duy trì, và kết thúc session
- **Synchronization**: checkpoint trong data transfer (nếu lỗi, resume từ checkpoint)
- Trong thực tế TCP/IP, chức năng này nằm trong L4 hoặc L7

#### Layer 6 — Presentation (Trình bày)
- **Data translation**: chuyển đổi giữa các định dạng (ASCII ↔ EBCDIC, Big-endian ↔ Little-endian)
- **Encryption/Decryption**: SSL/TLS (thực tế implement ở đây)
- **Compression**: gzip, deflate
- Đảm bảo data gửi đi và nhận về có cùng format

#### Layer 7 — Application (Ứng dụng)
- Giao diện trực tiếp với **ứng dụng người dùng**
- Cung cấp **network services** cho ứng dụng: HTTP(S), FTP, SMTP, DNS, SSH, DHCP
- **Không** phải là ứng dụng (browser, email client), mà là **protocol** mà ứng dụng dùng

---

## Pseudocode / Code mẫu

```python
# Minh họa Encapsulation concept bằng Python
# (Đây là mô phỏng khái niệm, không phải implementation thực)

class PDU:
    """Protocol Data Unit — gói dữ liệu tại mỗi tầng"""
    def __init__(self, data, header=None, trailer=None):
        self.header = header or {}
        self.data = data
        self.trailer = trailer or {}
    
    def __repr__(self):
        return f"[{self.header} | {self.data} | {self.trailer}]"


def encapsulate(app_data: str) -> dict:
    """Mô phỏng quá trình encapsulation từ L7 xuống L1"""
    
    # Layer 7 - Application: HTTP request
    l7_data = PDU(app_data, header={"method": "GET", "host": "example.com"})
    print(f"L7 Application: {l7_data}")
    
    # Layer 4 - Transport: TCP segment
    l4_segment = PDU(l7_data, header={
        "src_port": 52000,
        "dst_port": 80,
        "seq_num": 1000,
        "ack_num": 0,
        "flags": "SYN"
    })
    print(f"L4 Transport: [TCP Header | {l7_data}]")
    
    # Layer 3 - Network: IP packet
    l3_packet = PDU(l4_segment, header={
        "src_ip": "192.168.1.10",
        "dst_ip": "93.184.216.34",
        "ttl": 64,
        "protocol": "TCP"
    })
    print(f"L3 Network: [IP Header | TCP Seg | {app_data}]")
    
    # Layer 2 - Data Link: Ethernet frame
    l2_frame = PDU(l3_packet, 
        header={"src_mac": "AA:BB:CC:DD:EE:01", "dst_mac": "AA:BB:CC:DD:EE:FF"},
        trailer={"crc": "0xABCDEF12"}  # CRC checksum
    )
    print(f"L2 Data Link: [MAC Header | IP | TCP | Data | CRC]")
    
    # Layer 1 - Physical: bits
    bits = "010101010101....(raw bits)"
    print(f"L1 Physical: {bits}")
    
    return {"l7": l7_data, "l4": l4_segment, "l3": l3_packet, "l2": l2_frame, "l1": bits}


def decapsulate_demo():
    """Mô phỏng decapsulation ở phía receiver"""
    print("\n--- Receiver (Decapsulation) ---")
    print("L1: Nhận bits từ dây mạng")
    print("L2: Đọc MAC header, kiểm tra CRC, bóc frame → lấy IP packet")
    print("L3: Đọc IP header, kiểm tra dst_ip, bóc packet → lấy TCP segment")
    print("L4: Đọc TCP header, kiểm tra port, reassemble → lấy HTTP data")
    print("L7: Đọc HTTP header, xử lý request")


# Demo
print("=== ENCAPSULATION ===")
result = encapsulate("GET /index.html HTTP/1.1")
print()
decapsulate_demo()
```

```bash
# Thực tế: dùng Wireshark để xem các tầng OSI
# Hoặc dùng tcpdump để capture packets

# Capture packets trên interface eth0
sudo tcpdump -i eth0 -n

# Xem chi tiết (verbose) — thấy L2, L3, L4 headers
sudo tcpdump -i eth0 -v -n

# Chỉ xem HTTP traffic (L7, port 80)
sudo tcpdump -i eth0 -n port 80

# Ping để test L3 (ICMP)
ping -c 4 8.8.8.8

# Traceroute để xem routing (L3)
traceroute google.com
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng OSI Model khi:**
- **Troubleshooting mạng**: xác định lỗi ở tầng nào (ping OK nhưng HTTP fail → L7 issue)
- **Thiết kế hệ thống**: quyết định thiết bị nào (switch L2 hay router L3)
- **Học/giảng dạy**: framework để hiểu network từng bước
- **Giao tiếp trong team**: "lỗi này là L4" — mọi người hiểu ngay

**Không cần OSI Model khi:**
- **Implement thực tế**: dùng TCP/IP stack của OS, không tự implement OSI
- **Lập trình ứng dụng**: developer thường chỉ cần quan tâm L4 (socket) và L7 (HTTP)
- **Production debugging chi tiết**: dùng Wireshark, tcpdump (nhìn thấy packet thực)

---

## So sánh với các khái niệm liên quan

| | OSI Model | TCP/IP Model | DoD Model |
|-|-----------|--------------|-----------|
| Số tầng | 7 | 4 | 4 |
| Nguồn gốc | ISO (1984) | DARPA/DoD (1970s) | US Dept of Defense |
| Mục đích | Lý thuyết/tham chiếu | Thực tế/implement | Military networks |
| Tầng 1-2 | Physical + Data Link | Network Access | Network Access |
| Tầng 3 | Network | Internet | Internet |
| Tầng 4 | Transport | Transport | Host-to-Host |
| Tầng 5-7 | Session+Presentation+App | Application | Process/App |
| Dùng thực tế | Không (chỉ tham chiếu) | Có (mọi OS) | Có (military) |

---

## Lỗi thường gặp (Common Pitfalls)

1. **Nhầm OSI với TCP/IP**: OSI là mô hình lý thuyết, TCP/IP mới là cái được implement trong Linux, Windows, macOS
2. **Nhầm tầng của TLS/SSL**: TLS thường được gán cho L6 (Presentation) trong OSI, nhưng thực tế nó nằm giữa L4-L7 trong TCP/IP stack
3. **Quên PDU name**: mỗi tầng có tên riêng — Segment (L4), Packet (L3), Frame (L2), Bits (L1). Không gọi tất cả là "packet"
4. **Switch vs Router nhầm tầng**: Switch hoạt động ở L2 (MAC), Router ở L3 (IP). "Layer 3 switch" là switch có thêm routing capability
5. **ARP ở tầng nào**: ARP nằm giữa L2 và L3 — nó dùng L2 (Ethernet frame) nhưng phục vụ L3 (IP → MAC mapping)
6. **ICMP ở tầng nào**: ICMP (ping) là L3 protocol, dùng IP để đóng gói, nhưng thường nhầm là L4

---

## Câu hỏi phỏng vấn hay gặp

1. **Kể tên 7 tầng OSI và chức năng mỗi tầng?**
   - L7 Application, L6 Presentation, L5 Session, L4 Transport, L3 Network, L2 Data Link, L1 Physical
   - Mnemonic: "**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing"

2. **Sự khác biệt giữa OSI và TCP/IP model?**
   - OSI 7 tầng lý thuyết, TCP/IP 4 tầng thực tế
   - OSI gộp L5+L6+L7 → Application trong TCP/IP
   - OSI gộp L1+L2 → Network Access trong TCP/IP

3. **PDU tại mỗi tầng là gì?**
   - L7-L5: Data, L4: Segment/Datagram, L3: Packet, L2: Frame, L1: Bits

4. **Router hoạt động ở tầng nào? Switch? Hub?**
   - Router: L3, Switch: L2, Hub: L1

5. **Khi bạn gõ URL vào browser, dữ liệu đi qua những tầng nào?**
   - L7: HTTP request → L6: encode → L5: session → L4: TCP segment → L3: IP packet → L2: Ethernet frame → L1: bits → (truyền qua mạng) → reverse decapsulation

6. **TLS hoạt động ở tầng nào?**
   - Lý thuyết OSI: L6 Presentation; Thực tế TCP/IP: giữa Transport và Application (L4.5)

7. **Tại sao cần phân tầng? Lợi ích là gì?**
   - Modularity, interoperability, troubleshooting, independent development
