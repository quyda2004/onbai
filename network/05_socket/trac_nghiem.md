# Trắc nghiệm — Socket Programming

> **Tổng số câu:** 20
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)
> Mỗi câu có 4 đáp án (A/B/C/D), ghi rõ đáp án đúng và giải thích.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Socket được xác định duy nhất bởi bộ thông tin nào?

- A. IP address và hostname
- B. IP address, port, và protocol (5-tuple với src+dst)
- C. MAC address và port
- D. Chỉ cần port number

> **Đáp án: B**
> **Giải thích:** Một TCP connection được xác định bởi 5-tuple: (src_ip, src_port, dst_ip, dst_port, protocol). Điều này cho phép nhiều connection đến cùng server:443 — mỗi connection có src_port khác nhau.

---

**Câu 2:** `SOCK_STREAM` tương ứng với giao thức nào?

- A. UDP
- B. ICMP
- C. TCP
- D. IP

> **Đáp án: C**
> **Giải thích:** `socket.SOCK_STREAM` = TCP (connection-oriented, reliable, stream). `socket.SOCK_DGRAM` = UDP (connectionless, unreliable, datagram). `socket.SOCK_RAW` = raw IP socket.

---

**Câu 3:** Thứ tự đúng của TCP Server socket lifecycle là gì?

- A. socket → bind → accept → listen → recv
- B. socket → bind → listen → accept → recv/send
- C. socket → connect → bind → listen → accept
- D. socket → listen → bind → accept → recv

> **Đáp án: B**
> **Giải thích:** TCP Server: socket() tạo socket → bind() gắn vào địa chỉ → listen() chuyển sang trạng thái nghe → accept() chờ và chấp nhận connection mới → recv()/send() trao đổi data → close().

---

**Câu 4:** Client cần gọi hàm nào để kết nối đến server?

- A. bind()
- B. listen()
- C. accept()
- D. connect()

> **Đáp án: D**
> **Giải thích:** Client: socket() → connect() (kết nối đến server, three-way handshake) → send()/recv() → close(). Client không cần bind() thường xuyên (OS tự gán ephemeral port), không dùng listen() và accept().

---

**Câu 5:** `SO_REUSEADDR` socket option có tác dụng gì?

- A. Cho phép nhiều process chia sẻ cùng port
- B. Cho phép tái sử dụng địa chỉ ở TIME_WAIT — bind lại port ngay sau khi server restart
- C. Tăng kích thước socket buffer
- D. Disable Nagle's algorithm

> **Đáp án: B**
> **Giải thích:** Sau khi server close, port ở TIME_WAIT ~60 giây. Nếu restart ngay → `bind() failed: Address already in use`. `SO_REUSEADDR` cho phép bind lại port dù vẫn ở TIME_WAIT. Gần như mọi TCP server đều cần option này.

---

**Câu 6:** Unix Domain Socket khác Network Socket ở điểm nào?

- A. Unix Domain Socket hỗ trợ UDP, Network Socket chỉ hỗ trợ TCP
- B. Unix Domain Socket dùng file path thay vì IP+port, chỉ giao tiếp trong cùng machine
- C. Unix Domain Socket không hỗ trợ bidirectional communication
- D. Unix Domain Socket cần quyền root

> **Đáp án: B**
> **Giải thích:** Unix Domain Socket (AF_UNIX): địa chỉ là file path (ví dụ `/tmp/app.sock`), không qua network stack → overhead thấp hơn, nhanh hơn ~2x, chỉ dùng được trong cùng machine. Dùng cho IPC: Nginx ↔ PHP-FPM, Redis local access.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Blocking socket vs Non-blocking socket — sự khác biệt?

- A. Blocking socket chỉ nhận data lớn hơn 1KB
- B. Blocking socket block thread cho đến khi có data/connection; non-blocking return ngay với error nếu chưa sẵn sàng
- C. Non-blocking socket không hỗ trợ TCP
- D. Blocking socket nhanh hơn vì không cần polling

> **Đáp án: B**
> **Giải thích:** Blocking `recv()`: thread sleep cho đến khi có data (efficient cho single-connection). Non-blocking `recv()`: ngay lập tức trả về `BlockingIOError` nếu không có data — cần polling hoặc kết hợp với select/epoll để hiệu quả.

---

**Câu 8:** `select()` và `epoll()` có chức năng gì chung?

- A. Mã hóa socket data
- B. I/O multiplexing — monitor nhiều file descriptor, thông báo khi có event
- C. Load balance connections giữa nhiều server
- D. Compress data trước khi gửi

