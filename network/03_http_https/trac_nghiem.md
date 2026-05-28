# Trắc nghiệm — HTTP & HTTPS

> **Tổng số câu:** 21
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** HTTP method nào dùng để lấy tài nguyên từ server mà không thay đổi state?

- A. POST
- B. PUT
- C. GET
- D. DELETE

> **Đáp án: C**
> **Giải thích:** GET là Safe và Idempotent — không thay đổi state server, chỉ đọc dữ liệu. POST tạo tài nguyên mới (có side effect), PUT thay thế tài nguyên, DELETE xóa.

---

**Câu 2:** HTTP status code 404 có nghĩa là gì?

- A. Server lỗi nội bộ
- B. Client chưa được authenticate
- C. Tài nguyên không tìm thấy
- D. Request bị redirect

> **Đáp án: C**
> **Giải thích:** 404 Not Found — server không tìm thấy tài nguyên tại URL đó. 500 = Server Error, 401 = Unauthorized (chưa authenticate), 301/302 = Redirect.

---

**Câu 3:** HTTPS khác HTTP ở điểm nào?

- A. HTTPS dùng UDP thay vì TCP
- B. HTTPS thêm TLS để mã hóa traffic giữa client và server
- C. HTTPS dùng port 8080 thay vì 80
- D. HTTPS chỉ dùng được trên website lớn

> **Đáp án: B**
> **Giải thích:** HTTPS = HTTP + TLS. TLS mã hóa toàn bộ HTTP payload, đảm bảo confidentiality (mã hóa), integrity (không bị sửa), authentication (cert chứng minh server là đúng). HTTPS dùng port 443 (không phải 8080).

---

**Câu 4:** Status code 201 có nghĩa là gì?

- A. Request thành công, không có content trả về
- B. Tài nguyên được tạo thành công
- C. Request đang được xử lý
- D. Server cần authentication

> **Đáp án: B**
> **Giải thích:** 201 Created — thường được trả về sau POST request tạo tài nguyên mới. Response thường kèm `Location` header chỉ đến URL của tài nguyên mới. 204 No Content — thành công nhưng không có body.

---

**Câu 5:** HTTP method nào dùng để cập nhật một phần tài nguyên?

- A. PUT
- B. POST
- C. PATCH
- D. UPDATE

> **Đáp án: C**
> **Giải thích:** PATCH cập nhật một phần tài nguyên (partial update). PUT thay thế toàn bộ tài nguyên (nếu gửi partial với PUT, server có thể xóa các field không được gửi). UPDATE không phải HTTP method chuẩn.

---

**Câu 6:** HTTP hoạt động ở tầng nào trong mô hình TCP/IP?

- A. Transport
- B. Internet
- C. Network Access
- D. Application

> **Đáp án: D**
> **Giải thích:** HTTP là application-layer protocol, chạy trên TCP (Transport layer). HTTP/3 chạy trên QUIC (dựa trên UDP, nhưng QUIC implement reliability ở transport, HTTP/3 vẫn ở Application layer).

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Sự khác biệt chính giữa HTTP/1.1 và HTTP/2 là gì?

- A. HTTP/2 dùng UDP thay vì TCP
- B. HTTP/2 hỗ trợ multiplexing — nhiều request/response song song trên 1 TCP connection
- C. HTTP/2 không cần TLS
- D. HTTP/2 sử dụng ASCII format thay vì binary

> **Đáp án: B**
> **Giải thích:** HTTP/2 key features: binary framing (không phải text), multiplexing (nhiều stream song song — không bị HOL blocking ở application layer), header compression (HPACK), Server Push. HTTP/1.1 dù có pipelining nhưng bị HOL blocking (response phải theo đúng thứ tự request).

---

**Câu 8:** Idempotent nghĩa là gì trong HTTP?

- A. Request không có side effect
- B. Gọi N lần cho kết quả server giống như gọi 1 lần
- C. Request không cần authentication
- D. Response luôn là 200 OK

> **Đáp án: B**
> **Giải thích:** Idempotent = gọi nhiều lần = gọi 1 lần (về state server). GET, PUT, DELETE là idempotent. POST không idempotent (POST 2 lần tạo 2 resource). Safe là tập con của idempotent — không thay đổi state (GET, HEAD, OPTIONS).

---

**Câu 9:** Trong TLS handshake, mục đích của certificate là gì?

- A. Mã hóa HTTP payload
- B. Xác thực identity của server — client kiểm tra cert do CA đáng tin cậy ký
- C. Tăng tốc độ kết nối
- D. Lưu trữ session cookie

