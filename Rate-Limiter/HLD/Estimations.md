# Rate Limiter - Capacity Estimation

## 1️⃣ Assumptions

- Peak Traffic: 1 Million Requests Per Second (1M RPS)
- 10 Million active users
- Limit example: 100 requests per minute per user
- Decision latency target: < 5ms

---

## 2️⃣ Rate Limit Checks

At 1M RPS:

→ 1M rate limit decisions per second
→ 60M per minute
→ 3.6B per hour

The system must handle at least 1M counter operations per second.

---

## 3️⃣ Counter Storage Estimation

Assume:

- 10M active users
- Each user has 1 active counter (current minute)

Each counter stores:
- UserID (8 bytes)
- Count (4 bytes)
- Expiry timestamp (8 bytes)
≈ 20 bytes per counter (rounded to 32 bytes with overhead)

Memory needed:

10M users × 32 bytes ≈ 320 MB

Even if doubled for safety:
≈ 640 MB

This easily fits in distributed Redis.

---

## 4️⃣ Read/Write Operations

For Fixed Window algorithm:

Each request requires:
- 1 read
- 1 increment
- 1 expiry check

Using Redis INCR with TTL makes this atomic.

Total operations:

1M INCR per second

Redis can handle:
~100K ops/sec per core

So we need:

At least 10–15 Redis nodes (clustered).

---

## 5️⃣ Network Considerations

Each request involves:

- 1 network call to Redis
- ~1KB request overhead

At 1M RPS:

Network throughput ≈ 1GB/sec

Must deploy rate limiter close to gateway to reduce latency.

---

## 6️⃣ Scaling Strategy

We shard by:

- UserID hash

Redis Cluster:

hash(UserID) % N nodes

Ensures:
- Even key distribution
- Horizontal scalability

---

## 7️⃣ Storage Choice

We choose:

In-memory store (Redis)

Why?
- Ultra low latency
- Atomic increment
- Built-in TTL
- Horizontal sharding support

Database is too slow for per-request counter updates.

---

## Conclusion

- Memory requirement: < 1GB for 10M users
- Need 10–15 Redis nodes for 1M RPS
- Shard by UserID
- Use atomic INCR + EXPIRE
