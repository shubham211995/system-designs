# URL Shortener - High Level Architecture

## 🏗 System Components

### 1. Client
- Browser / Mobile App
- Sends create and redirect requests

---

### 2. Load Balancer
- Distributes traffic across application servers
- Ensures high availability
- Health checks for instances

---

### 3. Application Layer
- Stateless servers
- Handles:
  - URL creation
  - Redirect requests
  - Cache lookup
  - DB lookup (if cache miss)
  - Publishing analytics event

Deployed as:
12–15 instances for peak 5,000 QPS

---

### 4. Redis Cache (Distributed)

Purpose:
- Serve hot URLs quickly
- Reduce DB load

Strategy:
- Cache shortCode → longURL
- TTL optional
- Clustered setup with replication
- 80% cache hit assumed

---

### 5. Database

Type:
- SQL database

Setup:
- 1 Primary (writes)
- Multiple read replicas

Primary Key:
- shortCode

Storage:
~5–6 TB over 10 years

No sharding initially.

---

### 6. Analytics Processing (Async)

Flow:
- Redirect request processed
- Click event published to message queue
- Background worker updates click count

Ensures:
- Redirect path remains low latency
- No synchronous DB update during redirect

---

## 🔁 Request Flow

### 🔹 URL Creation

1. Client sends POST /create
2. App server generates ID
3. ID encoded to Base62 shortCode
4. Store in DB
5. Return short URL

---

### 🔹 Redirect Flow

1. Client requests GET /{shortCode}
2. App checks Redis
   - If hit → return 302 redirect
   - If miss → fetch from DB → populate cache → return 302
3. Publish click event asynchronously
