# DNS — Domain Name System (Hệ thống Phân giải Tên Miền)

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn muốn gọi điện cho bạn bè nhưng bạn không nhớ số điện thoại, chỉ nhớ tên. Bạn tra danh bạ điện thoại: gõ tên "Nguyễn Văn An" → danh bạ trả về số "0912.345.678" → bạn gọi được.

**DNS** là quyển "danh bạ điện thoại" của Internet:
- Bạn gõ `www.google.com` vào trình duyệt (tên dễ nhớ)
- DNS tra cứu và trả về địa chỉ IP `142.250.185.46` (số điện thoại của Google)
- Trình duyệt kết nối đến địa chỉ IP đó

Tại sao cần DNS? Vì con người nhớ tên tốt hơn nhớ dãy số. Máy tính ngược lại — cần địa chỉ IP để kết nối.

Và giống như danh bạ có nhiều cấp (danh bạ địa phương, danh bạ quốc gia...), DNS cũng có cấu trúc phân cấp từ trung ương đến địa phương để phân tán tải và quản lý hàng tỷ tên miền.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### DNS là Distributed Hierarchical Database

DNS không phải một máy chủ duy nhất mà là hệ thống phân tán với hàng nghìn server toàn cầu, phân cấp thành:

```
Root (.)
├── .com
│   ├── google.com
│   │   └── www.google.com
│   ├── facebook.com
│   └── ...
├── .vn
│   ├── vnexpress.net (chú ý: .net là TLD khác)
│   └── ...
├── .org
└── ...
```

### DNS Resolution — Quá trình phân giải

**Recursive Query** (phổ biến, client dùng):

```
Browser               Recursive Resolver     Root NS     TLD NS (.com)    Authoritative NS
  |                        |                   |              |                  |
  | query: www.google.com  |                   |              |                  |
  |----------------------->|                   |              |                  |
  |                        |                   |              |                  |
  |                        | Hỏi Root NS       |              |                  |
  |                        |------------------>|              |                  |
  |                        |  "Hỏi .com NS"   |              |                  |
  |                        |<------------------|              |                  |
  |                        |                                  |                  |
  |                        | Hỏi .com TLD NS                  |                  |
  |                        |---------------------------------->|                  |
  |                        |  "Hỏi google.com NS"             |                  |
  |                        |<----------------------------------|                  |
  |                        |                                                     |
  |                        | Hỏi google.com Authoritative NS                    |
  |                        |---------------------------------------------------->|
  |                        |  "www.google.com → 142.250.185.46"                 |
  |                        |<----------------------------------------------------|
  |                        |                                                     
  | IP: 142.250.185.46     |
  |<-----------------------|
```

- **Recursive Resolver**: Thường là ISP hoặc Google (8.8.8.8), Cloudflare (1.1.1.1). Thực hiện toàn bộ quá trình tìm kiếm thay cho client.
- **Root Nameserver**: 13 địa chỉ IP logic (a.root-servers.net → m.root-servers.net), biết TLD nào do ai quản lý. Thực tế có >1000 instance nhờ Anycast.
- **TLD Nameserver**: Quản lý `.com`, `.vn`, `.org`... biết authoritative NS cho mỗi domain.
- **Authoritative Nameserver**: Lưu records thực sự của domain.

**Iterative Query** (DNS server hỏi server khác):
Resolver hỏi Root → Root trả về địa chỉ TLD NS → Resolver hỏi TLD NS → TLD NS trả về địa chỉ Authoritative NS → Resolver hỏi Authoritative NS → nhận kết quả.

### DNS Record Types

| Type | Tên đầy đủ | Ý nghĩa | Ví dụ |
|------|-----------|---------|-------|
| **A** | Address | IPv4 address | `google.com → 142.250.185.46` |
| **AAAA** | IPv6 Address | IPv6 address | `google.com → 2607:f8b0:4004:c1b::65` |
| **CNAME** | Canonical Name | Alias → tên khác | `www.example.com → example.com` |
| **MX** | Mail Exchange | Mail server + priority | `example.com → mail.example.com (priority 10)` |
| **TXT** | Text | Dữ liệu text tùy ý | SPF, DKIM, domain verification |
| **NS** | Nameserver | Authoritative NS của domain | `example.com → ns1.dnsprovider.com` |
| **PTR** | Pointer | Reverse DNS (IP → tên) | `46.185.250.142.in-addr.arpa → google.com` |
| **SOA** | Start of Authority | Thông tin về zone | Serial, refresh, retry, expire |
| **SRV** | Service | Xác định server cho service | `_http._tcp.example.com → host:port` |
| **CAA** | Cert Authority Auth | CA được phép cấp cert | `example.com → letsencrypt.org` |

