# Trắc nghiệm — Network Security

> **Tổng số câu:** 21
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** CIA Triad trong bảo mật thông tin gồm những gì?

- A. Certificate, Integrity, Authentication
- B. Confidentiality, Integrity, Availability
- C. Cryptography, Identity, Authorization
- D. Cipher, Isolation, Audit

> **Đáp án: B**
> **Giải thích:** CIA Triad là 3 nguyên tắc cốt lõi của bảo mật: Confidentiality (bảo mật — chỉ người được phép mới đọc được), Integrity (toàn vẹn — dữ liệu không bị sửa đổi), Availability (sẵn sàng — hệ thống hoạt động khi cần).

---

**Câu 2:** Firewall hoạt động ở tầng nào và làm gì?

- A. Tầng Physical, ngăn vật lý không cho kết nối
- B. Tầng Application, chỉ check HTTP traffic
- C. Từ L3 đến L7 tùy loại, lọc traffic dựa trên rules
- D. Tầng Transport, chỉ check TCP connections

> **Đáp án: C**
> **Giải thích:** Stateless firewall L3/L4 (packet filter), Stateful firewall L4 (track connection state), WAF L7 (check HTTP payload). Firewall có thể hoạt động từ L3 đến L7 tùy loại và cấu hình.

---

**Câu 3:** MITM (Man-in-the-Middle) attack là gì?

- A. Tấn công brute force vào mật khẩu
- B. Attacker chèn vào giữa client và server, có thể đọc/sửa traffic
- C. Tấn công làm nghẽn server
- D. Tấn công vào database bằng SQL

> **Đáp án: B**
> **Giải thích:** MITM: attacker đứng giữa client và server, intercept traffic. Nếu không mã hóa (HTTP), attacker đọc được toàn bộ. Nếu có TLS đúng cách → attacker không đọc được nhưng có thể drop packets. HTTPS + certificate validation ngăn MITM.

---

**Câu 4:** TLS/HTTPS ngăn chặn MITM bằng cách nào?

- A. Mã hóa chỉ ở phía client
- B. Server certificate cho phép client verify đang nói chuyện đúng server; TLS mã hóa traffic
- C. Dùng VPN để ẩn traffic
- D. Chặn tất cả traffic không phải HTTPS

> **Đáp án: B**
> **Giải thích:** TLS certificate: server gửi cert được CA ký. Client verify chữ ký CA (root cert có sẵn trong OS). Nếu MITM tự ký cert giả → client từ chối (certificate error). Sau verify, TLS mã hóa → MITM không đọc được dù ngồi giữa.

---

**Câu 5:** DDoS khác DoS ở điểm nào?

- A. DDoS dùng UDP, DoS dùng TCP
- B. DDoS tấn công từ nhiều nguồn (botnet) đồng thời, khó chặn hơn DoS từ 1 nguồn
- C. DDoS nhắm vào database, DoS nhắm vào web server
- D. DDoS chỉ tấn công được server lớn

> **Đáp án: B**
> **Giải thích:** DoS (Denial of Service) từ 1 địa chỉ → dễ block bằng IP. DDoS (Distributed DoS) từ hàng nghìn nguồn (compromised machines/botnet) → bandwidth lớn hơn, khó block hơn vì IP thay đổi liên tục.

---

**Câu 6:** SYN Flood attack khai thác điểm yếu nào của TCP?

- A. TCP không có mã hóa
- B. TCP three-way handshake tạo half-open connection state; server giữ state cho mỗi SYN nhận được
- C. TCP port 80 luôn mở
- D. TCP không có authentication

> **Đáp án: B**
> **Giải thích:** Attacker gửi SYN với IP giả → server trả SYN-ACK (tạo half-open connection, dùng RAM) → không bao giờ nhận ACK → queue half-open connections đầy → từ chối connection hợp lệ. SYN Cookies giải quyết: server không lưu state cho half-open, encode trong ISN.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** HSTS (HTTP Strict Transport Security) bảo vệ khỏi gì?

