# System Design Roadmap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       SYSTEM DESIGN LEARNING PATH                       │
│                   Mũi tên = cần học trước (prerequisite)                │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                        ┌──────────▼──────────┐
                        │  📈 01 · Scalability│
                        │  (khả năng mở rộng) │
                        └──────┬──────┬───────┘
                               │      │
                     ┌─────────┘      └──────────┐
                     │                           │
            ┌────────▼─────────┐       ┌─────────▼────────┐
            │  ⚖️ 02 ·         │       │  ⚡ 03 · Caching │
            │  Load Balancing  │       │  (bộ nhớ đệm)    │
            └────────┬─────────┘       └─────────┬─────────┘
                     │                           │
                     └────────────┬──────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🗄️ 04 · Database    │
                        │  (SQL vs NoSQL,      │
                        │   sharding, replica) │
                        └─────────┬────────────┘
                                  │
                     ┌────────────┴────────────┐
                     │                         │
            ┌────────▼─────────┐     ┌─────────▼────────┐
            │  📨 05 ·         │     │  🔌 06 ·          │
            │  Message Queue   │     │  API Design       │
            │  (Kafka, RabbitMQ│     │  (REST, GraphQL,  │
            │   async tasks)   │     │   gRPC, rate limit│
            └────────┬─────────┘     └─────────┬─────────┘
                     │                         │
                     └────────────┬────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  🧩 07 · Microservices│
                        │  (service mesh,       │
                        │   Docker, K8s)        │
                        └─────────┬─────────────┘
                                  │
                        ┌─────────▼────────────┐
                        │  ⚖️ 08 · Consistency  │
                        │  & Availability      │
                        │  (CAP theorem, ACID, │
                        │   eventual consistency│
                        └──────────────────────┘
```

---

## Giải thích từng topic

| # | Topic | Mô tả ngắn | Tài liệu |
|---|-------|------------|----------|
| 01 | 📈 Scalability | Vertical (nâng cấp máy) vs Horizontal (thêm máy) — stateless design, bottleneck analysis | [📖 Lý thuyết](01_scalability/ly_thuyet.md) · [📝 Trắc nghiệm](01_scalability/trac_nghiem.md) |
| 02 | ⚖️ Load Balancing | Phân phối traffic — Round Robin, Least Connections, L4 vs L7, Nginx/HAProxy | [📖 Lý thuyết](02_load_balancing/ly_thuyet.md) · [📝 Trắc nghiệm](02_load_balancing/trac_nghiem.md) |
| 03 | ⚡ Caching | Redis/Memcached — cache-aside, write-through, TTL, cache invalidation, CDN | [📖 Lý thuyết](03_caching/ly_thuyet.md) · [📝 Trắc nghiệm](03_caching/trac_nghiem.md) |
| 04 | 🗄️ Database | SQL vs NoSQL, indexing, sharding, replication, connection pooling, ACID | [📖 Lý thuyết](04_database/ly_thuyet.md) · [📝 Trắc nghiệm](04_database/trac_nghiem.md) |
| 05 | 📨 Message Queue | Kafka, RabbitMQ — async communication, pub/sub, event-driven, dead letter queue | [📖 Lý thuyết](05_message_queue/ly_thuyet.md) · [📝 Trắc nghiệm](05_message_queue/trac_nghiem.md) |
| 06 | 🔌 API Design | REST vs GraphQL vs gRPC, pagination, versioning, rate limiting, idempotency | [📖 Lý thuyết](06_api_design/ly_thuyet.md) · [📝 Trắc nghiệm](06_api_design/trac_nghiem.md) |
| 07 | 🧩 Microservices | Tách monolith thành services — service discovery, API gateway, circuit breaker | [📖 Lý thuyết](07_microservices/ly_thuyet.md) · [📝 Trắc nghiệm](07_microservices/trac_nghiem.md) |
| 08 | ⚖️ Consistency & Availability | CAP theorem, ACID vs BASE, eventual consistency, quorum, distributed locks | [📖 Lý thuyết](08_consistency_availability/ly_thuyet.md) · [📝 Trắc nghiệm](08_consistency_availability/trac_nghiem.md) |

---

## Lộ trình theo giai đoạn

```
  Giai đoạn 1  ──▶  Scalability
                          │
              ┌───────────┴───────────┐
              │                       │
      Load Balancing              Caching
              │                       │
              └───────────┬───────────┘
                          │
  Giai đoạn 2             ▼
                       Database
                          │
              ┌───────────┴───────────┐
              │                       │
       Message Queue             API Design
              │                       │
              └───────────┬───────────┘
                          │
  Giai đoạn 3             ▼
                     Microservices
                          │
                          ▼
               Consistency & Availability
                          │
                   ✅ System Design
                   Interview Ready
```

---

## Quan hệ giữa các topic

```
  Scalability       ─────▶  Load Balancing       (scale out cần LB phân phối)
  Scalability       ─────▶  Caching              (cache giảm load khi scale)
  Load Balancing    ─────▶  Database             (nhiều instance → cần replica)
  Caching           ─────▶  Consistency          (cache stale → consistency issue)
  Database          ─────▶  Consistency          (ACID, sharding → tradeoff)
  Message Queue     ─────▶  Microservices        (services giao tiếp async qua queue)
  API Design        ─────▶  Microservices        (mỗi service expose API)
  Microservices     ─────▶  Consistency          (distributed system → CAP theorem)
```
