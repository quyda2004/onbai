# Trắc nghiệm — DNS

> **Tổng số câu:** 21
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** DNS dùng để làm gì?

- A. Mã hóa HTTP traffic
- B. Chuyển đổi domain name thành IP address
- C. Phân chia mạng thành subnet
- D. Xác thực người dùng vào mạng

> **Đáp án: B**
> **Giải thích:** DNS (Domain Name System) là hệ thống phân cấp chuyển đổi human-readable domain (google.com) thành IP address (142.250.190.78) mà máy tính có thể dùng để kết nối.

---

**Câu 2:** DNS thường dùng giao thức và port nào?

- A. TCP port 80
- B. UDP port 53
- C. TCP port 443
- D. UDP port 67

> **Đáp án: B**
> **Giải thích:** DNS thường dùng UDP port 53 cho query thông thường (nhỏ, nhanh, không cần handshake). Dùng TCP port 53 khi response > 512 bytes (EDNS: 4096 bytes) hoặc zone transfer.

---

**Câu 3:** DNS record type A được dùng để làm gì?

- A. Ánh xạ hostname sang IPv6 address
- B. Chỉ định mail server cho domain
- C. Ánh xạ hostname sang IPv4 address
- D. Tạo alias từ domain này sang domain khác

> **Đáp án: C**
> **Giải thích:** A record: hostname → IPv4. AAAA record: hostname → IPv6. MX record: mail server. CNAME record: hostname → hostname khác (alias).

---

**Câu 4:** TTL trong DNS record có nghĩa là gì?

- A. Thời gian server mất để trả lời query
- B. Số giây DNS record được cache trước khi phải requery
- C. Số lần tối đa có thể query
- D. Kích thước tối đa của DNS response

> **Đáp án: B**
> **Giải thích:** TTL (Time To Live): số giây mà resolver và client có thể cache DNS record trước khi phải hỏi lại authoritative server. TTL thấp = thay đổi nhanh propagate nhưng tăng query load.

---

**Câu 5:** CNAME record dùng để làm gì?

- A. Chỉ định mail server
- B. Tạo alias — trỏ một hostname đến một hostname khác
- C. Reverse DNS lookup
- D. Xác minh domain ownership

> **Đáp án: B**
> **Giải thích:** CNAME (Canonical Name): `blog.example.com → example.wordpress.com`. Khi client query blog.example.com, DNS trả về CNAME và tiếp tục resolve example.wordpress.com. CNAME không thể dùng cho apex domain (root domain như example.com).

---

**Câu 6:** Có bao nhiêu root name server trong hệ thống DNS?

- A. 1
- B. 7
- C. 13
- D. 100

> **Đáp án: C**
> **Giải thích:** Có 13 root name server (a–m.root-servers.net). Trên thực tế, mỗi "server" là một Anycast cluster với hàng trăm máy chủ vật lý. Con số 13 xuất phát từ giới hạn kích thước UDP packet 512 bytes của DNS truyền thống.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Thứ tự đúng của quá trình DNS resolution là gì?

- A. Browser cache → OS cache → Authoritative NS → Root NS
- B. Root NS → TLD NS → Authoritative NS → Browser
- C. Browser cache → OS cache → Recursive resolver → Root NS → TLD NS → Authoritative NS
- D. Authoritative NS → Root NS → TLD NS → Browser

> **Đáp án: C**
> **Giải thích:** Thứ tự: (1) Browser cache, (2) OS cache (/etc/hosts + OS DNS cache), (3) Recursive resolver (ISP hoặc 8.8.8.8) — nếu resolver cũng không có cache thì, (4) Root NS → TLD NS → Authoritative NS. Resolver cache kết quả, browser nhận IP.

---

**Câu 8:** MX record dùng để làm gì và có gì đặc biệt?

- A. Ánh xạ IP sang domain; không có gì đặc biệt
- B. Chỉ định mail server cho domain; có priority value (số nhỏ = ưu tiên cao)
- C. Lưu text tùy ý; thường dùng cho SPF và DKIM
- D. Chỉ định nameserver cho domain

> **Đáp án: B**
> **Giải thích:** MX (Mail Exchange) record chỉ định mail server xử lý email cho domain, kèm priority. Priority nhỏ hơn = được thử trước. Ví dụ: MX 10 mail1.example.com, MX 20 mail2.example.com → thử mail1 trước, fallback sang mail2.

---

**Câu 9:** DNS Caching Poisoning là gì?

- A. Cache resolver bị đầy, không query được
- B. Attacker inject DNS records giả vào cache của resolver, redirect traffic
- C. Virus làm hỏng file DNS cache của OS
- D. DNS resolver cố tình trả về kết quả sai

