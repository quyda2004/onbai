# Trắc nghiệm — TCP/IP

> **Tổng số câu:** 21
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** TCP three-way handshake gồm các bước nào?

- A. SYN → ACK → FIN
- B. SYN → SYN-ACK → ACK
- C. SYN → ACK → SYN-ACK
- D. HELLO → ACK → ESTABLISHED

> **Đáp án: B**
> **Giải thích:** Three-way handshake: (1) Client gửi SYN với sequence number x, (2) Server trả SYN-ACK với seq=y, ack=x+1, (3) Client gửi ACK với ack=y+1. Cả 3 bước đảm bảo cả hai bên có thể gửi và nhận.

---

**Câu 2:** UDP khác TCP ở điểm nào quan trọng nhất?

- A. UDP chạy ở tầng Network, TCP ở tầng Transport
- B. UDP không đảm bảo delivery và không có connection
- C. UDP chỉ dùng được trong mạng LAN
- D. UDP không hỗ trợ port

> **Đáp án: B**
> **Giải thích:** UDP là connectionless (không handshake) và unreliable (không ACK, không retransmission). Cả TCP và UDP đều ở tầng Transport (L4) và đều dùng port.

---

**Câu 3:** Port số nào được dùng cho HTTPS?

- A. 80
- B. 21
- C. 22
- D. 443

> **Đáp án: D**
> **Giải thích:** Well-known ports: HTTP=80, HTTPS=443, FTP=21, SSH=22, DNS=53, SMTP=25. Các port 0–1023 là well-known ports, cần quyền root/admin để bind.

---

**Câu 4:** Địa chỉ IPv4 có bao nhiêu bit?

- A. 16
- B. 32
- C. 64
- D. 128

> **Đáp án: B**
> **Giải thích:** IPv4 là 32-bit, viết dạng 4 octet (4 × 8 bit). IPv6 là 128-bit. Tổng địa chỉ IPv4: 2³² ≈ 4.3 tỷ (đã cạn kiệt từ 2011).

---

**Câu 5:** Subnet mask của /24 là gì?

- A. 255.0.0.0
- B. 255.255.0.0
- C. 255.255.255.0
- D. 255.255.255.128

> **Đáp án: C**
> **Giải thích:** /24 nghĩa là 24 bit đầu là network portion. 24 bit 1 = 11111111.11111111.11111111.00000000 = 255.255.255.0. Host range: 254 hosts (2⁸ - 2, trừ network và broadcast address).

---

**Câu 6:** Well-known port của DNS là gì?

- A. 53
- B. 80
- C. 443
- D. 67

> **Đáp án: A**
> **Giải thích:** DNS dùng port 53, thường trên UDP (query nhỏ), TCP khi response > 512 bytes (zone transfer, EDNS). Port 67/68 là DHCP.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Tại sao TCP four-way termination cần 4 bước thay vì 2?

- A. Vì TCP cần xác nhận cả hai chiều đóng kết nối độc lập
- B. Vì server cần thêm thời gian xử lý
- C. Vì TCP header có giới hạn kích thước
- D. Vì NAT cần thêm bước

> **Đáp án: A**
> **Giải thích:** Mỗi chiều của TCP connection phải đóng riêng. Client gửi FIN (muốn đóng chiều client→server), server ACK. Server còn data cần gửi thì gửi tiếp, sau đó server gửi FIN (đóng chiều server→client), client ACK.

---

**Câu 8:** TIME_WAIT state trong TCP là gì và tại sao cần thiết?

- A. Trạng thái chờ handshake hoàn tất
- B. Trạng thái sau khi gửi FIN, chờ 2×MSL để đảm bảo ACK cuối đến đích
- C. Trạng thái khi connection bị timeout
- D. Trạng thái khi server quá tải

> **Đáp án: B**
> **Giải thích:** Sau khi gửi ACK cuối (bước 4 của four-way termination), client ở TIME_WAIT trong 2×MSL (~60-120s). Lý do: đảm bảo ACK cuối đến được server (nếu mất, server sẽ retransmit FIN, client cần còn tồn tại để ACK lại); ngăn delayed packet của connection cũ ảnh hưởng connection mới.

---

**Câu 9:** Sliding Window trong TCP dùng để làm gì?

- A. Phát hiện lỗi bit trong packet
- B. Kiểm soát tốc độ gửi để tránh làm tràn buffer của receiver
- C. Xác định đường đi tốt nhất cho packet
- D. Mã hóa dữ liệu truyền