- A. SQL injection qua HTTP
- B. SSL Stripping / downgrade attack — attacker không thể hạ cấp HTTPS xuống HTTP
- C. DDoS attack
- D. Brute force password

> **Đáp án: B**
> **Giải thích:** SSL stripping: MITM redirect HTTPS về HTTP trước khi user nhìn thấy. HSTS: sau lần đầu visit, browser nhớ domain này phải dùng HTTPS (lưu trong HSTS list trong `max-age` giây). Ngay cả khi user gõ `http://`, browser tự convert thành `https://` mà không gửi request HTTP.

---

**Câu 8:** IDS khác IPS như thế nào?

- A. IDS và IPS giống nhau hoàn toàn
- B. IDS phát hiện và log; IPS phát hiện và block (inline với traffic)
- C. IDS cho mạng LAN, IPS cho Internet
- D. IDS dùng signature, IPS dùng anomaly detection

> **Đáp án: B**
> **Giải thích:** IDS (Intrusion Detection System): out-of-band, nhận copy của traffic, chỉ alert/log — không ảnh hưởng traffic. IPS (Intrusion Prevention System): inline, traffic đi qua IPS, có thể block ngay lập tức. IPS thêm latency và false positive có thể block traffic hợp lệ.

---

**Câu 9:** Perfect Forward Secrecy (PFS) đảm bảo điều gì?

- A. Password không bao giờ gửi qua mạng
- B. Leak private key của server không decrypt được traffic cũ đã ghi lại
- C. Mỗi user có session key riêng
- D. TLS handshake chỉ cần 1 RTT

> **Đáp án: B**
> **Giải thích:** PFS dùng ephemeral Diffie-Hellman (ECDHE): key exchange dùng key tạm thời, bị xóa sau session. Nếu attacker record tất cả traffic hôm nay, sau này lấy được server private key → vẫn không decrypt được (vì session key đã xóa). Khác với RSA key exchange (không PFS): leak private key = decrypt hết lịch sử.

---

**Câu 10:** OAuth 2.0 Authorization Code flow giải quyết vấn đề gì?

- A. Mã hóa mật khẩu người dùng
- B. Third-party app (ví dụ: game) có thể access resource (ảnh Google) mà không cần user chia sẻ mật khẩu
- C. Tự động rotate API keys
- D. Phòng chống SQL injection

> **Đáp án: B**
> **Giải thích:** OAuth 2.0: user cho phép app A access resource của user ở service B, không cần chia sẻ password của B với app A. App A nhận access_token có scope giới hạn, thời hạn. Nếu app A bị hack, hacker không có password của B, chỉ có token (có thể revoke).

---

**Câu 11:** JWT payload có thể bị đọc bởi ai?

- A. Chỉ server có thể đọc vì được mã hóa
- B. Bất kỳ ai có token — payload chỉ base64-encoded, không mã hóa
- C. Chỉ client tạo token
- D. Không ai đọc được vì được sign bằng HMAC

> **Đáp án: B**
> **Giải thích:** JWT gồm 3 phần: header.payload.signature — tất cả base64url encoded, không mã hóa. Bất kỳ ai có token đều đọc được payload (try jwt.io). Signature chỉ đảm bảo payload không bị sửa — không che giấu nội dung. Không lưu secret (password, private key) trong JWT payload.

---

**Câu 12:** ARP Spoofing hoạt động như thế nào?

- A. Attacker giả mạo DNS response
- B. Attacker gửi ARP reply giả để liên kết IP của victim với MAC của attacker
- C. Attacker flood mạng bằng ARP request
- D. Attacker thay đổi routing table của router

> **Đáp án: B**
> **Giải thích:** ARP Spoofing/Poisoning: attacker broadcast ARP reply "192.168.1.1 (gateway) có MAC = AA:BB:CC" (MAC của attacker). Các máy trong LAN cập nhật ARP cache → traffic gửi đến gateway đi qua attacker (MITM). Phòng: Dynamic ARP Inspection (DAI), static ARP, 802.1X.

