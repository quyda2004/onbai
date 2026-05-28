# DNS — Domain Name System

---

## Giải thích cho người mới hoàn toàn

Địa chỉ IP của máy chủ Google là `142.250.190.78`. Bạn sẽ không thể nhớ được con số này. Vì vậy, người ta tạo ra DNS — một cuốn **danh bạ điện thoại khổng lồ** của Internet.

Khi bạn gõ `google.com` vào trình duyệt:
1. Máy tính hỏi "DNS resolver": *"google.com có địa chỉ IP là bao nhiêu?"*
2. DNS resolver tra cứu trong danh bạ và trả lời: *"142.250.190.78"*
3. Trình duyệt dùng IP đó để kết nối đến server Google

Cả quá trình này xảy ra trong vài mili-giây mà bạn không biết gì. Đó là DNS — chuyển tên miền dễ nhớ thành địa chỉ IP máy tính hiểu được.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### DNS Hierarchy — Cấu trúc phân cấp

DNS là hệ thống **phân tán, phân cấp** — không có một server trung tâm nào chứa toàn bộ mapping.

```
Root (.)
├── .com
│   ├── google.com  (authoritative nameserver của Google)
│   │   ├── www.google.com → 142.250.190.78
│   │   └── mail.google.com → ...
│   └── facebook.com
├── .org
│   └── wikipedia.org
└── .vn
    └── vnexpress.net
```

**4 thành phần chính:**

| Thành phần | Vai trò | Ví dụ |
|------------|---------|-------|
| DNS Resolver (Recursive) | Hỏi thay cho client, cache kết quả | 8.8.8.8 (Google), 1.1.1.1 (Cloudflare) |
| Root Name Server | Biết địa chỉ TLD servers | 13 root servers (a–m.root-servers.net) |
| TLD Name Server | Biết authoritative servers cho domain | Verisign (.com), IANA (.org) |
| Authoritative Name Server | Chứa DNS records thực sự | Route 53, Cloudflare DNS |

### DNS Resolution Process — Từng bước

```
Browser           OS Cache         Resolver         Root NS       TLD NS (.com)    Authoritative
   |                  |               |                |               |                |
   |-- query -------> |               |                |               |                |
   |  (cache miss)    |               |                |               |                |
   |<-- not found --- |               |                |               |                |
   |                  |               |                |               |                |
   |------------- query -----------> |                |               |                |
   |              (resolver cache miss)                |               |                |
   |                  |               |-- query -----> |               |                |
   |                  |               |<- "hỏi .com TLD" ------------ |                |
   |                  |               |                |               |                |
   |                  |               |-- query ---------------------->|                |
   |                  |               |<- "hỏi ns1.google.com" ------- |                |
   |                  |               |                |               |                |
   |                  |               |-- query ------------------------------------------------>|
   |                  |               |<- "google.com = 142.250.190.78" ----------------------- |
   |                  |               |                |               |                |
   |<----------- response (IP) ----- |                |               |                |
   |  (resolver caches kết quả)      |                |               |                |
```

**Iterative vs Recursive:**
- **Recursive resolution**: client hỏi resolver, resolver tự đi hỏi hết rồi trả kết quả
- **Iterative resolution**: resolver hỏi root, root trả về "hỏi TLD", resolver hỏi TLD, TLD trả về "hỏi authoritative", ...

### DNS Record Types

| Record | Mục đích | Ví dụ |
|--------|----------|-------|
| **A** | Ánh xạ hostname → IPv4 | `www.example.com → 93.184.216.34` |
| **AAAA** | Ánh xạ hostname → IPv6 | `www.example.com → 2606:2800:220:1:248:1893:25c8:1946` |
| **CNAME** | Alias — trỏ đến hostname khác | `blog.example.com → example.wordpress.com` |
| **MX** | Mail server (email routing) | `example.com → mail.example.com (priority 10)` |
| **NS** | Nameserver cho domain | `example.com → ns1.digitalocean.com` |
| **TXT** | Text arbitrary — SPF, DKIM, verification | `"v=spf1 include:_spf.google.com ~all"` |
| **PTR** | Reverse lookup (IP → hostname) | `34.216.184.93.in-addr.arpa → www.example.com` |
| **SOA** | Start of Authority — thông tin chính của zone | Serial, refresh, retry, expire, TTL |
| **SRV** | Service location (port + hostname) | `_http._tcp.example.com → 10 5 80 www.example.com` |
| **CAA** | Xác định CA được phép cấp cert | `example.com → letsencrypt.org` |

### DNS Caching và TTL

**TTL (Time To Live)**: số giây DNS record được cache.