> **Đáp án: B**
> **Giải thích:** Sliding Window là cơ chế flow control. Receiver quảng cáo `rwnd` (receive window = còn bao nhiêu buffer). Sender không được có hơn `rwnd` bytes in-flight chưa được ACK. Khi buffer đầy, receiver set rwnd=0, sender dừng gửi.

---

**Câu 10:** Mạng 10.0.0.0/8 có bao nhiêu host address khả dụng?

- A. 254
- B. 65,534
- C. 16,777,214
- D. 4,294,967,294

> **Đáp án: C**
> **Giải thích:** /8 có 8 bit network, 24 bit host. Số host = 2²⁴ - 2 = 16,777,216 - 2 = 16,777,214 (trừ network address và broadcast). 10.0.0.0 là private range (RFC 1918).

---

**Câu 11:** Tại sao DNS thường dùng UDP thay vì TCP?

- A. UDP nhanh hơn và DNS query/response thường nhỏ (<512 bytes)
- B. TCP không hỗ trợ DNS protocol
- C. UDP cho phép broadcast, cần thiết cho DNS
- D. TCP quá chậm để xử lý DNS

> **Đáp án: A**
> **Giải thích:** DNS query thường rất nhỏ. UDP không cần handshake (tiết kiệm RTT), overhead thấp (8 bytes vs 20+ bytes TCP). Khi response > 512 bytes (hoặc 4096 bytes với EDNS) hoặc zone transfer, DNS dùng TCP.

---

**Câu 12:** Congestion control trong TCP — slow start hoạt động như thế nào?

- A. Giảm tốc độ gửi xuống minimum khi network congested
- B. Bắt đầu với cwnd=1 MSS, tăng gấp đôi mỗi RTT cho đến ssthresh
- C. Luôn gửi ở tốc độ tối đa rồi giảm dần
- D. Chờ ACK từng packet trước khi gửi packet tiếp

> **Đáp án: B**
> **Giải thích:** Slow Start: cwnd bắt đầu tại 1 MSS, mỗi ACK tăng cwnd thêm 1 MSS → mỗi RTT cwnd tăng gấp đôi (exponential). Khi cwnd đạt ssthresh → chuyển sang Congestion Avoidance (tăng tuyến tính +1 MSS/RTT).

---

**Câu 13:** Sự khác biệt giữa địa chỉ public IP và private IP?

- A. Public IP dùng trong LAN, private IP dùng trên Internet
- B. Private IP không route được trên Internet, dùng trong mạng nội bộ với NAT
- C. Private IP nhanh hơn public IP
- D. Không có sự khác biệt, chỉ là quy ước đặt tên

> **Đáp án: B**
> **Giải thích:** Private IP ranges (RFC 1918): 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 — không được route trên Internet. Router biên (gateway) dùng NAT để dịch private→public IP khi ra Internet.

---

**Câu 14:** Nagle's Algorithm trong TCP là gì? Khi nào cần tắt?

- A. Thuật toán chọn route tốt nhất; tắt khi có nhiều route
- B. Gộp các TCP segment nhỏ thành segment lớn hơn; tắt khi cần low latency (game, VoIP)
- C. Thuật toán phát hiện duplicate ACK; tắt khi network ổn định
- D. Kiểm soát kích thước window; tắt khi bandwidth cao

> **Đáp án: B**
> **Giải thích:** Nagle's Algorithm: không gửi segment nhỏ nếu còn data in-flight chưa ACK — chờ gộp lại cho đủ lớn. Giảm số packet nhỏ nhưng thêm latency. Tắt bằng `TCP_NODELAY` khi cần low latency: game server, VoIP, database response.

---

## Phần 3 — Nâng cao (câu 15–21)

**Câu 15:** Fast Retransmit trong TCP xảy ra khi nào?

- A. Khi timeout chờ ACK hết hạn
- B. Khi nhận 3 duplicate ACK (tổng cộng 4 ACK cùng số)
- C. Khi sender phát hiện packet bị mất qua checksum
- D. Khi receiver gửi NACK

> **Đáp án: B**
> **Giải thích:** Fast Retransmit: khi sender nhận 3 duplicate ACK (tức 4 ACK cùng số sequence), suy ra packet tiếp theo bị mất → gửi lại ngay mà không chờ timeout. Sau đó vào Fast Recovery (giảm cwnd xuống một nửa, không về 1).

---

**Câu 16:** Với subnet 192.168.10.0/25, broadcast address là gì?

