# Network Security — Bảo mật Mạng

---

## Giải thích cho người mới hoàn toàn

Tưởng tượng nhà bạn có nhiều lớp bảo vệ: **cổng ngoài** (tường lửa — chặn người lạ vào), **két sắt** (mã hóa — dù vào nhà cũng không lấy được đồ), **camera an ninh** (IDS — phát hiện kẻ xâm nhập), và **bảo vệ** (IPS — chặn kẻ xâm nhập lại).

Bảo mật mạng cũng vậy — dữ liệu của bạn cần nhiều lớp bảo vệ khi đi qua Internet:
- **Firewall** ngăn traffic nguy hiểm vào/ra
- **Mã hóa (TLS)** đảm bảo dù bị nghe lén cũng không đọc được
- **Authentication** xác minh đúng người dùng/server
- **IDS/IPS** phát hiện và chặn tấn công

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Firewall

**Stateless (Packet Filter):** kiểm tra từng packet độc lập theo rules (src_ip, dst_ip, port, protocol). Nhanh, nhưng không hiểu context — dễ bypass bằng cách chia nhỏ packet.

**Stateful Inspection:** theo dõi **connection state** (SYN_SENT, ESTABLISHED, ...). Chặn packet giả mạo thuộc connection không tồn tại. Đây là chuẩn hiện đại.

**Application Layer Gateway (L7 Firewall / WAF):**
- Hiểu nội dung protocol (HTTP, DNS, FTP)
- Phát hiện SQL injection, XSS trong HTTP payload
- Deep Packet Inspection (DPI)

**iptables (Linux):**
```bash
# Chặn kết nối đến port 22 từ ngoài
iptables -A INPUT -p tcp --dport 22 -j DROP

# Cho phép traffic đã established
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Rate limiting — chặn brute force
iptables -A INPUT -p tcp --dport 22 -m recent --update --seconds 60 --hitcount 4 -j DROP
```

### VPN — Virtual Private Network

| VPN Protocol | Layer | Mã hóa | Tốc độ | Use case |
|-------------|-------|--------|--------|----------|
| IPSec | L3 | AES, 3DES | Nhanh | Site-to-site, enterprise |
| OpenVPN | L4/L7 | TLS/OpenSSL | Trung bình | Remote access |
| WireGuard | L3 | ChaCha20-Poly1305 | Nhanh nhất | Modern VPN, gaming |
| L2TP/IPSec | L2+L3 | IPSec | Trung bình | Built-in OS support |
| PPTP | L2 | MPPE | Nhanh nhưng yếu | Legacy, KHÔNG dùng |

**IPSec Modes:**
- **Transport Mode**: chỉ encrypt payload, giữ nguyên IP header. Dùng cho host-to-host.
- **Tunnel Mode**: encrypt toàn bộ packet, thêm IP header mới. Dùng cho gateway-to-gateway (VPN).

**WireGuard** sử dụng Curve25519 (ECDH), ChaCha20-Poly1305 — code base nhỏ (~4000 LOC vs ~100k của OpenVPN), audit được dễ hơn.

### TLS/SSL Deep Dive

**Cipher Suite** — ví dụ: `TLS_AES_256_GCM_SHA384`

| Phần | Ý nghĩa | Ví dụ |
|------|---------|-------|
| Key Exchange | Trao đổi khóa | ECDHE (ephemeral Diffie-Hellman) |
| Authentication | Xác thực server | RSA, ECDSA |
| Encryption | Mã hóa data | AES-256-GCM, ChaCha20-Poly1305 |
| MAC | Integrity | SHA-384 |

**Perfect Forward Secrecy (PFS):** dùng **ephemeral** key cho key exchange (ECDHE). Mỗi session có key riêng → leak private key không decrypt được session cũ.

**Certificate Pinning:** client hardcode public key/cert của server, từ chối kết nối nếu cert không khớp. Chống MITM ngay cả khi CA bị compromise. Dùng trong mobile app.

**mTLS (Mutual TLS):** cả client VÀ server đều xác thực bằng certificate. Dùng trong microservices (service mesh Istio/Linkerd).

### Common Attacks

#### 1. Man-in-the-Middle (MITM)

```
Client ←→ Attacker ←→ Server

Attacker có thể:
- Đọc traffic (nếu không mã hóa)
- Inject data
- Replay old requests
```

**Phòng chống:** HTTPS (TLS), Certificate Pinning, HSTS (HTTP Strict Transport Security).

**HSTS:** server gửi header `Strict-Transport-Security: max-age=31536000; includeSubDomains`. Browser nhớ trong 1 năm, tự động dùng HTTPS cho mọi request — không thể bị redirect về HTTP.

#### 2. DDoS (Distributed Denial of Service)

| Loại | Cơ chế | Ví dụ | Phòng chống |
|------|--------|-------|-------------|
| Volumetric | Overwhelm bandwidth | UDP flood, ICMP flood | Rate limiting, Anycast scrubbing |
| Protocol | Khai thác weakness giao thức | SYN flood, Smurf attack | SYN cookies, firewall |
| Application | Khai thác L7 | HTTP flood, Slowloris | WAF, rate limiting, CAPTCHA |