```
example.com.  3600  IN  A  93.184.216.34
              ^^^^
              TTL = 3600 giây = 1 giờ
```

**Cache layers:**
1. **Browser cache**: Chrome cache DNS 1 phút (có thể xem tại `chrome://net-internals/#dns`)
2. **OS cache**: `nscd`, Windows DNS Client service
3. **Recursive resolver cache**: ISP hoặc 8.8.8.8 cache theo TTL
4. **Negative caching**: NXDOMAIN (domain không tồn tại) cũng được cache (SOA minimum TTL)

**Khi thay đổi DNS record**: phải chờ TTL cũ hết hạn trên toàn hệ thống. Trick: giảm TTL xuống 300s trước khi thay đổi, sau đó thay đổi, chờ 5 phút, tăng TTL lại.

### DNS over HTTPS (DoH) và DNS over TLS (DoT)

DNS truyền thống dùng UDP port 53 — **plaintext**, ISP và các bên trung gian có thể:
- Theo dõi bạn đang truy cập domain nào
- DNS spoofing (chỉnh sửa response)

| | Truyền thống | DoT | DoH |
|-|-------------|-----|-----|
| Port | UDP/TCP 53 | TCP 853 | HTTPS 443 |
| Mã hóa | Không | TLS | TLS qua HTTPS |
| Phát hiện | Dễ (port 53) | Dễ (port 853) | Khó (lẫn với HTTPS) |
| Privacy | Không | Tốt | Tốt nhất |
| Hỗ trợ | Universal | Limited | Firefox, Chrome, Windows 11 |

### DNSSEC — DNS Security Extensions

DNSSEC ký số (digital signature) các DNS record để ngăn spoofing:
- Mỗi zone có cặp key ZSK (Zone Signing Key) và KSK (Key Signing Key)
- Client verify chữ ký dọc theo chain từ root → TLD → authoritative
- Không mã hóa (vẫn plaintext), chỉ đảm bảo **integrity và authenticity**

### DNS Attacks

| Tấn công | Cơ chế | Phòng chống |
|----------|--------|-------------|
| DNS Spoofing / Cache Poisoning | Inject bản ghi giả vào cache resolver | DNSSEC, kiểm tra source port và transaction ID ngẫu nhiên |
| DNS Amplification DDoS | Gửi query với IP giả (victim) đến resolver open → resolver gửi response lớn về victim | Rate limiting, không cho phép recursive resolver public |
| DNS Tunneling | Encode data trong DNS queries (C2 malware) | DNS query analytics, block unusual query patterns |
| NXDOMAIN attack | Query hàng triệu domain không tồn tại để làm nghẽn resolver | Rate limiting per IP |

---

## Định nghĩa chính xác

**DNS** (Domain Name System) là hệ thống phân cấp, phân tán để ánh xạ human-readable domain names thành IP addresses và các thông tin khác (MX, TXT, ...). Được định nghĩa trong RFC 1034 và RFC 1035. DNS hoạt động theo mô hình client-server, thường dùng UDP port 53 (TCP khi response > 512 bytes hoặc zone transfer). Hệ thống bao gồm recursive resolvers, root name servers, TLD name servers, và authoritative name servers.

---

## Đặc điểm kỹ thuật / So sánh

| Tiêu chí | DNS (UDP) | DNS (TCP) | DoT | DoH |
|----------|-----------|-----------|-----|-----|
| Port | 53 | 53 | 853 | 443 |
| Transport | UDP | TCP | TLS/TCP | HTTPS |
| Max payload | 512 bytes (EDNS: 4096) | Unlimited | Unlimited | Unlimited |
| Latency | Thấp nhất | Cao hơn | Cao hơn | Cao nhất |
| Dùng khi | Query thông thường | Zone transfer, large response | Privacy | Privacy + bypass filtering |

---

## Code mẫu