> **Đáp án: B**
> **Giải thích:** DNS Cache Poisoning (Kaminsky Attack): attacker inject bản ghi DNS giả vào cache của recursive resolver. Ví dụ: resolver cache "bank.com → attacker_ip" → người dùng bị redirect đến site giả mạo. Phòng: randomize source port + transaction ID, DNSSEC.

---

**Câu 10:** Negative caching trong DNS là gì?

- A. Từ chối không cache DNS response
- B. Cache kết quả NXDOMAIN (domain không tồn tại) để tránh query lặp lại
- C. Cache DNS trên client nhưng không trên server
- D. Xóa DNS cache khi TTL hết

> **Đáp án: B**
> **Giải thích:** Negative caching (RFC 2308): resolver cache cả kết quả "không tìm thấy" (NXDOMAIN). Thời gian cache dựa trên SOA minimum TTL. Giúp giảm tải cho DNS server khi có nhiều query đến domain không tồn tại.

---

**Câu 11:** Sự khác biệt giữa Recursive resolver và Authoritative nameserver?

- A. Recursive resolver lưu records; Authoritative chỉ forward query
- B. Authoritative lưu DNS records thực sự của domain; Recursive resolver hỏi thay cho client và cache kết quả
- C. Recursive = ISP nameserver; Authoritative = root server
- D. Không có sự khác biệt, đây là hai tên gọi của cùng một server

> **Đáp án: B**
> **Giải thích:** Authoritative NS (ví dụ: Route 53, Cloudflare) là nguồn sự thật — chứa DNS records thực của domain. Recursive resolver (8.8.8.8, 1.1.1.1) là intermediary — nhận query từ client, hỏi các NS server khác, cache kết quả, trả lời client.

---

**Câu 12:** Khi đổi A record của domain, tại sao người dùng vẫn thấy IP cũ trong một thời gian?

- A. Server mới chưa khởi động
- B. Do TTL — DNS record cũ đang được cache ở nhiều resolver, phải chờ hết TTL mới requery
- C. ISP chặn thay đổi DNS
- D. Browser không tự động reload DNS

> **Đáp án: B**
> **Giải thích:** DNS propagation: mỗi resolver cache record cũ theo TTL. Nếu TTL=86400 (1 ngày), một số user vẫn thấy IP cũ đến 24 giờ. Best practice: giảm TTL xuống 300s (5 phút) trước ≥24 giờ trước khi migrate.

---

**Câu 13:** PTR record dùng để làm gì?

- A. Chỉ định priority của server
- B. Reverse DNS lookup — ánh xạ IP address về hostname
- C. Xác thực domain bằng public key
- D. Lưu địa chỉ IPv6

> **Đáp án: B**
> **Giải thích:** PTR (Pointer) record: reverse DNS lookup. `8.8.8.8` → query `8.8.8.8.in-addr.arpa` → PTR record → `dns.google`. Dùng để verify email server (anti-spam), logging, security auditing.

---

**Câu 14:** DoH (DNS over HTTPS) có lợi thế gì so với DNS thông thường?

- A. Nhanh hơn vì dùng HTTP/2
- B. Privacy tốt hơn — DNS query được mã hóa, lẫn vào HTTPS traffic, khó bị chặn
- C. Không cần resolver, truy vấn trực tiếp authoritative NS
- D. Hỗ trợ nhiều record type hơn

> **Đáp án: B**
> **Giải thích:** DNS thường = UDP port 53, plaintext → ISP/router có thể thấy bạn query domain nào, filter, hoặc spoof. DoH = DNS trong HTTPS (port 443) → mã hóa bởi TLS, lẫn vào HTTPS traffic thông thường → khó chặn và nghe lén hơn.

---

## Phần 3 — Nâng cao (câu 15–21)

**Câu 15:** Tại sao CNAME không thể dùng cho apex domain (ví dụ: example.com)?

- A. Vì apex domain không hỗ trợ Unicode
- B. RFC quy định: apex domain phải có SOA và NS records, CNAME không thể coexist với records khác
- C. Vì CNAME chỉ hoạt động với subdomain
- D. Vì apex domain phải dùng IPv6

> **Đáp án: B**
> **Giải thích:** Theo RFC 1034: CNAME không thể coexist với bất kỳ record nào khác (kể cả SOA, NS) tại cùng một name. Apex domain bắt buộc phải có SOA và NS records → không thể dùng CNAME. Giải pháp: ALIAS/ANAME record (không chuẩn RFC nhưng nhiều DNS provider hỗ trợ) hoặc A record trực tiếp.

---

**Câu 16:** DNS Amplification Attack hoạt động như thế nào?

