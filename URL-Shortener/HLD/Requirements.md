# URL Shortener - Requirements

## 🎯 Problem Statement

Design a scalable URL shortening service similar to Bitly.

---

## 📌 Functional Requirements

- Generate short URL from long URL
- Redirect short URL to original URL
- Support custom alias (optional)
- Support expiration time (optional)
- Track number of clicks

---

## 📌 Non-Functional Requirements

- High availability
- Low latency (<100ms redirect)
- Horizontally scalable
- Highly reliable
- Eventual consistency acceptable for analytics

---

## 📊 Traffic Assumptions

- 10 Million new URLs per day
- 100 Million redirects per day
- 10-year retention

---

## 📌 Out of Scope

- Authentication
- Advanced analytics dashboard
- QR code generation
- Monetization
- Spam detection

---

## 🌐 Redirect Strategy

Use HTTP 302 (Temporary Redirect) to ensure:
- All traffic flows through our system
- Analytics tracking is accurate
- URL can be modified in future
