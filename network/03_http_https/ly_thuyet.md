# HTTP/HTTPS — Giao thức nền tảng của World Wide Web

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn vào một tiệm ăn. Bạn (client) gọi món cho bồi bàn (server): "Cho tôi một tô phở đặc biệt" (request). Bồi bàn đi vào bếp, một lúc sau mang ra tô phở cho bạn (response).

**HTTP** hoạt động đúng theo quy trình đó:
1. Trình duyệt của bạn gửi yêu cầu (request) đến máy chủ web
2. Máy chủ xử lý và gửi trả nội dung (response) — trang HTML, ảnh, video...
3. Trình duyệt hiển thị nội dung đó lên màn hình

**HTTPS** giống như tiệm ăn đó có phòng riêng kín đáo và bồi bàn ký hợp đồng bảo mật: mọi cuộc trò chuyện được mã hóa, không ai nghe lén được. Chữ "S" viết tắt cho "Secure".

Điểm quan trọng: HTTP là **stateless** — bồi bàn không nhớ bạn là ai từ lần trước. Mỗi lần bạn vào tiệm, bạn phải tự giới thiệu lại (đó là lý do cần cookie/session để "nhớ" bạn đã đăng nhập).

---

## Giải thích cho người đã biết lập trình (nâng cao)

### HTTP là Application Layer Protocol

HTTP (HyperText Transfer Protocol) chạy trên TCP (port 80) hoặc TLS+TCP (HTTPS, port 443). Là giao thức **request-response**, **stateless**, **text-based** (HTTP/1.x).

### HTTP Request Structure

```
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
Accept: application/json
Content-Length: 45

{"name": "Alice", "email": "alice@example.com"}
```

Cấu trúc:
- **Request Line**: `METHOD /path HTTP/version`
- **Headers**: Key-Value, mỗi dòng một header
- **Blank line**: phân cách header và body
- **Body** (tùy chọn): dữ liệu gửi kèm

### HTTP Response Structure

```
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/users/123
X-Request-ID: abc-def-456
Date: Wed, 28 May 2026 10:00:00 GMT

{"id": 123, "name": "Alice", "created_at": "2026-05-28"}
```

Cấu trúc:
- **Status Line**: `HTTP/version STATUS_CODE reason`
- **Headers**
- **Blank line**
- **Body**

### HTTP Methods — Idempotent và Safe

| Method  | Safe | Idempotent | Body? | Mô tả |
|---------|------|------------|-------|-------|
| GET     | Có   | Có         | Không | Lấy resource |
| HEAD    | Có   | Có         | Không | Như GET nhưng không có body |
| OPTIONS | Có   | Có         | Không | Hỏi server hỗ trợ những gì |
| POST    | Không| Không      | Có    | Tạo resource mới |
| PUT     | Không| Có         | Có    | Thay thế toàn bộ resource |
| PATCH   | Không| Không*     | Có    | Cập nhật một phần resource |
| DELETE  | Không| Có         | Tùy   | Xóa resource |

- **Safe**: Không thay đổi state server
- **Idempotent**: Gọi nhiều lần = kết quả giống gọi 1 lần (`PUT /users/1` nhiều lần vẫn ra user đó)

### HTTP Status Codes

| Nhóm | Ý nghĩa | Codes quan trọng |
|------|---------|-----------------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved Permanently, 302 Found, 304 Not Modified, 307 Temporary Redirect |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 405 Method Not Allowed, 409 Conflict, 422 Unprocessable Entity, 429 Too Many Requests |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout |

Lưu ý quan trọng:
- **401 vs 403**: 401 = chưa xác thực (chưa login), 403 = đã xác thực nhưng không có quyền
- **301 vs 302**: 301 = permanent redirect (browser cache URL mới), 302 = tạm thời
- **502 vs 503**: 502 = upstream server lỗi, 503 = server quá tải hoặc bảo trì

### HTTP Headers quan trọng

