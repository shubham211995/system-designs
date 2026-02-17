# URL Shortener - API Contracts

## 1. Create Short URL

### POST /api/v1/urls

Request Body:
{
  "longUrl": "https://example.com/page",
  "customAlias": "optionalAlias",
  "expiryDate": "2026-12-31T23:59:59Z"
}

Response:
{
  "shortUrl": "https://short.ly/abc123",
  "shortCode": "abc123",
  "expiresAt": "2026-12-31T23:59:59Z"
}

Status Codes:
201 Created
400 Bad Request
409 Conflict (alias already exists)


## 2. Redirect

### GET /{shortCode}

Response:
302 Redirect → Location: original long URL

Error:
404 Not Found



## 3. Analytics (Optional Future)

### GET /api/v1/urls/{shortCode}/stats

Response:
{
  "totalClicks": 10234,
  "createdAt": "...",
  "expiresAt": "..."
}