> **Đáp án: B**
> **Giải thích:** Server gửi X.509 certificate chứa public key và được ký bởi CA (Certificate Authority). Client verify chữ ký này dựa trên CA root certs có sẵn trong OS/browser. Đây là cơ chế xác thực — đảm bảo bạn đang nói chuyện đúng với google.com, không phải kẻ giả mạo.

---

**Câu 10:** CORS preflight request dùng method nào?

- A. GET
- B. POST
- C. OPTIONS
- D. HEAD

> **Đáp án: C**
> **Giải thích:** Browser tự động gửi OPTIONS request trước khi gửi request "non-simple" (PUT, DELETE, custom headers). Server trả về `Access-Control-Allow-*` headers để nói browser request nào được phép. Browser sau đó mới gửi request thực.

---

**Câu 11:** `Cache-Control: no-cache` có nghĩa là gì?

- A. Không lưu cache gì cả
- B. Phải validate với server trước khi dùng cache
- C. Cache vĩnh viễn
- D. Chỉ CDN mới được cache

> **Đáp án: B**
> **Giải thích:** `no-cache` ≠ không có cache. no-cache = có thể lưu cache nhưng phải gửi conditional request (If-None-Match / If-Modified-Since) để validate trước khi dùng. Server trả 304 Not Modified nếu chưa thay đổi → dùng cache. `no-store` mới là không lưu gì cả.

---

**Câu 12:** 401 vs 403 — sự khác biệt?

- A. 401 = Server Error, 403 = Client Error
- B. 401 = chưa authenticate (thiếu/sai credentials), 403 = đã authenticate nhưng không có quyền
- C. 401 = Not Found, 403 = Forbidden vĩnh viễn
- D. Không có sự khác biệt thực tế

> **Đáp án: B**
> **Giải thích:** 401 Unauthorized = "tôi không biết bạn là ai" (missing/invalid authentication). Thường kèm `WWW-Authenticate` header hướng dẫn cách authenticate. 403 Forbidden = "tôi biết bạn là ai nhưng bạn không được phép làm điều này" (authorization failure).

---

**Câu 13:** Perfect Forward Secrecy trong TLS đảm bảo điều gì?

- A. Kết nối nhanh hơn
- B. Leak private key của server không decrypt được các session đã ghi lại trước đó
- C. Certificate không thể bị giả mạo
- D. Client cũng phải có certificate

> **Đáp án: B**
> **Giải thích:** PFS dùng ECDHE (ephemeral Diffie-Hellman): mỗi session có key exchange riêng (ephemeral key), key này bị xóa sau session. Nếu attacker ghi lại encrypted traffic và sau này lấy được long-term private key của server → vẫn không decrypt được (không có ephemeral key của session đó).

---

**Câu 14:** HTTP/3 khác HTTP/2 ở điểm nào quan trọng nhất?

- A. HTTP/3 không hỗ trợ multiplexing
- B. HTTP/3 chạy trên QUIC (UDP) thay vì TCP — giải quyết TCP-level HOL blocking
- C. HTTP/3 không cần TLS
- D. HTTP/3 chỉ dùng được trên Chrome

> **Đáp án: B**
> **Giải thích:** HTTP/2 trên TCP vẫn bị HOL blocking ở TCP level: nếu 1 TCP packet bị mất, tất cả streams phải chờ retransmit. HTTP/3 trên QUIC (UDP): mỗi QUIC stream độc lập → packet loss của stream A không block stream B. Ngoài ra: 0-RTT connection resumption, TLS 1.3 built-in.

---

## Phần 3 — Nâng cao (câu 15–21)

**Câu 15:** Khi browser thấy `Strict-Transport-Security: max-age=31536000`, nó làm gì?

- A. Encrypt tất cả cookies trong 1 năm
- B. Trong 1 năm, tự động upgrade mọi request HTTP thành HTTPS cho domain đó
- C. Cache tất cả response trong 1 năm
- D. Không cho phép browser mở trang trong 1 năm

> **Đáp án: B**
> **Giải thích:** HSTS (HTTP Strict Transport Security): browser nhớ rằng domain này phải dùng HTTPS trong `max-age` giây (31536000s = 1 năm). Mọi request http:// sẽ tự động được browser chuyển thành https:// mà không cần redirect từ server. Ngăn chặn SSL stripping attack.

---

**Câu 16:** JWT (JSON Web Token) có vấn đề gì với việc revocation?

- A. JWT không thể revoke được vì không cần query database
- B. JWT tự động expire sau 24 giờ
- C. JWT revoke được bằng cách xóa cookie
- D. JWT revoke được bằng cách thay đổi secret key

> **Đáp án: A**
> **Giải thích:** JWT là stateless — server verify bằng signature, không query database. Nếu JWT bị leak và còn hạn, không thể invalidate mà không có blacklist hoặc thay đổi secret (sẽ invalidate tất cả tokens). Giải pháp: short expiry (15-60 phút) + refresh token, hoặc token blacklist (redis).