> **Đáp án: B**
> **Giải thích:** select() và epoll() đều là I/O multiplexing: cho phép 1 thread monitor nhiều socket và biết socket nào có data để đọc/ghi mà không cần block hoặc polling. Sự khác biệt: epoll O(1) per event, select O(n) scan toàn bộ fds mỗi lần gọi.

---

**Câu 9:** Tại sao `epoll()` hiệu quả hơn `select()` cho nhiều connection?

- A. epoll() hỗ trợ UDP còn select() thì không
- B. epoll() chỉ trả về các fd có event (O(1) per event), select() phải scan toàn bộ fd_set O(n)
- C. epoll() có giới hạn 1024 connections, select() không có
- D. epoll() tự động close idle connections

> **Đáp án: B**
> **Giải thích:** select(): mỗi lần gọi phải copy fd_set kernel↔userspace và scan tất cả fds dù chỉ 1 có event → O(n). epoll(): kernel dùng event-driven mechanism, chỉ trả về fds có event → O(1) per event, O(active_events) total. Với 10K connections nhưng chỉ 100 active, epoll >> select.

---

**Câu 10:** C10K Problem đề cập đến vấn đề gì?

- A. Xử lý file lớn hơn 10KB qua socket
- B. Handle 10,000 concurrent connections với 1 server
- C. Giảm latency xuống dưới 10ms
- D. Compress 10,000 packets mỗi giây

> **Đáp án: B**
> **Giải thích:** C10K (10K connections): vào cuối thập niên 1990, server web khó xử lý 10K concurrent connections. Thread-per-connection cần 10GB RAM (1MB stack/thread). Giải pháp: async I/O + event loop (Nginx, Node.js, asyncio).

---

**Câu 11:** WebSocket upgrade từ HTTP diễn ra như thế nào?

- A. Client mở TCP connection mới trên port 9000
- B. Client gửi HTTP GET với `Upgrade: websocket` header, server trả 101 Switching Protocols
- C. Server gửi PUSH_PROMISE để bắt đầu WebSocket
- D. Client và server đồng ý qua DNS TXT record

> **Đáp án: B**
> **Giải thích:** WebSocket bắt đầu từ HTTP handshake: client gửi `Upgrade: websocket` và `Sec-WebSocket-Key`. Server trả `101 Switching Protocols` và `Sec-WebSocket-Accept`. Sau đó, cùng TCP connection được dùng cho WebSocket framing — bidirectional, full-duplex.

---

**Câu 12:** `send()` vs `sendall()` — khi nào dùng `sendall()`?

- A. `sendall()` dùng khi gửi binary data, `send()` cho text
- B. `send()` có thể gửi ít hơn số bytes yêu cầu; `sendall()` đảm bảo gửi hết, tự loop
- C. `sendall()` nhanh hơn `send()`
- D. `sendall()` chỉ dùng với UDP

> **Đáp án: B**
> **Giải thích:** `send()` trả về số bytes thực sự đã gửi (có thể ít hơn data). Lý do: kernel buffer đầy → partial send. Phải loop để đảm bảo gửi hết. `sendall()` tự làm điều này — an toàn hơn. Chỉ dùng `send()` khi cần biết exact bytes sent.

---

**Câu 13:** Backlog trong `listen(backlog)` có nghĩa là gì?

- A. Số lượng byte tối đa được buffer
- B. Số lượng incomplete connections (SYN_RCVD) + complete connections chờ accept()
- C. Timeout trước khi từ chối connection
- D. Số thread xử lý connection

> **Đáp án: B**
> **Giải thích:** `listen(backlog)` thiết lập kích thước queue chờ: (1) incomplete connection queue (SYN_RCVD, chờ ACK) + (2) complete connection queue (ESTABLISHED, chờ accept()). Nếu queue đầy, kernel drop hoặc RST incoming SYN. Với nhiều connection đồng thời, tăng backlog (thường 128–4096).

---

**Câu 14:** Tại sao `recv()` trả về `b""` (empty bytes)?

- A. Không có data trong buffer
- B. Server gửi empty string
- C. Connection bị đóng bởi đối phương (graceful close, FIN received)
- D. Buffer overflow

> **Đáp án: C**
> **Giải thích:** `recv()` trả về `b""` khi peer đã gửi FIN (đóng connection). Đây là cách Python/socket API báo hiệu EOF. Phải xử lý case này để break khỏi loop đọc. Khác với blocking (thread đang chờ data) và error (exception).

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** Phân tích đoạn code sau — vấn đề là gì?

```python
while True:
    conn, addr = server.accept()
    data = conn.recv(4096)
    response = process(data)
    conn.sendall(response)
    conn.close()
```

- A. Không có vấn đề gì
- B. Sequential server — chỉ xử lý 1 connection tại một thời điểm, blocking toàn bộ khi process() chậm
- C. Không close server socket
- D. recv() không đủ 4096 bytes