---

**Câu 13:** mTLS khác TLS thông thường như thế nào?

- A. mTLS dùng nhiều certificate cùng lúc
- B. Trong mTLS, cả client VÀ server đều xác thực bằng certificate (mutual authentication)
- C. mTLS nhanh hơn TLS thông thường
- D. mTLS chỉ dùng cho mobile application

> **Đáp án: B**
> **Giải thích:** TLS thường: chỉ server authenticate (client verify server cert). mTLS (Mutual TLS): cả hai chiều authenticate — server cũng verify client cert. Dùng trong microservices (service mesh Istio), B2B API, không cần username/password vì cert là identity.

---

**Câu 14:** Stateful firewall vs Stateless firewall — sự khác biệt?

- A. Stateful nhanh hơn, Stateless an toàn hơn
- B. Stateless kiểm tra từng packet độc lập; Stateful theo dõi connection state, hiểu ngữ cảnh
- C. Stateful chỉ filter TCP, Stateless filter cả UDP
- D. Không có sự khác biệt thực tế

> **Đáp án: B**
> **Giải thích:** Stateless: kiểm tra từng packet riêng lẻ theo rules (src_ip, dst_ip, port) — không biết packet đó thuộc connection nào. Có thể bypass bằng cách chia nhỏ packet. Stateful: track connection state (SYN_SENT, ESTABLISHED, ...) — có thể phát hiện packet giả mạo không thuộc connection hợp lệ.

---

## Phần 3 — Nâng cao (câu 15–21)

**Câu 15:** Timing Attack là gì và tại sao dùng `hmac.compare_digest()` thay vì `==`?

- A. Timing attack là tấn công khai thác thời gian CPU; không có sự khác biệt thực tế
- B. `==` so sánh từng byte, dừng khi tìm thấy byte khác (variable time) → attacker đo thời gian để đoán bytes; `compare_digest` luôn mất thời gian cố định (constant-time)
- C. `compare_digest` nhanh hơn `==` cho strings dài
- D. `==` không work với bytes, phải dùng `compare_digest`

> **Đáp án: B**
> **Giải thích:** `"abc" == "axc"` dừng ở byte thứ 2 (nhanh hơn). `"abc" == "abd"` dừng ở byte thứ 3 (chậm hơn chút). Attacker gửi hàng triệu guess, đo response time → suy ra secret từng byte. `hmac.compare_digest()` luôn mất cùng thời gian bất kể bytes khác nhau ở đâu.

---

**Câu 16:** Zero Trust Architecture khác mô hình bảo mật truyền thống (castle-and-moat) như thế nào?

- A. Zero Trust không dùng firewall
- B. Truyền thống: ai trong mạng nội bộ thì tin tưởng; Zero Trust: không tin ai cả — verify mọi request bất kể nguồn gốc
- C. Zero Trust chỉ cho cloud, truyền thống cho on-premise
- D. Zero Trust dùng blockchain để verify identity

> **Đáp án: B**
> **Giải thích:** Castle-and-moat: có VPN/tường lửa bảo vệ perimeter, bên trong tin tưởng hoàn toàn → nếu attacker vào được (lateral movement dễ). Zero Trust: "never trust, always verify" — mọi request phải authenticate và authorize, kể cả từ internal network. Microsegmentation, least privilege, continuous monitoring.

---

**Câu 17:** Phân tích scenario: JWT của user có `exp: 1735689600` (Jan 1, 2025) bị đánh cắp hôm nay (Jan 2, 2025). Attacker có làm được gì không?

- A. Có, attacker dùng được vì server không kiểm tra exp
- B. Không, JWT đã expired (exp < current time) — server sẽ từ chối
- C. Có, nếu attacker thay đổi exp trong token
- D. Phụ thuộc vào loại server

> **Đáp án: B**
> **Giải thích:** Server verify JWT: (1) verify signature (attacker không có secret key → không thể forge/modify), (2) check `exp` claim (1735689600 < current timestamp → expired → reject). Attacker không thể thay đổi exp vì sẽ làm signature không hợp lệ. JWT hết hạn là vô dụng với attacker.