### CNAME vs A Record

```
# KHÔNG dùng CNAME ở apex domain (naked domain):
example.com CNAME other.com    ← SAI (RFC không cho phép CNAME ở apex)

# Đúng cách:
www.example.com CNAME example.com  ← OK
example.com A 93.184.216.34        ← OK

# ALIAS/ANAME record (extension của một số DNS provider):
example.com ALIAS other.com    ← Cho phép ở apex, provider tự resolve
```

### TTL và DNS Caching

```
Quá trình cache:
1. Browser cache: A record lưu TTL giây, kể từ khi nhận
2. OS cache: /etc/hosts được check đầu tiên, luôn override DNS
3. Recursive Resolver cache: dùng chung giữa nhiều client
4. Khi TTL = 0: không cache, luôn query

Ảnh hưởng TTL:
- TTL thấp (300s): thay đổi DNS có hiệu lực nhanh, tăng query load
- TTL cao (86400s): giảm query, nhưng thay đổi DNS propagate chậm

Khi migrate server:
- Giảm TTL xuống 300s trước 24-48 giờ
- Migrate, verify
- Cập nhật DNS record
- Sau khi ổn định, tăng TTL lại
```

### DNS Caching ở nhiều tầng

```
Browser → OS Resolver → Recursive Resolver (ISP/Google/Cloudflare)
                              ↓
                    Check cache trước
                    Miss → query Root NS
                    
/etc/hosts (Linux/Mac) — override tất cả DNS:
127.0.0.1   localhost
192.168.1.100   mydev.local

/etc/resolv.conf (Linux) — cấu hình DNS server:
nameserver 8.8.8.8
nameserver 8.8.4.4
search example.com          # tự thêm domain khi lookup ngắn
```

### DNS over HTTPS (DoH) và DNS over TLS (DoT)

| | Truyền thống | DNS over TLS (DoT) | DNS over HTTPS (DoH) |
|-|-------------|-------------------|---------------------|
| Port | 53 UDP/TCP | 853 TCP | 443 HTTPS |
| Mã hóa | Không | TLS | TLS (trong HTTPS) |
| Privacy | ISP thấy tất cả | ISP thấy bạn dùng DoT | Lẫn với HTTPS traffic |
| Firewall | Dễ bị chặn/intercept | Dễ nhận diện (port 853) | Khó chặn (port 443) |
| Hỗ trợ | Universal | Tích hợp OS | Browser (Firefox default) |

### DNS Attacks

**1. DNS Cache Poisoning / Spoofing**
```
Attacker chèn record giả vào cache của resolver:
google.com → 1.2.3.4 (IP của attacker thay vì IP thật)

Phòng chống: DNSSEC (ký số các DNS records)
```

**2. DDoS Amplification Attack**
```
Attacker gửi DNS query nhỏ (60 bytes) với source IP giả (victim)
DNS server trả response lớn (3000+ bytes) về victim
Amplification factor: 50x

Query: ANY isc.org → response 3000 bytes
50 queries/s × 50x amplification = 150 Kbps → victim

Phòng chống: Rate limiting, disable ANY query, Response Rate Limiting (RRL)
```

**3. DNS Hijacking**
```
ISP hoặc attacker redirect DNS responses:
- ISP: chặn domain, redirect về trang cảnh báo
- Attacker: MITM, sửa response
- Phòng chống: DoH/DoT, DNSSEC
```

**4. Subdomain Takeover**
```
example.com có CNAME tới subdomain.hosting.com
Hosting provider xóa account → subdomain.hosting.com trống
Attacker đăng ký subdomain.hosting.com → kiểm soát domain
```

---

## Định nghĩa chính xác

**DNS (Domain Name System)**: Hệ thống phân cấp phân tán (RFC 1034, 1035) cung cấp dịch vụ phân giải tên miền sang địa chỉ IP và ngược lại. Hoạt động theo mô hình client-server trên UDP port 53 (hoặc TCP cho responses > 512 bytes hoặc zone transfer).

**Zone**: Một phần của DNS namespace mà một authoritative nameserver có quyền quản lý. Mỗi zone có một file zone chứa resource records.

**DNSSEC**: DNS Security Extensions — thêm digital signature vào DNS records để xác thực tính toàn vẹn.

---

## Bảng / Sơ đồ kỹ thuật

### DNS Packet Format