**SYN Flood:** attacker gửi SYN với IP giả → server tạo half-open connection, queue đầy → từ chối connection hợp lệ.

**SYN Cookies:** server encode session info trong ISN (Initial Sequence Number), không cần lưu state cho half-open connection.

#### 3. ARP Spoofing

```
Attacker gửi ARP reply giả: "IP 192.168.1.1 (gateway) có MAC là AA:BB:CC:DD:EE:FF"
→ Các máy trong LAN cập nhật ARP cache
→ Traffic gửi đến gateway đi qua Attacker trước
```

**Phòng chống:** Dynamic ARP Inspection (DAI) trên switch, static ARP entries, 802.1X authentication.

#### 4. SQL Injection qua Network

```http
POST /login HTTP/1.1
{"username": "admin' OR '1'='1' --", "password": "anything"}
```

**Phòng chống:** Parameterized queries / Prepared statements, WAF, input validation.

#### 5. XSS và CSRF

**XSS (Cross-Site Scripting):**
- Inject malicious JS vào trang web
- Phòng: CSP header, output encoding, HttpOnly cookie

**CSRF (Cross-Site Request Forgery):**
- Lừa browser nạn nhân gửi request đến site đang đăng nhập
- Phòng: CSRF token, SameSite cookie, `Origin`/`Referer` header check

### Authentication & Authorization

**OAuth 2.0 Flow (Authorization Code):**
```
User → Client App → "Login with Google"
Client App → Authorization Server (Google): redirect với client_id, scope, state
User → Login và consent ở Google
Google → redirect về Client App với authorization_code
Client App → Authorization Server: đổi code lấy access_token + refresh_token
Client App → Resource Server (API): gửi kèm access_token
```

**JWT (JSON Web Token):** `header.payload.signature`
- **Không mã hóa** payload (chỉ base64 encode) — không lưu secret
- Verify bằng signature — không cần query database
- Revocation khó — cần blacklist hoặc short expiry + refresh token

**API Key vs JWT vs mTLS:**
| | API Key | JWT | mTLS |
|-|---------|-----|------|
| Revocation | Dễ (xóa key) | Khó | Revoke cert (CRL/OCSP) |
| Stateless | Không (cần lookup) | Có | Có |
| Use case | Simple API | Web/Mobile auth | Microservices |

### IDS vs IPS

| | IDS (Intrusion Detection) | IPS (Intrusion Prevention) |
|-|--------------------------|---------------------------|
| Vị trí | Out-of-band (copy traffic) | Inline (traffic đi qua) |
| Hành động | Alert / Log | Alert + Block |
| False positive ảnh hưởng | Thấp (chỉ alert) | Cao (có thể block nhầm) |
| Latency | Không ảnh hưởng | Thêm latency |

**Signature-based**: so sánh với database known attacks — nhanh nhưng không phát hiện zero-day.
**Anomaly-based**: so sánh với baseline behavior — phát hiện unknown attacks nhưng nhiều false positive.

### Zero Trust Architecture

Nguyên tắc: **"Never trust, always verify"** — không tin tưởng bất kỳ user/device nào chỉ vì họ ở trong mạng nội bộ.

Thay thế mô hình "castle and moat" (VPN vào = tin tưởng hoàn toàn):
- Verify mọi request (identity, device health, context)
- Least privilege access
- Micro-segmentation
- Continuous monitoring

---

## Định nghĩa chính xác

**Network Security** là tập hợp các chính sách, quy trình, và công nghệ nhằm bảo vệ tính **Confidentiality** (bí mật), **Integrity** (toàn vẹn), và **Availability** (sẵn sàng) của tài nguyên mạng và dữ liệu. Ba thuộc tính này gọi là **CIA Triad** — tiêu chuẩn đánh giá bảo mật trong ngành.

---

## Đặc điểm kỹ thuật / So sánh

| Công nghệ | Mục đích | Layer | Ví dụ |
|-----------|---------|-------|-------|
| Firewall | Lọc traffic | L3/L4/L7 | iptables, AWS Security Group |
| WAF | Chống web attack | L7 | Cloudflare, ModSecurity |
| VPN | Tunnel bảo mật | L3/L4 | WireGuard, OpenVPN |
| TLS | Mã hóa transport | L4/L7 | HTTPS, IMAPS |
| IDS | Phát hiện xâm nhập | L3–L7 | Snort, Suricata |
| IPS | Chặn xâm nhập | L3–L7 | Snort inline, Suricata |
| DNSSEC | Bảo vệ DNS | L7 | DNS record signing |
| Zero Trust | Architecture | All | BeyondCorp, Cloudflare Access |

---

## Code mẫu

