# HTTP & HTTPS

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đang gọi món tại nhà hàng. Bạn (trình duyệt) gọi phục vụ (server) và nói: *"Cho tôi một tô phở"* (request). Phục vụ mang tô phở ra (response). Nếu nhà hàng hết phở, phục vụ nói *"Xin lỗi, hết món rồi"* (status code 404).

**HTTP** là ngôn ngữ giao tiếp đó — quy định cách trình duyệt "hỏi" và server "trả lời".

Còn **HTTPS** giống như cuộc nói chuyện đó diễn ra trong phòng riêng, cách âm hoàn toàn, không ai nghe lén được. Chữ **S** là **Secure** — mọi thứ được mã hóa trước khi truyền đi. Ngay cả khi có kẻ xấu ngồi giữa đường mạng, họ cũng chỉ thấy ký tự vô nghĩa.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### HTTP — Hypertext Transfer Protocol

HTTP là application-layer protocol (L7) chạy trên TCP (HTTP/1.1, HTTP/2) hoặc QUIC (HTTP/3). Mỗi transaction gồm request và response.

**Request structure:**
```
GET /api/users?page=1 HTTP/1.1\r\n
Host: api.example.com\r\n
Authorization: Bearer eyJhbGc...\r\n
Accept: application/json\r\n
\r\n
```

**Response structure:**
```
HTTP/1.1 200 OK\r\n
Content-Type: application/json\r\n
Content-Length: 256\r\n
Cache-Control: max-age=3600\r\n
\r\n
{"users": [...]}
```

### HTTP Methods — Semantics

| Method | Safe? | Idempotent? | Has Body? | Mục đích |
|--------|-------|-------------|-----------|----------|
| GET    | Có    | Có          | Không     | Lấy tài nguyên |
| HEAD   | Có    | Có          | Không     | Chỉ lấy headers (kiểm tra tồn tại, size) |
| OPTIONS| Có    | Có          | Không     | CORS preflight, xem method được phép |
| POST   | Không | Không       | Có        | Tạo tài nguyên mới |
| PUT    | Không | Có          | Có        | Replace **toàn bộ** tài nguyên |
| PATCH  | Không | Không       | Có        | Cập nhật **một phần** tài nguyên |
| DELETE | Không | Có          | Không     | Xóa tài nguyên |

- **Safe**: không thay đổi state server
- **Idempotent**: gọi N lần = gọi 1 lần (kết quả server giống nhau)

### HTTP Status Codes

| Range | Ý nghĩa | Codes quan trọng |
|-------|---------|-----------------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols (WebSocket upgrade) |
| 2xx | Success | 200 OK, 201 Created, 204 No Content, 206 Partial Content |
| 3xx | Redirection | 301 Moved Permanently, 302 Found, 304 Not Modified, 307 Temporary Redirect |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Entity, 429 Too Many Requests |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout |

### HTTP Versions — Evolution

**HTTP/1.0**: mỗi request mở TCP connection mới → TCP handshake overhead mỗi lần.

**HTTP/1.1**:
- **Persistent connection** (`Connection: keep-alive`): tái sử dụng TCP connection
- **Pipelining**: gửi nhiều request mà không cần chờ response, nhưng bị **head-of-line blocking** (response phải trả về đúng thứ tự)
- **Chunked transfer encoding**: stream response mà không cần biết Content-Length trước

**HTTP/2**:
- **Binary framing**: không còn text-based, giảm parsing overhead
- **Multiplexing**: nhiều request/response song song trên 1 TCP connection — không bị application-level HOL blocking
- **Header compression (HPACK)**: nén headers, hiệu quả khi headers lặp lại nhiều
- **Server Push**: server chủ động gửi resource trước khi client hỏi
- Vẫn bị **TCP-level HOL blocking** khi có packet loss (một packet mất → tất cả streams phải chờ)

**HTTP/3**:
- Chạy trên **QUIC** (UDP-based) thay vì TCP
- Không bị TCP HOL blocking (mỗi QUIC stream độc lập)
- **0-RTT connection resumption**: kết nối lại không cần handshake đầy đủ
- TLS 1.3 built-in, bắt buộc

### HTTPS — TLS 1.3 Handshake

```
Client                              Server
  |                                   |
  |---- ClientHello ----------------> |  TLS version, cipher suites, client random, key_share (ECDH public key)
  |                                   |
  |<--- ServerHello ---------------- |  Chọn cipher suite, server random, key_share (ECDH public key)
  |<--- {Certificate} -------------- |  Chứng chỉ X.509 của server
  |<--- {CertificateVerify} -------- |  Chữ ký số — chứng minh server có private key khớp cert
  |<--- {Finished} ----------------- |  MAC của toàn bộ handshake
  |                                   |
  |---- {Finished} ----------------> |  Client xác nhận
  |                                   |
  |<==== Encrypted Application Data ==|  HTTPS traffic bắt đầu
```