---

**Câu 18:** CSRF attack hoạt động như thế nào và tại sao SameSite cookie phòng chặn được?

- A. CSRF đánh cắp cookie trực tiếp; SameSite mã hóa cookie
- B. CSRF lừa browser nạn nhân gửi request đến site đang đăng nhập; SameSite=Strict không gửi cookie khi request từ cross-site
- C. CSRF fake identity của server; SameSite verify server identity
- D. CSRF bypass CORS; SameSite enforce CORS

> **Đáp án: B**
> **Giải thích:** CSRF: attacker tạo trang evil.com với `<img src="bank.com/transfer?to=attacker&amount=1000">`. Khi nạn nhân visit evil.com (đang đăng nhập bank.com), browser tự gửi request kèm cookie của bank.com → transfer thành công. SameSite=Strict: cookie không được gửi khi request xuất phát từ domain khác → CSRF thất bại.

---

**Câu 19:** WireGuard được coi là tiên tiến hơn OpenVPN vì những lý do gì?

- A. WireGuard hỗ trợ nhiều cipher hơn, linh hoạt hơn
- B. Codebase nhỏ (~4000 LOC vs ~100K), thuật toán hiện đại cố định (ChaCha20-Poly1305, Curve25519), handshake nhanh hơn
- C. WireGuard miễn phí, OpenVPN tốn phí
- D. WireGuard không cần certificate, OpenVPN cần

> **Đáp án: B**
> **Giải thích:** WireGuard advantages: (1) Codebase nhỏ → dễ audit, ít attack surface. (2) Dùng thuật toán state-of-the-art cố định (không phải linh hoạt như OpenVPN → loại bỏ weak cipher negotiation). (3) Handshake 1-RTT. (4) Roaming: khi IP thay đổi (mobile), connection tự phục hồi. Nhược điểm: chưa hỗ trợ TCP (chỉ UDP), ít tùy chọn hơn.

---

**Câu 20:** Phân tích security headers của response sau — vấn đề gì?

```http
HTTP/1.1 200 OK
Content-Type: text/html
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

- A. Content-Type header thiếu charset
- B. `Allow-Origin: *` kết hợp với `Allow-Credentials: true` là invalid và nguy hiểm — browser sẽ từ chối, nhưng nếu server không enforce đúng có thể bypass
- C. Response thiếu Content-Length
- D. Không có vấn đề gì

> **Đáp án: B**
> **Giải thích:** Spec CORS: không thể dùng `*` với `credentials: true`. Browser sẽ từ chối request có credentials nếu `Allow-Origin: *`. Tuy nhiên, một số server cấu hình sai (ví dụ: reflect Origin header khi có credentials) → bất kỳ origin nào cũng được phép với credentials → data leakage. Phải chỉ định whitelist origin cụ thể.

---

**Câu 21:** Certificate Transparency (CT) là gì và giải quyết vấn đề gì?

- A. Mã hóa certificate bằng transparent algorithm
- B. Public log của tất cả TLS certificate được issue — phát hiện CA cấp cert giả/trái phép cho domain
- C. Cho phép user tự tạo certificate mà không cần CA
- D. Rotate certificate tự động

> **Đáp án: B**
> **Giải thích:** CT (RFC 6962): mọi CA phải log certificate vào public, append-only CT logs. Browser require CT (Chrome từ 2018). Lợi ích: domain owner có thể monitor cert nào được issue cho domain của mình → phát hiện nếu rogue CA issue cert giả (ví dụ: DigiNotar 2011 incident). CAA record thêm một layer: chỉ định CA nào được phép issue cert.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | C      | 13  | B      |
| 3   | B      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | B      | 16  | B      |
| 6   | B      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | B      | 19  | B      |
| 9   | B      | 20  | B      |
| 10  | B      | 21  | B      |
| 11  | B      |     |        |