**Request Headers:**
```
Host: example.com                    # Bắt buộc trong HTTP/1.1
Accept: application/json, */*
Accept-Encoding: gzip, deflate, br
Accept-Language: vi-VN, en-US
Authorization: Bearer <token>        # Xác thực
Cookie: session_id=abc123
Cache-Control: no-cache
If-None-Match: "etag-value"          # Conditional request
If-Modified-Since: Mon, 25 May 2026
User-Agent: Mozilla/5.0 ...
Origin: https://myapp.com            # CORS
```

**Response Headers:**
```
Content-Type: application/json; charset=utf-8
Content-Length: 1234
Content-Encoding: gzip
Cache-Control: max-age=3600, public
ETag: "abc123def"
Last-Modified: Mon, 25 May 2026 10:00:00 GMT
Set-Cookie: session_id=xyz; HttpOnly; Secure; SameSite=Strict
Access-Control-Allow-Origin: https://myapp.com  # CORS
Strict-Transport-Security: max-age=31536000; includeSubDomains  # HSTS
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
```

### CORS (Cross-Origin Resource Sharing)

```
Browser (https://app.com) → GET https://api.example.com/data

1. Preflight (OPTIONS) — với non-simple requests:
   OPTIONS /data HTTP/1.1
   Origin: https://app.com
   Access-Control-Request-Method: POST
   Access-Control-Request-Headers: Content-Type

2. Server response:
   Access-Control-Allow-Origin: https://app.com
   Access-Control-Allow-Methods: GET, POST
   Access-Control-Allow-Headers: Content-Type
   Access-Control-Max-Age: 86400

3. Actual request nếu preflight OK
```

### HTTP/1.1 vs HTTP/2 vs HTTP/3

| Tính năng | HTTP/1.1 | HTTP/2 | HTTP/3 (QUIC) |
|-----------|----------|--------|----------------|
| Transport | TCP | TCP | UDP (QUIC) |
| Text/Binary | Text | Binary (framing) | Binary |
| Multiplexing | Không (pipelining kém) | Có (streams) | Có |
| Head-of-Line Blocking | Có (TCP) | Ở TCP level | Không |
| Header Compression | Không | HPACK | QPACK |
| Server Push | Không | Có | Có (hạn chế) |
| Connection per domain | 6-8 | 1 | 1 |
| 0-RTT | Không | Không | Có |

**HTTP/2 Multiplexing**: Nhiều request/response chạy song song trên 1 TCP connection bằng cơ chế streams. Mỗi stream có stream ID, frames được interleaved.

**HTTP/3 / QUIC**: Chạy trên UDP, tích hợp TLS 1.3, mỗi stream độc lập → không bị head-of-line blocking ở transport layer.

### HTTPS = HTTP + TLS

**TLS 1.3 Handshake (1-RTT)**:
```
Client                              Server
  |                                   |
  |  ClientHello                      |
  |  (TLS version, cipher suites,     |
  |   key_share, random)              |
  |---------------------------------->|
  |                                   |
  |  ServerHello                      |
  |  (selected cipher, key_share,     |
  |   Certificate, Finished)          |
  |<----------------------------------|
  |                                   |
  |  (Client verify cert)             |
  |  Finished                         |
  |  [Application Data]               |
  |---------------------------------->|
  |  [Application Data]               |
  |<----------------------------------|
```

**Certificate Chain**:
```
Root CA (tự ký, được OS/browser tin tưởng sẵn)
    └── Intermediate CA (ký bởi Root CA)
            └── Server Certificate (ký bởi Intermediate CA)
                    contains: domain, public key, validity, issuer
```

Browser verify:
1. Server cert ký bởi Intermediate CA? → check chữ ký
2. Intermediate CA ký bởi Root CA? → check chữ ký
3. Root CA có trong trust store không?
4. Cert chưa hết hạn và không bị revoke (OCSP/CRL)?

### REST Principles (Roy Fielding, 2000)