```
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                      ID                       |  16-bit transaction ID
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|QR|  Opcode   |AA|TC|RD|RA|   Z    |   RCODE   |  Flags
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    QDCOUNT                    |  Số lượng questions
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    ANCOUNT                    |  Số lượng answers
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    NSCOUNT                    |  Số lượng authority records
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    ARCOUNT                    |  Số lượng additional records
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

Flags quan trọng:
- **QR**: 0 = query, 1 = response
- **RD**: Recursion Desired (client yêu cầu recursive)
- **RA**: Recursion Available (server hỗ trợ recursive)
- **AA**: Authoritative Answer
- **RCODE**: 0=OK, 1=Format error, 2=Server fail, 3=Name error (NXDOMAIN), 5=Refused

---

## Code mẫu

### Python — dns.resolver để query DNS

```python
import dns.resolver
import dns.reversename
import socket

# Cài đặt: pip install dnspython

def query_dns_records():
    resolver = dns.resolver.Resolver()
    # Dùng Cloudflare DNS
    resolver.nameservers = ['1.1.1.1', '1.0.0.1']
    
    domain = 'google.com'
    
    # A record (IPv4)
    print(f"\n=== A Records cho {domain} ===")
    try:
        answers = resolver.resolve(domain, 'A')
        for rdata in answers:
            print(f"  {domain} → {rdata.address} (TTL: {answers.ttl}s)")
    except dns.resolver.NXDOMAIN:
        print(f"  Domain {domain} không tồn tại")
    except dns.resolver.NoAnswer:
        print(f"  Không có A record")
    
    # AAAA record (IPv6)
    print(f"\n=== AAAA Records ===")
    try:
        answers = resolver.resolve(domain, 'AAAA')
        for rdata in answers:
            print(f"  {domain} → {rdata.address}")
    except Exception as e:
        print(f"  {e}")
    
    # MX records
    print(f"\n=== MX Records ===")
    answers = resolver.resolve(domain, 'MX')
    for rdata in sorted(answers, key=lambda x: x.preference):
        print(f"  Priority {rdata.preference}: {rdata.exchange}")
    
    # TXT records (SPF, DKIM, etc.)
    print(f"\n=== TXT Records ===")
    try:
        answers = resolver.resolve(domain, 'TXT')
        for rdata in answers:
            for txt_string in rdata.strings:
                print(f"  {txt_string.decode()}")
    except Exception as e:
        print(f"  {e}")
    
    # NS records
    print(f"\n=== NS Records ===")
    answers = resolver.resolve(domain, 'NS')
    for rdata in answers:
        print(f"  {rdata.target}")


def reverse_dns_lookup(ip: str):
    """Reverse DNS: IP → hostname"""
    try:
        # Dùng dns.reversename để tạo PTR query name
        rev_name = dns.reversename.from_address(ip)
        print(f"Reverse lookup: {ip} → PTR name: {rev_name}")
        
        answer = dns.resolver.resolve(rev_name, 'PTR')
        for rdata in answer:
            print(f"  {ip} → {rdata.target}")
    except Exception as e:
        print(f"  Lỗi: {e}")
    
    # Cách đơn giản hơn với socket
    try:
        hostname = socket.gethostbyaddr(ip)
        print(f"  socket.gethostbyaddr: {hostname}")
    except socket.herror as e:
        print(f"  {e}")


def check_dns_propagation(domain: str, record_type: str = 'A'):
    """Kiểm tra DNS record từ nhiều server khác nhau"""
    dns_servers = {
        'Google Primary': '8.8.8.8',
        'Google Secondary': '8.8.4.4',
        'Cloudflare': '1.1.1.1',
        'OpenDNS': '208.67.222.222',
    }
    
    print(f"\n=== DNS Propagation Check: {domain} ({record_type}) ===")
    for name, server in dns_servers.items():
        resolver = dns.resolver.Resolver()
        resolver.nameservers = [server]
        resolver.timeout = 3
        try:
            answers = resolver.resolve(domain, record_type)
            ips = [str(r.address) for r in answers]
            print(f"  [{name} {server}]: {', '.join(ips)}")
        except Exception as e:
            print(f"  [{name} {server}]: ERROR - {e}")


if __name__ == '__main__':
    query_dns_records()
    
    print("\n" + "="*50)
    reverse_dns_lookup('8.8.8.8')
    
    print("\n" + "="*50)
    check_dns_propagation('github.com')
```

### Bash — Debug DNS với dig và nslookup

```bash
# Query A record
dig google.com A

# Query với specific DNS server (@)
dig @8.8.8.8 google.com A

# Query tất cả record types
dig google.com ANY

# Chỉ xem answer, không có extra info
dig +short google.com

# Trace toàn bộ quá trình resolution (recursive)
dig +trace google.com

# Reverse DNS lookup
dig -x 8.8.8.8

# Query MX records
dig google.com MX

# Query TXT records (kiểm tra SPF)
dig google.com TXT

# Kiểm tra DNSSEC
dig +dnssec google.com

# Xem TTL còn lại
dig +ttl google.com