```python
import ssl
import socket
import hashlib
import hmac

# ── 1. TLS Client — kiểm tra cert và kết nối an toàn
def tls_connect(hostname: str, port: int = 443):
    ctx = ssl.create_default_context()
    # ctx.verify_mode = ssl.CERT_REQUIRED  (mặc định)
    # ctx.check_hostname = True             (mặc định)

    with socket.create_connection((hostname, port), timeout=5) as raw:
        with ctx.wrap_socket(raw, server_hostname=hostname) as tls:
            cert = tls.getpeercert()
            print(f"TLS version : {tls.version()}")
            print(f"Cipher      : {tls.cipher()[0]}")
            print(f"Cert subject: {dict(x[0] for x in cert['subject'])}")
            print(f"Cert issuer : {dict(x[0] for x in cert['issuer'])}")
            print(f"Valid until : {cert['notAfter']}")
    return cert

tls_connect("google.com")

# ── 2. Basic Port Scanner (educational)
import concurrent.futures

def scan_port(host: str, port: int, timeout: float = 0.5) -> bool:
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
            sock.settimeout(timeout)
            return sock.connect_ex((host, port)) == 0
    except Exception:
        return False

def scan_host(host: str, ports: range):
    print(f"Scanning {host}...")
    open_ports = []
    with concurrent.futures.ThreadPoolExecutor(max_workers=100) as executor:
        results = executor.map(lambda p: (p, scan_port(host, p)), ports)
    for port, is_open in results:
        if is_open:
            open_ports.append(port)
            print(f"  Port {port}: OPEN")
    return open_ports

# scan_host("127.0.0.1", range(1, 1025))

# ── 3. HMAC — Message Authentication Code
def generate_hmac(key: str, message: str) -> str:
    """Tạo HMAC để verify message integrity (không thể giả mạo nếu không có key)"""
    h = hmac.new(key.encode(), message.encode(), hashlib.sha256)
    return h.hexdigest()

def verify_hmac(key: str, message: str, signature: str) -> bool:
    expected = generate_hmac(key, message)
    return hmac.compare_digest(expected, signature)  # constant-time comparison

key = "super_secret_key"
msg = "Transfer $1000 to account 12345"
sig = generate_hmac(key, msg)
print(f"HMAC: {sig}")
print(f"Valid: {verify_hmac(key, msg, sig)}")
print(f"Tampered: {verify_hmac(key, 'Transfer $9999 to account 12345', sig)}")

# ── 4. Kiểm tra HSTS và security headers
import urllib.request

def check_security_headers(url: str):
    req = urllib.request.Request(url, headers={'User-Agent': 'SecurityCheck/1.0'})
    with urllib.request.urlopen(req, timeout=5) as resp:
        headers = dict(resp.headers)
        checks = {
            'Strict-Transport-Security': 'HSTS',
            'Content-Security-Policy': 'CSP',
            'X-Frame-Options': 'Clickjacking protection',
            'X-Content-Type-Options': 'MIME sniffing protection',
            'Referrer-Policy': 'Referrer policy',
        }
        for header, description in checks.items():
            value = headers.get(header, 'MISSING')
            status = "✓" if value != 'MISSING' else "✗"
            print(f"  {status} {description}: {value[:60]}")

# check_security_headers("https://google.com")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**TLS/HTTPS — dùng luôn luôn:** không có lý do gì không dùng trong production.

**Certificate Pinning — dùng khi:**
- Mobile app với backend cố định
- High-security app (banking, healthcare)
- Không khuyến khích cho web (khó update khi cert thay đổi)

**VPN — dùng khi:**
- Remote access vào internal network
- Site-to-site connection giữa văn phòng
- Bypass geo-restriction (với điều kiện hợp lệ)

**mTLS — dùng khi:**
- Service-to-service trong microservices
- API giữa các partner (B2B)

---

## Lỗi thường gặp (Common Pitfalls)

- **HTTP thay vì HTTPS trong production**: mọi traffic bị nghe lén.
- **`verify=False` trong requests**: disable certificate validation → MITM attack.
- **JWT lưu secret trong payload**: payload chỉ base64-encoded, không mã hóa — ai cũng đọc được.
- **Không có rate limiting**: API endpoint dễ bị brute force.
- **CORS `Access-Control-Allow-Origin: *` cho authenticated endpoint**: ai cũng có thể gọi API từ bất kỳ domain.
- **Lỗi timing attack khi compare secret**: dùng `==` thay vì `hmac.compare_digest()` → attacker đo thời gian so sánh để đoán secret.
- **Firewall chỉ chặn inbound**: malware đã vào bên trong có thể tự do gửi data ra ngoài.
- **Không có security headers**: thiếu HSTS, CSP, X-Frame-Options → dễ bị XSS, clickjacking.

---

## Câu hỏi phỏng vấn hay gặp

- Stateful vs Stateless firewall — sự khác biệt?
- MITM attack là gì? TLS ngăn chặn thế nào?
- SYN flood là gì? SYN cookies giải quyết ra sao?
- Perfect Forward Secrecy là gì và tại sao quan trọng?
- JWT có những vấn đề bảo mật gì? Cách giải quyết?
- mTLS khác TLS thông thường như thế nào?
- IDS vs IPS — vị trí trong mạng và cách hoạt động?
- Zero Trust Architecture là gì? Tại sao cần?
- HSTS là gì? Tại sao ngăn được downgrade attack?
- OAuth 2.0 Authorization Code flow hoạt động như thế nào?