1. **Client-Server**: Tách biệt UI và data storage
2. **Stateless**: Mỗi request mang đủ thông tin, server không lưu session client
3. **Cacheable**: Response phải chỉ rõ có cache được không
4. **Uniform Interface**: Resource có URI cố định, manipulation qua representations
5. **Layered System**: Client không biết đang nói chuyện với server hay proxy
6. **Code on Demand** (tùy chọn): Server có thể gửi code (JavaScript) cho client chạy

### Cookies vs Sessions vs JWT

| | Cookie | Server Session | JWT |
|-|--------|---------------|-----|
| Lưu ở đâu | Browser | Server memory/DB | Client (localStorage/cookie) |
| Stateful/less | Stateful | Stateful | Stateless |
| Scalability | Tốt | Kém (sticky session hoặc shared store) | Tốt (horizontal scale) |
| Security | HttpOnly+Secure tốt | An toàn (data ở server) | Không thể revoke dễ (cần blacklist) |
| Size | ~4KB giới hạn | Không giới hạn | ~1-2KB typical |
| CSRF risk | Có | Có | Không (nếu không dùng cookie) |

---

## Định nghĩa chính xác

**HTTP (HyperText Transfer Protocol)**: Giao thức tầng ứng dụng (RFC 9110) theo mô hình request-response, stateless, chạy trên TCP. Là nền tảng truyền tải dữ liệu trên World Wide Web.

**HTTPS**: HTTP được bảo mật bằng TLS (Transport Layer Security). Cung cấp ba đảm bảo: confidentiality (mã hóa), integrity (không bị sửa đổi), authentication (xác thực server).

**REST (Representational State Transfer)**: Kiến trúc phần mềm cho distributed hypermedia systems, không phải giao thức. API tuân theo REST được gọi là RESTful API.

---

## Bảng / Sơ đồ kỹ thuật

### HTTP Request-Response Flow qua HTTPS

```
Browser                 DNS          TCP/TLS          Web Server
   |                     |               |                 |
   | DNS query(example.com)              |                 |
   |-------------------->|               |                 |
   | IP: 93.184.216.34   |               |                 |
   |<--------------------|               |                 |
   |                                     |                 |
   | TCP SYN                             |                 |
   |------------------------------------>|                 |
   | TCP SYN-ACK                         |                 |
   |<------------------------------------|                 |
   | TCP ACK                             |                 |
   |------------------------------------>|                 |
   |                                     |                 |
   | TLS ClientHello                     |                 |
   |------------------------------------>|                 |
   | TLS ServerHello + Cert              |                 |
   |<------------------------------------|                 |
   | TLS Finished (encrypted)            |                 |
   |------------------------------------>|                 |
   |                                     |                 |
   | HTTP GET / (encrypted)              |                 |
   |------------------------------------>|---------------->|
   |                                     | HTTP/1.1 GET /  |
   |                             HTTP/1.1 200 OK           |
   |<------------------------------------|<----------------|
   | HTML response (encrypted)           |                 |
```

### Cache-Control Directives

| Directive | Ý nghĩa |
|-----------|---------|
| `max-age=3600` | Cache trong 3600 giây |
| `no-cache` | Cache nhưng phải validate với server trước khi dùng |
| `no-store` | Không cache gì cả |
| `private` | Chỉ browser cache, không CDN/proxy |
| `public` | CDN/proxy có thể cache |
| `must-revalidate` | Hết hạn phải revalidate, không dùng stale |
| `immutable` | Content không bao giờ thay đổi (hash trong URL) |

---

## Code mẫu

### Python requests library — GET và POST

