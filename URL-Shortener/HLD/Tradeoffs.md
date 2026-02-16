# URL Shortener - Tradeoffs & Design Decisions

## 1. Database Choice (SQL vs NoSQL)

### Decision:
We chose SQL (e.g., PostgreSQL / MySQL).

### Why?
- Structured schema
- Strong consistency
- Simple query pattern (Primary Key lookup on shortCode)
- Avoids unnecessary NoSQL complexity at this scale

### Tradeoff:
- Horizontal scaling is harder than NoSQL
- Future sharding needed at very large scale

---

## 2. ID Generation Strategy

### Decision:
DB Auto-Increment + Base62 Encoding

### Why?
- No collision risk
- Simple implementation
- Monotonic IDs help indexing

### Tradeoff:
- DB becomes ID bottleneck
- Harder to scale globally without sharding

---

## 3. Caching Strategy

### Decision:
Distributed Cache (Redis)

### What We Cache:
- shortCode → longURL
- Negative lookups (short TTL)

### Tradeoffs:
+ Reduces DB load
+ Improves latency
- Cache consistency challenges
- Cache pollution risk

Mitigation:
- Short TTL for negative cache
- Bloom Filter for invalid shortCodes

---

## 4. Consistency Model

### Decision:
Eventual Consistency for analytics
Strong consistency for redirection

### Why?
- Redirection must always work correctly
- Click counts can tolerate delay

Tradeoff:
- Click analytics may lag slightly

---

## 5. Sharding Strategy

### Decision:
Do NOT shard initially.

Shard when:
- DB CPU > 70%
- Storage near capacity
- Write throughput exceeds single node limit

Future Sharding Key:
- shortCode range-based sharding

Tradeoff:
+ Avoid early complexity
- Migration later requires careful planning

---

## 6. Handling Expired Links

Options:
- Return 404 (hide existence)
- Return 410 Gone (more semantic)

Decision:
Return 404 to avoid information leakage.

Tradeoff:
Less semantic clarity, more security.

---

## 7. Protection Against Abuse

Problems:
- Brute-force shortCode scanning
- Cache pollution
- DDoS

Solutions:
- Rate limiting per IP
- Bloom filter for valid shortCodes
- WAF / CDN layer
- Negative caching

---

## 8. Hot Key Problem

Problem:
Some shortCodes may become viral.

Impact:
- Cache node overload
- Uneven traffic distribution

Mitigation:
- Redis cluster
- Replication
- CDN caching
- Load balancer distribution

---

## 9. Cache Stampede

Problem:
If cache entry expires under high traffic, many requests hit DB simultaneously.

Mitigation:
- Staggered TTL
- Background refresh
- Request coalescing

---

## 10. Failure Scenarios

### DB Failure:
- Use read replicas
- Automatic failover

### Cache Failure:
- System still works (just slower)

### Analytics Queue Failure:
- Use durable message queue (Kafka / SQS)

---

## 11. CAP Theorem

System prioritizes:
- Consistency (for redirection)
- Availability (highly important)

Partition tolerance assumed (distributed system).

We aim for:
Strong consistency for URL lookup.
Eventual consistency for analytics.

---

## Conclusion

This system is optimized for:
- High read traffic
- Low write traffic
- Fast redirection
- Horizontal scalability when required
- Controlled operational complexity
