
# Class Design

## 1. UrlController

Methods:
- createShortUrl()
- redirect()

---

## 2. UrlService

Methods:
- generateShortCode()
- saveUrl()
- getLongUrl()
- validateAlias()

---

## 3. UrlRepository

Methods:
- save()
- findByShortCode()

---

## 4. CacheService

Methods:
- get(shortCode)
- put(shortCode, longUrl)
- delete(shortCode)

---

## 5. AnalyticsService

Methods:
- publishClickEvent()