**TLS 1.3 so với TLS 1.2:**
- **1-RTT** thay vì 2-RTT
- **0-RTT** (Early Data) cho session resumption — cẩn thận với replay attack
- Loại bỏ cipher suites yếu (RSA key exchange, DES, RC4, MD5, SHA-1)
- **Perfect Forward Secrecy (PFS) bắt buộc**: dùng ECDHE — mỗi session có ephemeral key riêng, leak private key cũ không decrypt được traffic cũ

### Cookie, Session, JWT

| Cơ chế | Lưu ở đâu | Stateful? | Scalable? | Vấn đề bảo mật |
|--------|-----------|-----------|-----------|----------------|
| Session ID (cookie) | Server (memory/Redis) | Có | Cần shared storage | Session hijacking nếu không HTTPS |
| JWT (localStorage) | Client | Không | Tốt | XSS có thể đánh cắp token |
| JWT (HttpOnly cookie) | Client (cookie) | Không | Tốt | CSRF (cần SameSite=Strict hoặc CSRF token) |

**HttpOnly cookie** không đọc được bằng JavaScript → chống XSS.
**SameSite=Strict/Lax** → chống CSRF.

### CORS — Cross-Origin Resource Sharing

Browser enforce **Same-Origin Policy**: JS tại `https://app.com` không thể đọc response từ `https://api.com` trừ khi server cho phép.

**Simple request** (GET/POST với simple headers): browser tự gửi, kiểm tra response headers.
**Preflight** (các method khác hoặc custom headers): browser gửi OPTIONS trước.

```http
# Preflight request
OPTIONS /api/data HTTP/1.1
Origin: https://app.com
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: Authorization

# Server response
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://app.com
Access-Control-Allow-Methods: GET, POST, DELETE
Access-Control-Allow-Headers: Authorization
Access-Control-Max-Age: 86400
```

### HTTP Caching

```
Cache-Control: max-age=3600          → cache 1 giờ, không cần hỏi server
Cache-Control: no-cache              → mỗi lần phải validate với server (conditional GET)
Cache-Control: no-store              → không lưu cache gì cả
Cache-Control: public                → CDN có thể cache
Cache-Control: private               → chỉ browser cache, CDN không được cache
ETag: "abc123"                       → fingerprint của response
If-None-Match: "abc123"              → browser hỏi "vẫn là abc123 không?" → 304 Not Modified nếu không đổi
Last-Modified: Wed, 01 Jan 2025...   → server ghi ngày sửa cuối
If-Modified-Since: Wed, 01 Jan 2025  → browser hỏi "có thay đổi kể từ ngày này không?"
```

---

## Định nghĩa chính xác

**HTTP** (Hypertext Transfer Protocol) là stateless, application-layer protocol theo mô hình client-server. Được định nghĩa trong RFC 9110 (HTTP Semantics), RFC 9112 (HTTP/1.1), RFC 9113 (HTTP/2), RFC 9114 (HTTP/3). Stateless: mỗi request độc lập, server không lưu trạng thái giữa các request.

**HTTPS** = HTTP + TLS (Transport Layer Security). TLS chạy ở tầng giữa Transport và Application, mã hóa toàn bộ HTTP payload. TLS 1.3 được định nghĩa trong RFC 8446.

---

## Đặc điểm kỹ thuật / So sánh HTTP Versions

| Đặc điểm | HTTP/1.0 | HTTP/1.1 | HTTP/2 | HTTP/3 |
|-----------|----------|----------|--------|--------|
| Transport | TCP | TCP | TCP | QUIC (UDP) |
| Connection | Per-request | Persistent | Persistent | Persistent |
| Multiplexing | Không | Pipelining (HOL) | Có | Có |
| Header compression | Không | Không | HPACK | QPACK |
| Server Push | Không | Không | Có | Có |
| TLS bắt buộc | Không | Không | Không | Có |
| RTT handshake | TCP + TLS | TCP + TLS | TCP + TLS | 0-RTT (QUIC) |
| HOL blocking | App + TCP | App + TCP | Chỉ TCP | Không |
| Binary framing | Không | Không | Có | Có |

---

## Code mẫu

