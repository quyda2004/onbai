# Network Roadmap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       COMPUTER NETWORK LEARNING PATH                    │
│                   Mũi tên = cần học trước (prerequisite)                │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  🌐 01 · OSI Model  │
                        │  (7 tầng mạng)      │
                        └──────────┬──────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  🔗 02 · TCP / IP   │
                        │  (bộ giao thức lõi) │
                        └──────┬──────┬───────┘
                               │      │
                     ┌─────────┘      └──────────┐
                     │                           │
            ┌────────▼─────────┐       ┌─────────▼────────┐
            │  🌍 03 ·         │       │  📡 04 · DNS     │
            │  HTTP / HTTPS    │       │  (phân giải tên)  │
            │  (giao thức web) │       └─────────┬─────────┘
            └────────┬─────────┘                 │
                     │                           │
                     └────────────┬──────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🔌 05 · Socket      │
                        │  (lập trình mạng)    │
                        └─────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🛡️ 06 · Security    │
                        │  (bảo mật mạng)      │
                        └──────────────────────┘
```

---

## Giải thích từng topic

| # | Topic | Mô tả ngắn | Tài liệu |
|---|-------|------------|----------|
| 01 | 🌐 OSI Model | 7 tầng: Physical → Data Link → Network → Transport → Session → Presentation → Application | [📖 Lý thuyết](01_osi_model/ly_thuyet.md) · [📝 Trắc nghiệm](01_osi_model/trac_nghiem.md) |
| 02 | 🔗 TCP / IP | TCP (reliable, có ACK) vs UDP (nhanh, không đảm bảo) — 3-way handshake, flow control | [📖 Lý thuyết](02_tcp_ip/ly_thuyet.md) · [📝 Trắc nghiệm](02_tcp_ip/trac_nghiem.md) |
| 03 | 🌍 HTTP / HTTPS | Request/Response, methods GET/POST/PUT/DELETE, status code, TLS/SSL, HTTP/2 vs HTTP/3 | [📖 Lý thuyết](03_http_https/ly_thuyet.md) · [📝 Trắc nghiệm](03_http_https/trac_nghiem.md) |
| 04 | 📡 DNS | Phân giải domain → IP — recursive query, DNS cache, A/CNAME/MX records | [📖 Lý thuyết](04_dns/ly_thuyet.md) · [📝 Trắc nghiệm](04_dns/trac_nghiem.md) |
| 05 | 🔌 Socket | TCP/UDP socket, bind/listen/accept/connect — nền tảng của mọi network app | [📖 Lý thuyết](05_socket/ly_thuyet.md) · [📝 Trắc nghiệm](05_socket/trac_nghiem.md) |
| 06 | 🛡️ Security | Mã hoá symmetric/asymmetric, TLS handshake, firewall, DDoS, MITM, HTTPS | [📖 Lý thuyết](06_security/ly_thuyet.md) · [📝 Trắc nghiệm](06_security/trac_nghiem.md) |

---

## Lộ trình theo giai đoạn

```
  Giai đoạn 1  ──▶  OSI Model  ──▶  TCP/IP
                                       │
  Giai đoạn 2  ◀─────────────────────┘
       │
       ▼
  HTTP/HTTPS  ──▶  DNS
       │              │
       └──────┬───────┘
              │
  Giai đoạn 3 ▼
       Socket Programming
              │
  Giai đoạn 4 ▼
       Network Security
              │
        ✅ Backend Dev
        Interview Ready
```

---

## Quan hệ giữa các topic

```
  OSI Model   ─────▶  TCP/IP      (TCP/IP map vào tầng 3-4 của OSI)
  TCP/IP      ─────▶  HTTP/HTTPS  (HTTP chạy trên TCP tầng 7)
  TCP/IP      ─────▶  DNS         (DNS dùng UDP port 53)
  DNS         ─────▶  HTTP/HTTPS  (browser resolve DNS trước khi gửi HTTP request)
  HTTP/HTTPS  ─────▶  Security    (HTTPS = HTTP + TLS)
  TCP/IP      ─────▶  Socket      (Socket API wrap TCP/UDP)
  Socket      ─────▶  Security    (cần biết socket trước khi học TLS/firewall)
```