---

**Câu 17:** Tại sao `Access-Control-Allow-Origin: *` không hoạt động khi có `credentials: true`?

- A. Browser không hiểu ký tự `*`
- B. RFC/spec yêu cầu origin cụ thể khi có credentials để ngăn leak credentials sang bất kỳ origin
- C. Server cần restart để nhận cấu hình này
- D. Chỉ HTTPS mới hỗ trợ credentials

> **Đáp án: B**
> **Giải thích:** Khi request có credentials (cookies, Authorization header), browser yêu cầu server phải chỉ định chính xác origin (`Access-Control-Allow-Origin: https://app.com`), không được dùng `*`. Lý do bảo mật: nếu `*` + credentials được phép, bất kỳ trang nào cũng có thể dùng credentials của user để gọi API.

---

**Câu 18:** Server Push trong HTTP/2 có ý nghĩa gì và tại sao đang bị deprecated?

- A. Server push là tính năng gửi notification real-time; deprecated vì overhead quá cao
- B. Server chủ động gửi resource (CSS, JS) trước khi client hỏi; deprecated vì khó implement đúng và thường không tốt hơn preload hints
- C. Server push là keepalive mechanism; deprecated vì không an toàn
- D. Server push cho phép server gửi binary data; deprecated vì JSON phổ biến hơn

> **Đáp án: B**
> **Giải thích:** HTTP/2 Server Push: server có thể gửi style.css khi client request index.html, trước khi client parse HTML và tự request CSS. Vấn đề: server không biết client đã có cache chưa (có thể push dư thừa), khó implement đúng, performance không cải thiện nhiều so với `<link rel="preload">`. Chrome đã bỏ hỗ trợ Server Push.

---

**Câu 19:** ETag và If-None-Match hoạt động như thế nào trong caching?

- A. ETag là timestamp của response; If-None-Match gửi timestamp để kiểm tra
- B. Server gửi ETag (fingerprint), client gửi lại qua If-None-Match; server trả 304 nếu không đổi
- C. ETag mã hóa response body; client dùng để giải mã
- D. ETag chỉ dùng với CDN, không phải browser

> **Đáp án: B**
> **Giải thích:** Conditional caching flow: (1) Server gửi `ETag: "abc123"` với response. (2) Lần sau, client gửi `If-None-Match: "abc123"`. (3) Server check: nếu resource chưa thay đổi → 304 Not Modified (không gửi body, tiết kiệm bandwidth). Nếu đã thay đổi → 200 OK với ETag mới.

---

**Câu 20:** Phân tích đoạn header sau và chọn mô tả đúng:

```
Cache-Control: public, max-age=86400, stale-while-revalidate=3600
```

- A. Cache 1 ngày, CDN được phép cache, nhưng phải xóa sau 1 ngày
- B. Cache 1 ngày (public CDN được), và trong 1 giờ sau khi hết hạn vẫn có thể phục vụ stale content trong khi revalidate ngầm
- C. Cache 1 ngày, chỉ browser cache, không CDN
- D. Cache hết hạn sau 86400 lần request

> **Đáp án: B**
> **Giải thích:** `public` = CDN có thể cache. `max-age=86400` = cache 1 ngày. `stale-while-revalidate=3600` = trong 3600 giây sau khi max-age hết hạn, vẫn được phục vụ cached response (stale) trong khi ngầm revalidate. Giảm latency vì không cần chờ revalidate hoàn tất.

---

**Câu 21:** Trong TLS 1.3, tại sao không còn RSA key exchange?

- A. RSA bị phát hiện có backdoor
- B. RSA key exchange không hỗ trợ Perfect Forward Secrecy (ephemeral key)
- C. RSA quá chậm cho TLS 1.3
- D. RSA không tương thích với AES-GCM

> **Đáp án: B**
> **Giải thích:** Trong TLS 1.2, RSA key exchange: client encrypt pre-master secret bằng server's public RSA key. Nếu server's private key bị leak sau này, attacker có thể decrypt tất cả traffic đã ghi lại. TLS 1.3 chỉ cho phép ECDHE key exchange (ephemeral) — mỗi session có ephemeral key riêng, đảm bảo PFS.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | C      | 12  | B      |
| 2   | C      | 13  | B      |
| 3   | B      | 14  | B      |
| 4   | B      | 15  | B      |
| 5   | C      | 16  | A      |
| 6   | D      | 17  | B      |
| 7   | B      | 18  | B      |
| 8   | B      | 19  | B      |
| 9   | B      | 20  | B      |
| 10  | C      | 21  | B      |
| 11  | B      |     |        |
