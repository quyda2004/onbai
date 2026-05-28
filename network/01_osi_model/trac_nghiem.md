# Trắc nghiệm — OSI Model

> **Tổng số câu:** 21
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Mô hình OSI có bao nhiêu tầng?

- A. 4
- B. 5
- C. 6
- D. 7

> **Đáp án: D**
> **Giải thích:** OSI Model có đúng 7 tầng: Physical (1), Data Link (2), Network (3), Transport (4), Session (5), Presentation (6), Application (7). TCP/IP mới có 4 tầng.

---

**Câu 2:** PDU (Protocol Data Unit) tại tầng Transport là gì?

- A. Bit
- B. Frame
- C. Packet
- D. Segment

> **Đáp án: D**
> **Giải thích:** Mỗi tầng có PDU riêng: L1=Bits, L2=Frame, L3=Packet, L4=Segment (TCP)/Datagram (UDP), L5-L7=Data.

---

**Câu 3:** Tầng nào trong OSI chịu trách nhiệm định địa chỉ logic (IP) và routing?

- A. Tầng 2 — Data Link
- B. Tầng 3 — Network
- C. Tầng 4 — Transport
- D. Tầng 5 — Session

> **Đáp án: B**
> **Giải thích:** Tầng Network (L3) xử lý logical addressing (IP address) và routing (tìm đường đến đích). Router hoạt động ở tầng này.

---

**Câu 4:** Switch hoạt động ở tầng nào của mô hình OSI?

- A. Tầng 1 — Physical
- B. Tầng 2 — Data Link
- C. Tầng 3 — Network
- D. Tầng 4 — Transport

> **Đáp án: B**
> **Giải thích:** Switch thông thường hoạt động ở L2, dùng MAC address để forward frame. Hub hoạt động ở L1 (broadcast mọi frame). Layer-3 switch xử lý cả L2 và L3.

---

**Câu 5:** Giao thức HTTP hoạt động ở tầng nào?

- A. Tầng 3 — Network
- B. Tầng 4 — Transport
- C. Tầng 6 — Presentation
- D. Tầng 7 — Application

> **Đáp án: D**
> **Giải thích:** HTTP là application-layer protocol (L7). Nó chạy trên TCP (L4), nhưng bản thân HTTP thuộc L7 cùng với FTP, SMTP, DNS, SSH.

---

**Câu 6:** Quá trình thêm header vào dữ liệu khi đi từ tầng 7 xuống tầng 1 gọi là gì?

- A. Decapsulation
- B. Encapsulation
- C. Fragmentation
- D. Multiplexing

> **Đáp án: B**
> **Giải thích:** Encapsulation là quá trình mỗi tầng thêm header (và đôi khi trailer) khi data đi từ Application xuống Physical. Decapsulation là quá trình ngược lại (nhận).

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Tầng nào trong OSI chịu trách nhiệm mã hóa và nén dữ liệu?

- A. Tầng 4 — Transport
- B. Tầng 5 — Session
- C. Tầng 6 — Presentation
- D. Tầng 7 — Application

> **Đáp án: C**
> **Giải thích:** Tầng Presentation (L6) xử lý data translation: mã hóa/giải mã (SSL/TLS trong lý thuyết OSI), nén, chuyển đổi định dạng (ASCII, JPEG, MPEG). Tuy nhiên trong thực tế TCP/IP, những việc này nằm trong Application layer.

---

**Câu 8:** Khi bạn gửi HTTP request, ARP được sử dụng để làm gì?

- A. Chuyển domain thành IP address
- B. Chuyển IP address thành MAC address
- C. Encrypt HTTP payload
- D. Xác định port của HTTP server

> **Đáp án: B**
> **Giải thích:** ARP (Address Resolution Protocol) chuyển IP address (L3) thành MAC address (L2). Khi máy tính biết IP của gateway nhưng cần MAC để gửi frame Ethernet, ARP broadcast để hỏi "ai có IP này?".

---

**Câu 9:** Đâu là sự khác biệt chính giữa OSI và TCP/IP model?

- A. TCP/IP có nhiều tầng hơn OSI
- B. OSI là implementation thực tế, TCP/IP là lý thuyết
- C. OSI có 7 tầng lý thuyết, TCP/IP có 4 tầng thực tế
- D. OSI chỉ dùng cho mạng LAN, TCP/IP dùng cho Internet