```python
import socket
import dns.resolver  # pip install dnspython

# ── 1. DNS lookup cơ bản bằng socket
hostname = "google.com"
ip = socket.gethostbyname(hostname)
print(f"{hostname} → {ip}")

# Tất cả IP (round-robin)
all_ips = socket.getaddrinfo(hostname, 80)
for item in all_ips:
    print(item[4][0])

# ── 2. Reverse DNS lookup (PTR record)
ip = "8.8.8.8"
try:
    result = socket.gethostbyaddr(ip)
    print(f"Reverse DNS: {ip} → {result[0]}")  # dns.google
except socket.herror:
    print("No PTR record")

# ── 3. Query DNS records với dnspython
resolver = dns.resolver.Resolver()
resolver.nameservers = ['8.8.8.8', '1.1.1.1']  # dùng Google + Cloudflare

# A record
answers = resolver.resolve('google.com', 'A')
print("A records:")
for rdata in answers:
    print(f"  {rdata.address}")

# MX record
answers = resolver.resolve('gmail.com', 'MX')
print("MX records:")
for rdata in sorted(answers, key=lambda r: r.preference):
    print(f"  Priority {rdata.preference}: {rdata.exchange}")

# TXT record (SPF)
answers = resolver.resolve('google.com', 'TXT')
print("TXT records:")
for rdata in answers:
    print(f"  {rdata.strings}")

# NS record
answers = resolver.resolve('google.com', 'NS')
print("NS records:")
for rdata in answers:
    print(f"  {rdata.target}")

# CNAME
try:
    answers = resolver.resolve('www.github.com', 'CNAME')
    for rdata in answers:
        print(f"CNAME: www.github.com → {rdata.target}")
except dns.resolver.NoAnswer:
    print("No CNAME (direct A record)")

# ── 4. Check DNS propagation — so sánh từ nhiều resolver
def check_propagation(domain, record_type='A'):
    resolvers = {
        'Google': '8.8.8.8',
        'Cloudflare': '1.1.1.1',
        'OpenDNS': '208.67.222.222',
    }
    for name, ns in resolvers.items():
        r = dns.resolver.Resolver()
        r.nameservers = [ns]
        try:
            answers = r.resolve(domain, record_type)
            ips = [str(a) for a in answers]
            print(f"  {name} ({ns}): {ips}")
        except Exception as e:
            print(f"  {name}: Error — {e}")

print("DNS propagation check for example.com:")
check_propagation("example.com")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**DNS dùng khi:**
- Cần ánh xạ domain → IP (luôn dùng)
- Email routing (MX record)
- Xác minh domain ownership (TXT record: Google Search Console, Let's Encrypt)
- Load balancing địa lý (Geo DNS / Anycast)
- Service discovery trong microservices (SRV record hoặc DNS-based)

**Lưu ý TTL:**
- TTL thấp (60–300s): tốt cho thay đổi thường xuyên, nhưng tăng DNS query load
- TTL cao (86400s = 1 ngày): ít query hơn, nhưng thay đổi mất nhiều thời gian propagate

**Không nên:**
- Dùng DNS round-robin làm load balancer chính — client cache IP, không thấy server down
- Để TTL quá thấp thường xuyên — tăng tải cho resolver

---

## So sánh với các cơ chế discovery khác

| | DNS | /etc/hosts | mDNS | Service Mesh |
|-|-----|------------|------|-------------|
| Scope | Internet-wide | Local machine only | Local network | Cluster-internal |
| Cập nhật | Propagate theo TTL | Instant | Broadcast | Instant |
| Use case | Mọi nơi | Testing, override | IoT, Bonjour | Kubernetes, Istio |
| Privacy | Thấp (UDP plaintext) | N/A | LAN only | Tốt (mTLS) |

---

## Lỗi thường gặp (Common Pitfalls)

- **Quên giảm TTL trước khi migration**: nếu TTL=86400, sau khi đổi DNS record phải chờ 1 ngày. Giảm TTL xuống 300 trước ít nhất 1 ngày trước khi migrate.
- **CNAME trỏ vào CNAME (chain)**: gây thêm DNS lookup, giảm performance. Mỗi CNAME là 1 query thêm.
- **CNAME cho apex domain (naked domain)**: `example.com` không thể dùng CNAME (RFC cấm). Dùng ALIAS/ANAME record hoặc A record trực tiếp.
- **Nhầm A vs CNAME**: Dùng CNAME khi cần alias, A khi có IP cụ thể. CNAME cho subdomain, không cho root domain.
- **Không có MX record**: email gửi đến domain đó sẽ thất bại.
- **DNS cache poisoning không phòng**: resolver không bật DNSSEC validation.
- **Hardcode IP thay vì domain**: nếu server đổi IP, phải deploy lại code. Dùng domain + DNS.

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích quá trình DNS resolution từng bước khi gõ `google.com` vào trình duyệt.
- Sự khác biệt giữa recursive và iterative DNS resolution?
- TTL là gì? Tại sao quan trọng khi migrate server?
- A record vs CNAME — khi nào dùng cái nào? Tại sao không dùng CNAME cho apex domain?
- DNS cache poisoning là gì? DNSSEC giải quyết ra sao?
- Sự khác biệt giữa DoH và DoT? Cái nào privacy hơn?
- Có bao nhiêu root name server? Tại sao không nhiều hơn/ít hơn?
- Làm thế nào DNS được dùng để load balance traffic?
- Tại sao DNS dùng UDP thay vì TCP? Khi nào dùng TCP?
