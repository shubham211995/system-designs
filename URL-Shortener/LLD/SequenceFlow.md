
### Paste this:

```markdown
# Sequence Flow

## URL Creation

Client → Controller
Controller → Service
Service → ID Generator
Service → Repository (DB Write)
Service → Cache Delete (if exists)
Controller → Client (Return Short URL)

---

## Redirect Flow

Client → Controller
Controller → Cache
If hit → Return 302

If miss:
Controller → Repository (DB Read)
Repository → Cache Put
Controller → AnalyticsService (Async)
Controller → Return 302