- A. Attacker làm nghẽn DNS server bằng hàng triệu query
- B. Attacker gửi query với IP giả (victim) đến DNS open resolver → resolver gửi response lớn về victim, khuếch đại bandwidth
- C. Attacker crack mật khẩu của DNS admin
- D. Attacker chèn malware vào DNS software

> **Đáp án: B**
> **Giải thích:** DNS Amplification DDoS: query nhỏ (60 bytes) → response lớn (3000+ bytes) = amplification factor ~50x. Attacker dùng botnet gửi query ANY với src_ip=victim_ip đến nhiều open resolvers → victim bị flood bởi DNS responses. Phòng: BCP38 (ingress filtering), rate limiting, đóng open resolver.

---

**Câu 17:** DNSSEC cung cấp tính năng bảo mật nào mà DNS thường không có?

- A. Mã hóa DNS query và response
- B. Xác thực tính toàn vẹn của DNS records qua digital signature
- C. Ẩn DNS records khỏi public
- D. Mã hóa domain name

> **Đáp án: B**
> **Giải thích:** DNSSEC ký số (digital signature) các DNS records — client có thể verify records chưa bị sửa đổi (integrity và authenticity). DNSSEC KHÔNG mã hóa — DNS records vẫn plaintext, ai cũng thấy. Chỉ đảm bảo records là chính xác từ authoritative server.

---

**Câu 18:** Khi query `www.example.com`, trình tự CNAME chain là gì?

```
www.example.com  CNAME  alias.cdn.com
alias.cdn.com    CNAME  edge.cdn.net
edge.cdn.net     A      1.2.3.4
```

Trình duyệt sẽ kết nối đến đâu?

- A. www.example.com (ignore CNAME)
- B. alias.cdn.com
- C. edge.cdn.net
- D. 1.2.3.4

> **Đáp án: D**
> **Giải thích:** CNAME chain được follow tự động bởi resolver cho đến khi gặp A/AAAA record. Kết quả cuối là IP 1.2.3.4 — trình duyệt kết nối đến IP này. Chú ý: mỗi CNAME trong chain là thêm 1 DNS lookup → tăng latency.

---

**Câu 19:** Tại sao kỹ thuật Anycast được dùng cho root name servers?

- A. Để mã hóa traffic đến root server
- B. Cùng IP address được announce từ nhiều location — client tự động kết nối đến server gần nhất (thấp latency, resilient)
- C. Để phân chia root servers theo địa lý
- D. Để đồng bộ DNS records giữa các root server

> **Đáp án: B**
> **Giải thích:** Anycast: cùng IP (ví dụ 198.41.0.4 của a.root-servers.net) được announce qua BGP từ hàng trăm location toàn cầu. Routing tự động chọn server gần nhất (theo AS path). Lợi ích: latency thấp, resilient (nếu 1 node down, traffic tự route sang node khác).

---

**Câu 20:** Split-horizon DNS là gì?

- A. DNS server phân chia câu hỏi thành nhiều query nhỏ
- B. Cùng domain nhưng trả về IP khác nhau tùy thuộc vào source IP của query (internal vs external)
- C. DNS dùng UDP và TCP đồng thời cho cùng query
- D. Kỹ thuật cache DNS ở nhiều tầng

> **Đáp án: B**
> **Giải thích:** Split-horizon (split-brain) DNS: DNS server trả về kết quả khác nhau cho cùng domain. Ví dụ: query từ internal network → api.company.com = 10.0.0.1 (private IP); query từ Internet → api.company.com = 203.0.113.10 (public IP). Dùng để ẩn internal services, tối ưu routing.

---

**Câu 21:** Phân tích vấn đề bảo mật trong scenario sau:

Company A dùng domain `internal.company-a.com` cho internal services. Company A bị mua lại, domain bị bỏ không đăng ký gia hạn. Attacker đăng ký domain đó và tạo DNS records.

Đây là ví dụ về loại tấn công nào?

- A. DNS Cache Poisoning
- B. DNS Tunneling
- C. Subdomain Takeover / Dangling DNS
- D. DNS Amplification

> **Đáp án: C**
> **Giải thích:** Dangling DNS / Subdomain Takeover: DNS record trỏ đến resource không còn tồn tại hoặc bị buông bỏ. Attacker claim resource đó → control nội dung dưới domain nạn nhân. Phổ biến với CNAME trỏ đến SaaS platforms (GitHub Pages, Azure, AWS) bị delete. Phòng: audit và xóa DNS records không còn dùng.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 12  | B      |
| 2   | B      | 13  | B      |
| 3   | C      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | B      | 16  | B      |
| 6   | C      | 17  | B      |
| 7   | C      | 18  | D      |
| 8   | B      | 19  | B      |
| 9   | B      | 20  | B      |
| 10  | B      | 21  | C      |
| 11  | B      |     |        |