```python
import requests
import json

BASE_URL = "https://jsonplaceholder.typicode.com"

# --- GET Request ---
def get_user(user_id: int):
    headers = {
        "Accept": "application/json",
        "Authorization": "Bearer my-token-here",
    }
    
    # requests tự động: DNS, TCP, TLS, HTTP
    response = requests.get(
        f"{BASE_URL}/users/{user_id}",
        headers=headers,
        timeout=10  # seconds
    )
    
    # Raise exception nếu 4xx hoặc 5xx
    response.raise_for_status()
    
    print(f"Status: {response.status_code}")
    print(f"Content-Type: {response.headers['Content-Type']}")
    print(f"Response time: {response.elapsed.total_seconds():.3f}s")
    
    return response.json()


# --- POST Request ---
def create_post(title: str, body: str, user_id: int):
    payload = {
        "title": title,
        "body": body,
        "userId": user_id
    }
    
    response = requests.post(
        f"{BASE_URL}/posts",
        json=payload,         # Tự động set Content-Type: application/json
        timeout=10
    )
    
    response.raise_for_status()
    return response.json()


# --- Session (reuse TCP connection + cookies) ---
def demo_session():
    with requests.Session() as session:
        # Set headers mặc định cho tất cả request trong session
        session.headers.update({
            "User-Agent": "MyApp/1.0",
            "Accept": "application/json"
        })
        
        # Login (server gửi Set-Cookie, session tự lưu cookie)
        login_resp = session.post(
            "https://httpbin.org/post",
            json={"username": "alice", "password": "secret"}
        )
        
        # Request sau tự động gửi cookie
        data_resp = session.get("https://httpbin.org/cookies")
        print(data_resp.json())


# --- Xử lý lỗi đúng cách ---
def robust_get(url: str, retries: int = 3):
    for attempt in range(retries):
        try:
            response = requests.get(url, timeout=5)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.Timeout:
            print(f"Attempt {attempt+1}: Timeout, retrying...")
        except requests.exceptions.HTTPError as e:
            if e.response.status_code == 404:
                return None  # Không retry với 404
            if e.response.status_code >= 500:
                print(f"Server error {e.response.status_code}, retrying...")
            else:
                raise  # 4xx khác thì raise luôn
        except requests.exceptions.ConnectionError:
            print(f"Connection failed, retrying...")
    
    raise Exception(f"Failed after {retries} retries")


if __name__ == "__main__":
    user = get_user(1)
    print("User:", json.dumps(user, indent=2))
    
    post = create_post("Test title", "Test body", user_id=1)
    print("Created:", json.dumps(post, indent=2))
```

### HTTP Server đơn giản bằng Python

