# 🔗 URL Shortener — System Design

> Design TinyURL or bit.ly — a classic and extremely common system design question.

---

## 📋 Requirements

### Functional Requirements
- Given a long URL, generate a short URL (e.g., `tiny.io/abc123`)
- When short URL is visited, redirect to original long URL
- Short URLs should not expire (or optionally, have a configurable TTL)
- Custom short URLs (optional: user can choose alias)
- Analytics: click count, geographic data (optional)

### Non-Functional Requirements
- **High Availability**: 99.99% uptime (users must always be able to follow links)
- **Low Latency**: Redirect within 50ms
- **Scalability**: Handle 100 million URLs, 1 billion redirects/day
- **Durability**: Short URLs must not be lost

---

## 📊 Capacity Estimation

```
Write QPS: 100M URLs / (365 * 86400) = ~3 writes/sec
Read QPS:  1B redirects / (365 * 86400) = ~32,000 reads/sec
Read:Write ratio = ~10,000:1 (read-heavy!)

Storage (10 years):
- 3 writes/sec × 86400 × 365 × 10 = ~1 Billion URLs
- Each URL record ≈ 500 bytes → 500 GB total storage
```

---

## 🏗️ High-Level Architecture

```
                    ┌─────────────────┐
User → tiny.io/abc  │   Load Balancer │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
         Web Server 1  Web Server 2  Web Server 3
              │
        ┌─────┴──────┐
        ▼             ▼
    Cache (Redis)   DB (PostgreSQL)
    [Hot URLs]      [All URLs]
```

---

## 🔑 Core Design Decisions

### 1. URL Shortening Algorithm

#### Option A: Hash Function (MD5/SHA256)
```python
import hashlib

def shorten_url(long_url):
    hash = hashlib.md5(long_url.encode()).hexdigest()[:7]
    return hash  # 7 chars from hash

# Problem: Collisions possible; same URL → same hash; no uniqueness control
```

#### Option B: Base62 Encoding with Auto-increment ID (Recommended)

```python
import string

BASE62 = string.digits + string.ascii_lowercase + string.ascii_uppercase
# "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

def encode_base62(num):
    result = []
    while num > 0:
        result.append(BASE62[num % 62])
        num //= 62
    return ''.join(reversed(result)).zfill(7)

# ID=1       → "0000001"
# ID=1000000 → "4c92"
# 7 chars × 62 = 3.5 Trillion unique short URLs
```

**Why Base62?**
- URL-safe characters (no special chars)
- 7 characters = 62^7 ≈ 3.5 trillion possible URLs
- Auto-increment ID guarantees uniqueness, no collision check needed

#### Option C: Counter Service (Distributed)
For distributed systems, use a **token server** or **Redis INCR** to generate unique IDs:

```python
# Redis atomic counter
def get_next_id(redis_client):
    return redis_client.incr("url_counter")

# Multiple app servers → single Redis counter → no collisions
```

---

### 2. Database Design

```sql
CREATE TABLE urls (
    id          BIGSERIAL PRIMARY KEY,
    short_code  VARCHAR(10) UNIQUE NOT NULL,
    long_url    TEXT NOT NULL,
    user_id     INTEGER,           -- NULL for anonymous
    created_at  TIMESTAMP DEFAULT NOW(),
    expires_at  TIMESTAMP,         -- NULL for no expiry
    click_count BIGINT DEFAULT 0
);

CREATE INDEX idx_short_code ON urls(short_code);  -- Critical for O(log n) lookups
```

---

### 3. Caching Strategy

Read:Write = 10,000:1 → Cache aggressively.

```
Read (Redirect):
1. Check Redis cache for short_code → long_url
2. Cache Hit → Return 301/302 redirect
3. Cache Miss → Query DB → Cache result → Return redirect

Cache: LRU eviction, 20% most popular URLs
Rule: 80% of redirects go to 20% of URLs (Pareto principle)
TTL: 24 hours for non-expiring URLs

Write (Create URL):
1. Get next ID from counter
2. Encode to Base62 → short_code
3. Write to DB
4. Return short URL (don't write to cache on creation)
```

---

### 4. Redirect: 301 vs 302

| Code | Type | Browser Caches? | Analytics Count? | Use |
|------|------|----------------|-----------------|-----|
| **301** | Permanent Redirect | Yes | First visit only | Better performance, less server load |
| **302** | Temporary Redirect | No | Every visit | Better analytics tracking |

> **For URL shorteners that need analytics:** Use **302** (temporary) so every click comes through the server.
> **For performance-first:** Use **301** so browser caches and never hits server again.

---

### 5. Analytics (Optional Feature)

```python
# Async analytics — don't block the redirect
import asyncio

def redirect(short_code):
    long_url = get_url(short_code)
    
    # Non-blocking: publish click event to Kafka
    kafka_producer.send("url_clicks", {
        "short_code": short_code,
        "timestamp": datetime.now(),
        "user_agent": request.headers.get("User-Agent"),
        "ip": request.remote_addr
    })
    
    return redirect(long_url, code=302)
```

Analytics consumer processes events async to update click counts, geo data, etc.

---

## 🔑 Complete Flow

### Creating a Short URL
```
User POSTs long_url
    → App Server gets next_id from Redis INCR
    → Encode to Base62 → short_code
    → Write to PostgreSQL (id, short_code, long_url)
    → Return: "tiny.io/{short_code}"
```

### Redirecting
```
User GETs tiny.io/abc123
    → App Server checks Redis cache for "abc123"
    → Cache Hit: Redirect 302 to long_url
    → Cache Miss: Query PostgreSQL for "abc123"
        → Write to Redis cache (LRU, 24h TTL)
        → Redirect 302 to long_url
    → Send analytics event to Kafka (async)
```

---

## 🔧 Advanced Topics

### Custom Aliases
Allow users to choose their own short code (e.g., `tiny.io/mylink`):
- Check if `mylink` already exists in DB
- If not, insert with user-specified `short_code`
- Conflict → Return error, suggest alternatives

### Expiring URLs
- Store `expires_at` in DB
- On read: check `expires_at > NOW()` → if expired, delete + return 404
- Background job: periodically clean up expired URLs

### Rate Limiting
Prevent abuse (too many URL creations from one IP):
- Use Redis: `INCR url_creates:{ip}` with 1-minute TTL
- If count > 100/minute → return 429 Too Many Requests

---

## ❓ Interview Questions

**Q: How would you handle collisions in the hash approach?**
> Check if the hash already exists in the DB (with a different URL). If it does, append a counter (or use a different hash) and retry. This is why the auto-increment + Base62 approach is preferred — it eliminates collisions by construction.

**Q: Why use Base62 instead of Base64?**
> Base64 includes `+` and `/` which are special characters in URLs. Base62 (digits + lowercase + uppercase) is URL-safe.

**Q: How do you make this system highly available?**
> Multiple app servers behind a load balancer. Redis for caching and counter (with Redis Sentinel or Cluster for HA). PostgreSQL with read replicas. Geographic distribution with DNS routing for global users.

---

## ✅ Key Takeaways

- Auto-increment ID + Base62 encoding = clean, scalable approach
- Cache aggressively (read:write is 10,000:1)
- Use 302 for analytics, 301 for performance
- Distribute counter service to avoid bottleneck
- Kafka for async analytics events