- A. 192.168.10.127
- B. 192.168.10.128
- C. 192.168.10.255
- D. 192.168.10.254

> **Đáp án: A**
> **Giải thích:** /25 = 25 bit network, 7 bit host. Số địa chỉ = 2⁷ = 128. Network: 192.168.10.0, Broadcast: 192.168.10.0 + 127 = 192.168.10.127. Host range: 192.168.10.1 – 192.168.10.126 (126 hosts).

---

**Câu 17:** Tại sao 3-way handshake không thể giảm xuống còn 2-way?

- A. Vì TCP header không đủ chỗ
- B. Vì cần đảm bảo cả hai chiều có thể communicate và sync sequence numbers
- C. Vì 2-way handshake dễ bị tấn công hơn
- D. Vì router cần 3 bước để thiết lập route

> **Đáp án: B**
> **Giải thích:** 2-way chỉ đảm bảo server biết client gửi được (SYN). Client chưa biết server có gửi được không (chưa nhận SYN-ACK). Bước 3 (ACK) xác nhận client nhận được SYN-ACK → server biết client có thể nhận. Đồng thời cả hai bên sync ISN (Initial Sequence Number) của nhau.

---

**Câu 18:** IPv6 giải quyết vấn đề gì mà IPv4 không làm được?

- A. Cho phép truyền dữ liệu nhanh hơn
- B. Không gian địa chỉ lớn hơn (128-bit) và không cần NAT
- C. Bảo mật tốt hơn nhờ mã hóa built-in
- D. Giảm latency nhờ header nhỏ hơn

> **Đáp án: B**
> **Giải thích:** IPv6 (128-bit) cung cấp ~3.4×10³⁸ địa chỉ — đủ cho mọi thiết bị có địa chỉ public riêng, không cần NAT. Ngoài ra: stateless autoconfiguration (SLAAC), không cho phép fragmentation tại router (phải dùng PMTUD), Neighbor Discovery thay ARP. IPSec là optional trong IPv6 (không bắt buộc như một số tài liệu cũ viết).

---

**Câu 19:** TCP header có trường Flags. SYN+ACK trong một packet có ý nghĩa gì?

- A. Packet vừa bắt đầu kết nối vừa ACK một sequence number
- B. Lỗi trong packet, cần retransmit
- C. Packet kết thúc kết nối
- D. Packet yêu cầu urgent processing

> **Đáp án: A**
> **Giải thích:** Trong three-way handshake, bước 2 là SYN-ACK: server vừa gửi SYN của mình (để sync sequence number server→client) vừa ACK sequence number của client. Đây là tối ưu hóa — gộp 2 thông điệp vào 1 packet.

---

**Câu 20:** Đoạn code Python sau đây có vấn đề gì?

```python
data = b""
while True:
    chunk = sock.recv(1024)
    if chunk == b"":
        break
    data += chunk
```

- A. Không có vấn đề, code đúng
- B. `recv()` blocking quá lâu, cần timeout
- C. Không biết khi nào nhận đủ message — cần framing protocol
- D. `b""` không phải cách đúng để check connection closed

> **Đáp án: C**
> **Giải thích:** Vấn đề TCP stream: không có message boundary. Code này đọc cho đến khi connection đóng. Nếu server không đóng connection sau mỗi message (persistent connection), client sẽ block mãi. Cần implement framing: length-prefix (gửi kèm length trước data), delimiter (\n, \r\n), hoặc protocol cụ thể như HTTP.

---

**Câu 21:** NAT (Network Address Translation) hoạt động ở tầng nào và có tác dụng gì?

- A. L2 — thay đổi MAC address
- B. L3/L4 — thay đổi IP address (và port) để chia sẻ 1 public IP cho nhiều thiết bị
- C. L7 — thay đổi URL trong HTTP request
- D. L1 — khuếch đại tín hiệu

> **Đáp án: B**
> **Giải thích:** NAT (PAT - Port Address Translation): router biên thay src_ip (private) bằng public IP, thay src_port bằng port unique, lưu mapping vào NAT table. Khi response về, router tra NAT table để forward đúng về máy trong LAN. Phá vỡ end-to-end connectivity (lý do TCP hole punching, STUN/TURN cần thiết cho P2P/WebRTC).

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | B      | 13  | B      |
| 3   | D      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | C      | 16  | A      |
| 6   | A      | 17  | B      |
| 7   | A      | 18  | B      |
| 8   | B      | 19  | A      |
| 9   | B      | 20  | C      |
| 10  | C      | 21  | B      |
| 11  | A      |     |        |
