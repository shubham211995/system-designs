# Rate Limiter - Requirements

## 1️⃣ Functional Requirements

1. Limit requests based on:
   - IP address
   - User ID
   - API key

2. Support multiple algorithms:
   - Fixed Window
   - Sliding Window
   - Token Bucket
   - Leaky Bucket

3. Configurable limits:
   - e.g., 100 requests per minute
   - e.g., 1000 requests per hour

4. Return proper response when limit exceeded:
   - HTTP 429 Too Many Requests

5. Should allow different limits per endpoint

---

## 2️⃣ Non-Functional Requirements

- Low latency (<5ms decision time)
- Horizontally scalable
- Highly available
- Strong consistency for counters
- Handle millions of requests per second

---

## 3️⃣ Extended Requirements (Optional)

- Dynamic configuration changes
- Per-tenant configuration
- Global vs regional limits
- Analytics dashboard

---

## 4️⃣ Out of Scope (Initial Version)

- Billing system
- UI dashboard
- Advanced ML-based anomaly detection