# nslookup (cross-platform, đơn giản hơn)
nslookup google.com
nslookup google.com 1.1.1.1

# Flush DNS cache
# Linux (systemd-resolved):
sudo systemd-resolve --flush-caches
# macOS:
sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder
# Windows:
ipconfig /flushdns

# Xem /etc/hosts
cat /etc/hosts

# Xem DNS server đang dùng
cat /etc/resolv.conf
resolvectl status  # systemd-resolved
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng DNS trực tiếp khi:**
- Cần service discovery trong microservices (DNS-based load balancing)
- Kiểm tra domain ownership (TXT record verification)
- Email authentication (MX, SPF TXT, DKIM TXT)
- Phân tích propagation khi migrate domain

**Dùng DoH/DoT khi:**
- Privacy quan trọng (ISP không nên thấy bạn truy cập gì)
- Môi trường mạng không tin cậy (public WiFi)
- Tránh DNS hijacking của ISP

**Không tự viết DNS resolver khi:**
- Thư viện hệ thống (`getaddrinfo()`, `socket.getaddrinfo()`) đã xử lý caching, TTL, failover tự động
- Chỉ cần lookup đơn giản — dùng built-in

**Lưu ý cho production:**
- Cấu hình multiple authoritative NS (ít nhất 2) để tránh single point of failure
- Dùng health check + automated DNS failover (Route 53, Cloudflare Load Balancing)
- GeoDNS: trả về IP server gần nhất với client

---

## Lỗi thường gặp (Common Pitfalls)

1. **Quên giảm TTL trước khi migrate**: DNS record cũ còn cache ở resolver. Cần giảm TTL 24-48h trước migration.

2. **CNAME ở apex domain**: `example.com CNAME something` vi phạm RFC — dùng A record hoặc ALIAS/ANAME record của DNS provider.

3. **CNAME chain quá dài**: CNAME trỏ đến CNAME khác nhiều cấp → tăng latency. Giới hạn thường 8 hops.

4. **Hardcode IP thay vì dùng hostname**: IP có thể thay đổi. Luôn dùng hostname + DNS.

5. **Không verify DNS propagation**: Sau khi cập nhật record, phải kiểm tra từ nhiều resolver trước khi kết luận đã propagate.

6. **SPF record dùng sai**: Nhiều SPF record cho một domain → email bị từ chối. Chỉ được có 1 TXT record SPF.

7. **Wildcard DNS hiểu sai**: `*.example.com A 1.2.3.4` chỉ match 1 level: `sub.example.com` match, nhưng `a.b.example.com` không match.

8. **DNS không cache NXDOMAIN đủ lâu**: Tốn query cho domain không tồn tại. Cấu hình negative TTL qua SOA record.

---

## Câu hỏi phỏng vấn hay gặp

1. **Giải thích quá trình từ khi gõ URL đến khi trang web hiển thị (DNS phần)?**
   - Browser cache check → OS cache (/etc/hosts) → Local DNS resolver → Recursive resolver (ISP/Google/CF) → Root NS → TLD NS → Authoritative NS → IP address.

2. **DNS dùng UDP hay TCP? Tại sao?**
   - Chủ yếu UDP (port 53) vì queries nhỏ, nhanh, không cần overhead của TCP. Dùng TCP khi: response > 512 bytes (hoặc 4096 bytes với EDNS0), zone transfer (AXFR), DNSSEC.

3. **Sự khác biệt giữa recursive và iterative DNS query?**
   - Recursive: resolver chịu trách nhiệm tìm answer hoàn toàn, client nhận answer cuối cùng. Iterative: mỗi lần hỏi, server trả về địa chỉ NS tiếp theo để hỏi.

4. **TTL là gì và ảnh hưởng thế nào đến performance?**
   - Time To Live: thời gian record được cache. TTL cao = ít query, nhanh hơn nhưng thay đổi chậm propagate. TTL thấp = thay đổi nhanh nhưng nhiều query hơn.

5. **DNS poisoning là gì và cách phòng chống?**
   - Attacker chèn record giả vào cache resolver. Phòng: DNSSEC (ký số records), DoH/DoT (mã hóa transport), randomize query port và transaction ID.

6. **Giải thích sự khác biệt A, AAAA, CNAME record.**
   - A: domain → IPv4. AAAA: domain → IPv6. CNAME: domain → domain khác (alias). CNAME không thể dùng ở apex domain.

7. **Load balancing với DNS hoạt động thế nào?**
   - Round-robin DNS: nhiều A record cho cùng domain, resolver trả về theo vòng tròn. Hạn chế: client cache làm mất cân bằng, không health check. Giải pháp tốt hơn: Anycast, GeoDNS với health check.