> **Đáp án: C**
> **Giải thích:** OSI (7 tầng) là framework lý thuyết dùng để giải thích và troubleshoot. TCP/IP (4 tầng) là implementation thực tế chạy trên Internet. Không có OS nào implement đúng 7 tầng OSI riêng biệt.

---

**Câu 10:** Tầng nào xử lý flow control và error recovery (retransmission)?

- A. Tầng 2 — Data Link
- B. Tầng 3 — Network
- C. Tầng 4 — Transport
- D. Tầng 5 — Session

> **Đáp án: C**
> **Giải thích:** Tầng Transport (L4) với TCP xử lý flow control (sliding window), congestion control, error recovery (retransmission), và ordering (sequence number). L2 chỉ có error detection (CRC) không có recovery.

---

**Câu 11:** Khi ping 8.8.8.8 thành công nhưng không vào được website, lỗi có thể ở tầng nào?

- A. Tầng 1 hoặc 2
- B. Tầng 3
- C. Tầng 4 hoặc 7
- D. Không xác định được

> **Đáp án: C**
> **Giải thích:** Ping dùng ICMP (L3). Nếu ping thành công → L1, L2, L3 hoạt động. Website dùng HTTP/HTTPS qua TCP (L4) và DNS (L7). Lỗi có thể ở L4 (TCP port 80/443 bị chặn) hoặc L7 (DNS lookup thất bại, server trả về lỗi).

---

**Câu 12:** PDU tại tầng Data Link gọi là gì?

- A. Packet
- B. Segment
- C. Frame
- D. Datagram

> **Đáp án: C**
> **Giải thích:** L2 PDU = Frame. Frame bao gồm MAC header, data (IP packet), và FCS (Frame Check Sequence) trailer để phát hiện lỗi.

---

**Câu 13:** Thiết bị nào hoạt động ở tầng Physical (L1)?

- A. Switch
- B. Router
- C. Hub
- D. Firewall

> **Đáp án: C**
> **Giải thích:** Hub hoạt động ở L1 — nhận tín hiệu điện và khuếch đại, broadcast ra tất cả port mà không đọc địa chỉ. Switch (L2) dùng MAC, Router (L3) dùng IP, Firewall có thể từ L3 đến L7.

---

**Câu 14:** Tầng Session (L5) trong OSI có chức năng gì trong thực tế?

- A. Quản lý connection TCP
- B. Quản lý phiên đăng nhập, checkpoint/recovery cho dialog
- C. Mã hóa dữ liệu truyền
- D. Định tuyến gói tin

> **Đáp án: B**
> **Giải thích:** Tầng Session quản lý dialog giữa hai ứng dụng: thiết lập, duy trì, kết thúc phiên. Ví dụ: NetBIOS, RPC. Trong thực tế TCP/IP, chức năng này được tích hợp vào Application layer (cookie session, token).

---

## Phần 3 — Nâng cao (câu 15–21)

**Câu 15:** SSL/TLS thuộc tầng nào trong OSI model?

- A. Tầng 3 — Network
- B. Tầng 4 — Transport
- C. Tầng 5 hoặc 6 — Session/Presentation
- D. Tầng 7 — Application

> **Đáp án: C**
> **Giải thích:** Trong OSI lý thuyết, TLS nằm giữa L5 (Session — quản lý kết nối TLS) và L6 (Presentation — mã hóa/giải mã). Trong TCP/IP thực tế, TLS nằm giữa Transport (TCP) và Application (HTTP). Câu này có tranh luận trong cộng đồng, nhưng OSI thường map TLS vào L6.

---

**Câu 16:** Tại sao việc phân tầng trong OSI giúp cho việc thay đổi technology dễ dàng hơn?

- A. Vì các tầng có thể hoạt động song song
- B. Vì thay đổi một tầng không ảnh hưởng đến giao diện với tầng khác
- C. Vì OSI quy định implementation cụ thể cho từng tầng
- D. Vì mỗi tầng có một protocol duy nhất

> **Đáp án: B**
> **Giải thích:** Separation of concerns: mỗi tầng giao tiếp với tầng kề qua interface cố định. Đổi từ Ethernet sang Wi-Fi (L2) không ảnh hưởng IP (L3) hay TCP (L4). Đây là nguyên tắc abstraction quan trọng trong kỹ thuật.