> **Đáp án: B**
> **Giải thích:** Đây là sequential server: sau khi accept(), xử lý xong mới accept() tiếp. Nếu process() mất 5 giây, các client khác phải chờ hàng. Giải pháp: spawn thread/process per connection, hoặc dùng async/await + epoll.

---

**Câu 16:** Edge-triggered vs Level-triggered trong epoll — sự khác biệt?

- A. Edge-triggered kiểm tra liên tục, level-triggered chỉ kiểm tra khi có event
- B. Edge-triggered notify khi có state change; level-triggered notify khi fd ở trạng thái sẵn sàng (có data)
- C. Edge-triggered cho TCP, level-triggered cho UDP
- D. Không có sự khác biệt thực tế

> **Đáp án: B**
> **Giải thích:** `EPOLLET` (edge-triggered): chỉ báo một lần khi state thay đổi (ví dụ: data mới đến). Phải đọc hết data ngay vì không báo lại. `EPOLLIN` mặc định (level-triggered): báo liên tục khi còn data chưa đọc trong buffer. Edge-triggered efficient hơn nhưng khó implement đúng (dễ bỏ sót data).

---

**Câu 17:** TIME_WAIT ảnh hưởng đến server như thế nào và giải pháp là gì?

- A. Không ảnh hưởng gì, TIME_WAIT chỉ ở client
- B. Nhiều short-lived connections → nhiều socket ở TIME_WAIT → có thể hết ephemeral ports → reject connections; giải pháp: SO_REUSEADDR, connection pooling, `tcp_tw_reuse`
- C. TIME_WAIT tăng latency của mỗi request
- D. TIME_WAIT chỉ xảy ra khi server crash

> **Đáp án: B**
> **Giải thích:** Server xử lý nhiều short HTTP connections (hoặc server là client connect đến DB): mỗi close connection tạo TIME_WAIT state ~60s. Với nhiều request/s, có thể có hàng ngàn sockets ở TIME_WAIT, chiếm ephemeral ports (49152–65535 = 16K ports). Giải pháp: `SO_REUSEADDR`, `net.ipv4.tcp_tw_reuse=1`, connection pooling (tái dùng connection thay vì close).

---

**Câu 18:** Tại sao HTTP persistent connection (keep-alive) quan trọng?

- A. Giúp mã hóa data tốt hơn
- B. Tránh TCP three-way handshake và TLS handshake overhead cho mỗi request khi dùng connection lại
- C. Cho phép server push notification
- D. Tăng buffer size cho từng request

> **Đáp án: B**
> **Giải thích:** Mỗi new TCP connection cần 1 RTT (handshake) + 1-2 RTT (TLS). Với persistent connection, nhiều HTTP request dùng chung 1 connection → tiết kiệm latency. HTTP/1.1 default keep-alive, HTTP/2 bắt buộc persistent, WebSocket thì luôn persistent.

---

**Câu 19:** Khi implement chat server với WebSocket, tại sao không dùng threading mà dùng asyncio?

- A. asyncio nhanh hơn threading
- B. Threading không hỗ trợ WebSocket
- C. 10K users = 10K threads (10GB RAM, context switch overhead); asyncio/event loop dùng 1 thread, switch giữa connections khi idle
- D. asyncio đảm bảo message ordering tốt hơn

> **Đáp án: C**
> **Giải thích:** Chat server: hầu hết connections đang idle (chờ message). Thread-per-connection: 10K thread × 1MB stack = 10GB RAM + kernel context switch overhead. Async event loop: 1 thread, non-blocking I/O, switch khi await → có thể handle 100K+ idle connections với ít tài nguyên.

---

**Câu 20:** `sock.settimeout(5)` có ý nghĩa gì và tại sao cần?

- A. Giới hạn kích thước data nhận trong 5 giây
- B. Sau 5 giây không có data hoặc connection, raise socket.timeout — tránh hang vĩnh viễn
- C. Tự động retry sau 5 giây
- D. Giữ connection alive trong 5 giây

> **Đáp án: B**
> **Giải thích:** Không set timeout → blocking socket hang mãi nếu server không respond (network issue, server down). `settimeout(5)` → raise `socket.timeout` sau 5 giây → có thể handle error, retry, hoặc inform user. Cần thiết cho mọi production socket code. Sau timeout, socket vẫn usable (khác với close).

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 11  | B      |
| 2   | C      | 12  | B      |
| 3   | B      | 13  | B      |
| 4   | D      | 14  | C      |
| 5   | B      | 15  | B      |
| 6   | B      | 16  | B      |
| 7   | B      | 17  | B      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | C      |
| 10  | B      | 20  | B      |