```python
from http.server import HTTPServer, BaseHTTPRequestHandler
import json

class SimpleHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path == '/health':
            self._send_json(200, {"status": "ok"})
        elif self.path.startswith('/users/'):
            user_id = self.path.split('/')[-1]
            self._send_json(200, {"id": int(user_id), "name": "Alice"})
        else:
            self._send_json(404, {"error": "Not found"})
    
    def do_POST(self):
        content_length = int(self.headers.get('Content-Length', 0))
        body = self.rfile.read(content_length)
        data = json.loads(body)
        
        # Tạo resource mới
        new_resource = {"id": 123, **data}
        self._send_json(201, new_resource, extra_headers={
            "Location": f"/users/{new_resource['id']}"
        })
    
    def _send_json(self, status: int, data: dict, extra_headers: dict = None):
        body = json.dumps(data).encode()
        self.send_response(status)
        self.send_header('Content-Type', 'application/json')
        self.send_header('Content-Length', len(body))
        if extra_headers:
            for k, v in extra_headers.items():
                self.send_header(k, v)
        self.end_headers()
        self.wfile.write(body)
    
    def log_message(self, format, *args):
        print(f"[HTTP] {self.address_string()} - {format % args}")

if __name__ == "__main__":
    server = HTTPServer(('0.0.0.0', 8080), SimpleHandler)
    print("Server running on http://localhost:8080")
    server.serve_forever()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng HTTP/REST khi:**
- API công khai, client đa dạng (web, mobile, third-party)
- CRUD operations đơn giản
- Cần cacheability (GET requests)
- Team quen thuộc, không cần schema chặt chẽ

**Dùng HTTPS luôn luôn khi:**
- Bất kỳ dữ liệu nhạy cảm nào (password, token, PII)
- Ngày nay: mọi production web đều phải dùng HTTPS

**Xem xét thay thế HTTP/REST khi:**
- Cần real-time hai chiều → WebSocket hoặc SSE
- Cần hiệu năng cao, schema chặt chẽ → gRPC (HTTP/2 + Protobuf)
- Cần flexible query → GraphQL
- Microservices nội bộ → gRPC hoặc message queue

**Không nên dùng REST khi:**
- Cần streaming dữ liệu liên tục (ticker, sensor)
- Cần binary protocol hiệu năng cao giữa services

---

## Lỗi thường gặp (Common Pitfalls)

1. **Dùng GET để thay đổi state**: `GET /deleteUser?id=1` là sai — GET phải safe. Proxy và browser có thể cache hoặc prefetch GET request.

2. **Nhầm 401 và 403**: 401 = "Bạn là ai?" (cần authentication), 403 = "Tôi biết bạn là ai nhưng bạn không có quyền" (authorization failed).

3. **Không handle 429 Too Many Requests**: Cần implement exponential backoff + jitter khi retry.

4. **Cache-Control sai trên private data**: Thiếu `private` directive → CDN/proxy cache dữ liệu nhạy cảm của user.

5. **CORS misconfiguration**: `Access-Control-Allow-Origin: *` với `Access-Control-Allow-Credentials: true` → browser chặn (không cho phép).

6. **HTTP/1.1 Keep-Alive hiểu sai**: Connection reuse không phải multiplexing. HTTP/1.1 vẫn phải xử lý request tuần tự trên 1 connection.

7. **Không set timeout**: `requests.get(url)` mặc định không timeout → treo indefinitely.

8. **Body trong GET request**: Về mặt spec cho phép nhưng nhiều server/proxy/firewall bỏ qua. Tránh dùng body trong GET.

---

## Câu hỏi phỏng vấn hay gặp

1. **HTTP stateless nghĩa là gì? Làm sao duy trì state?**
   - Server không lưu thông tin về request trước. Duy trì state qua: Cookie (browser gửi kèm mỗi request), JWT token trong Authorization header, Server-side session với session ID.

2. **Sự khác biệt PUT và PATCH?**
   - PUT: thay thế toàn bộ resource (idempotent). PATCH: cập nhật một phần (không bắt buộc idempotent theo RFC).

3. **Làm sao HTTPS bảo vệ dữ liệu?**
   - TLS handshake: xác thực server (certificate), trao đổi symmetric key qua asymmetric encryption, sau đó mã hóa symmetric. Cung cấp: encryption, integrity (HMAC), authentication.

4. **HTTP/2 cải thiện gì so với HTTP/1.1?**
   - Multiplexing (multiple streams on 1 connection), header compression (HPACK), binary framing, server push.

5. **CORS là gì và tại sao tồn tại?**
   - Same-Origin Policy: browser chặn JS gửi request đến domain khác. CORS là cơ chế server cho phép cross-origin request bằng response headers.

6. **Idempotent nghĩa là gì? Tại sao quan trọng?**
   - Gọi N lần = kết quả giống gọi 1 lần. Quan trọng cho retry logic: nếu không biết request đã thành công chưa, có thể safely retry idempotent requests.

7. **Giải thích TLS certificate chain hoạt động thế nào?**
   - Browser tin Root CA sẵn, Root CA ký Intermediate CA, Intermediate CA ký Server cert. Chain of trust cho phép verify server cert mà không cần trust trực tiếp.

8. **Cookie HttpOnly và Secure flag có tác dụng gì?**
   - HttpOnly: JS không thể đọc cookie (chống XSS lấy cắp cookie). Secure: chỉ gửi qua HTTPS (chống network sniffing).