---

**Câu 17:** Trong quá trình nhận dữ liệu, router thực hiện thao tác gì với PDU?

- A. Chỉ đọc L1 bits và forward
- B. Decapsulate đến L3, đọc IP header, encapsulate lại với L2 header mới, forward
- C. Decapsulate toàn bộ đến L7, phân tích rồi gửi lại
- D. Giữ nguyên toàn bộ packet và forward theo MAC address

> **Đáp án: B**
> **Giải thích:** Router hoạt động ở L3. Khi nhận frame: bóc L2 header (Frame → Packet), đọc IP header để xác định next hop, thêm L2 header mới (MAC address của next hop), gửi đi. Router không đọc TCP hoặc HTTP payload trong quá trình routing thông thường.

---

**Câu 18:** Fragmentation trong IP xảy ra ở tầng nào và khi nào?

- A. L2, khi frame lớn hơn 1500 bytes
- B. L3, khi packet lớn hơn MTU của link
- C. L4, khi segment lớn hơn MSS
- D. L7, khi HTTP body quá lớn

> **Đáp án: B**
> **Giải thích:** IP Fragmentation xảy ra ở L3 khi packet lớn hơn MTU (Maximum Transmission Unit) của link (thường 1500 bytes Ethernet). Router có thể fragment IPv4 packet. IPv6 không cho phép router fragment — sender phải dùng Path MTU Discovery.

---

**Câu 19:** So sánh: Repeater vs Hub vs Switch vs Router về tầng OSI hoạt động?

- A. Tất cả đều hoạt động ở L1
- B. Repeater/Hub: L1, Switch: L2, Router: L3
- C. Repeater: L1, Hub: L2, Switch: L3, Router: L4
- D. Hub: L1, Switch: L2, Router: L2/L3

> **Đáp án: B**
> **Giải thích:** Repeater (khuếch đại tín hiệu) và Hub (multiport repeater) ở L1. Switch dùng MAC address ở L2. Router dùng IP address ở L3. Layer-3 switch hoạt động cả L2 và L3.

---

**Câu 20:** Encapsulation overhead: khi gửi 1 byte dữ liệu HTTP, tổng kích thước frame Ethernet (approximate) là bao nhiêu?

- A. ~1 byte
- B. ~21 bytes (HTTP header)
- C. ~61 bytes (Ethernet + IP + TCP + HTTP headers)
- D. ~1518 bytes (tối đa một frame Ethernet)

> **Đáp án: C**
> **Giải thích:** Overhead từ các header: Ethernet header=14 bytes, IP header=20 bytes, TCP header=20 bytes, HTTP header ~200+ bytes (biến đổi). Tối thiểu: ETH(14) + IP(20) + TCP(20) = 54 bytes header cho dù payload chỉ 1 byte. Vì vậy gửi nhiều request nhỏ rất tốn kém — batching/pipelining/multiplexing quan trọng.

---

**Câu 21:** Một packet đi từ máy A (192.168.1.2) qua 3 router đến máy B (10.0.0.5). Điều gì thay đổi và điều gì không thay đổi tại mỗi hop?

- A. IP src/dst thay đổi, MAC src/dst không đổi
- B. IP src/dst không đổi, MAC src/dst thay đổi tại mỗi hop
- C. Cả IP và MAC đều thay đổi tại mỗi hop
- D. Cả IP và MAC đều không thay đổi

> **Đáp án: B**
> **Giải thích:** IP src/dst (192.168.1.2 → 10.0.0.5) **không đổi** trong suốt hành trình (trừ NAT). MAC src/dst **thay đổi tại mỗi hop**: router nhận frame, bóc L2 header cũ, thêm L2 header mới với MAC của interface tiếp theo. Đây là điểm quan trọng: IP address = end-to-end, MAC address = hop-by-hop.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | D      | 12  | C      |
| 2   | D      | 13  | C      |
| 3   | B      | 14  | B      |
| 4   | B      | 15  | C      |
| 5   | D      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | C      | 18  | B      |
| 8   | B      | 19  | B      |
| 9   | C      | 20  | C      |
| 10  | C      | 21  | B      |
| 11  | C      |     |        |
