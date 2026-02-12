# URL Shortener - Capacity Estimation

## 📊 Traffic Estimation

### Writes (URL Creation)

10,000,000 URLs per day

10,000,000 / 86,400 ≈ 115 writes per second

---

### Redirects (Reads)

100,000,000 redirects per day

100,000,000 / 86,400 ≈ 1,157 QPS (average)

Assume 4x peak traffic:

Peak QPS ≈ 5,000

---

## 🗄 Storage Estimation

### Data Stored Per URL

- Short code: 8 bytes
- Original URL: 100 bytes (average)
- Created timestamp: 8 bytes
- Expiry timestamp: 8 bytes
- Click count: 4 bytes

Approximate per record ≈ 150 bytes (including indexing overhead)

---

### Total URLs in 10 Years

10M × 365 × 10 = 36.5 Billion URLs

36.5B × 150 bytes ≈ 5.5 TB

---

## ⚡ Application Servers

Peak QPS = 5,000

If one server handles 500 QPS:

5,000 / 500 = 10 servers

Add 30% buffer:

Deploy 12–15 application servers

---

## 🚀 Cache Strategy

- Use Redis cluster
- 80% cache hit assumed
- 20% requests hit DB

DB QPS under normal conditions:

20% of 5,000 = 1,000 QPS

Database must handle full 5,000 QPS in worst-case cache failure.

---

## 🗄 Database Choice

- Use SQL database
- Primary key: shortCode
- Indexed lookup (O(log n))
- 1 primary + multiple read replicas
- No sharding initially
- Sharding introduced when vertical scaling is insufficient

---

## 🆔 ID Generation Strategy

- DB auto-increment ID
- Encode using Base62
- Avoid collision
- Upgrade to Snowflake-based distributed ID if scaling further