```python
import requests
import ssl
import socket

# ── 1. HTTP GET cơ bản
response = requests.get("https://httpbin.org/get", timeout=5)
print(f"Status: {response.status_code}")       # 200
print(f"Headers: {response.headers['Content-Type']}")
print(f"Body: {response.json()}")

# ── 2. POST với JSON body và headers
payload = {"username": "alice", "role": "admin"}
response = requests.post(
    "https://httpbin.org/post",
    json=payload,
    headers={
        "Authorization": "Bearer token123",
        "X-Request-ID": "uuid-here"
    },
    timeout=5
)
print(response.status_code)  # 200

# ── 3. Reuse connection với Session (giống persistent connection)
session = requests.Session()
session.headers.update({"Authorization": "Bearer mytoken"})
r1 = session.get("https://httpbin.org/get")
r2 = session.get("https://httpbin.org/headers")  # tái dùng TCP connection

# ── 4. Kiểm tra TLS certificate
hostname = "google.com"
ctx = ssl.create_default_context()
with ctx.wrap_socket(socket.socket(), server_hostname=hostname) as sock:
    sock.connect((hostname, 443))
    cert = sock.getpeercert()
    print(f"TLS version: {sock.version()}")           # TLSv1.3
    print(f"Cipher: {sock.cipher()}")
    print(f"Expires: {cert['notAfter']}")

# ── 5. Retry với exponential backoff
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

retry_strategy = Retry(
    total=3,
    backoff_factor=1,           # delay: 1s, 2s, 4s
    status_forcelist=[429, 500, 502, 503, 504],
    allowed_methods=["GET", "POST"]
)
adapter = HTTPAdapter(max_retries=retry_strategy)
session = requests.Session()
session.mount("https://", adapter)
session.mount("http://", adapter)

# ── 6. Xem HTTP/2 status (cần httpx)
# import httpx
# with httpx.Client(http2=True) as client:
#     r = client.get("https://www.google.com")
#     print(r.http_version)  # HTTP/2
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**HTTPS (dùng luôn luôn):**
- Mọi production website và API
- Bất kỳ nơi nào truyền thông tin nhạy cảm (auth, payment, PII)
- Bắt buộc để dùng HTTP/2 và HTTP/3
- Google search ranking ưu tiên HTTPS

**HTTP (chỉ dùng):**
- Development local (localhost)
- Internal services trong private network có mTLS hoặc service mesh

**HTTP/2 (dùng khi):**
- Web app với nhiều API request cùng lúc (multiplexing)
- Muốn giảm latency nhờ header compression

**HTTP/3 (dùng khi):**
- Mobile app (network thay đổi thường xuyên)
- Video streaming, real-time application
- Môi trường có packet loss cao (Wi-Fi kém, mobile)

---

## So sánh với các giao thức liên quan

| | HTTP/REST | WebSocket | gRPC | GraphQL |
|-|-----------|-----------|------|---------|
| Pattern | Request-Response | Bidirectional stream | RPC / Streaming | Query-based |
| Protocol | HTTP/1.1, 2, 3 | Upgrade từ HTTP | HTTP/2 | HTTP |
| Real-time | Không (cần polling/SSE) | Có | Có (server streaming) | Subscription |
| Type safety | Không (JSON) | Không | Có (Protobuf) | Partial (schema) |
| Overhead | Medium | Low | Very low | Medium |
| Use case | REST API | Chat, games, live | Microservices | Flexible data query |

---

## Lỗi thường gặp (Common Pitfalls)

- **PUT vs PATCH nhầm lẫn**: PUT phải gửi toàn bộ resource — nếu gửi partial thì server có thể xóa các field không gửi. Dùng PATCH cho partial update.
- **401 vs 403 nhầm**: 401 = chưa authenticated (không có/sai token), 403 = đã authenticated nhưng không có quyền.
- **Caching endpoint có side effect**: GET endpoint có side effect sẽ bị browser/CDN cache, gây data stale.
- **CORS `*` với credentials**: `Access-Control-Allow-Origin: *` không hoạt động khi `credentials: true` — phải chỉ định origin cụ thể.
- **Không validate TLS cert**: `verify=False` trong requests library → MITM attack.
- **JWT trong localStorage**: dễ bị XSS. Dùng HttpOnly cookie.
- **Không đặt timeout**: request có thể hang vô hạn → connection pool exhaustion.
- **HTTP/1.1 với quá nhiều domain**: browser giới hạn 6 TCP connections/domain — dùng HTTP/2 hoặc domain sharding (legacy).

---

## Câu hỏi phỏng vấn hay gặp

- GET vs POST — sự khác biệt, khi nào dùng cái nào?
- Idempotent là gì? Tại sao DELETE được coi là idempotent?
- Sự khác biệt chính giữa HTTP/1.1, HTTP/2, HTTP/3?
- TLS handshake hoạt động như thế nào? Tại sao cần 3-way TCP + TLS?
- Perfect Forward Secrecy là gì và tại sao quan trọng?
- CORS là gì? Tại sao browser enforce CORS nhưng Postman thì không?
- 401 vs 403 — khi nào dùng cái nào?
- Cookie HttpOnly vs localStorage — cái nào bảo mật hơn, tại sao?
- ETag và Cache-Control hoạt động như thế nào?
- HTTP/2 multiplexing giải quyết vấn đề gì của HTTP/1.1?
