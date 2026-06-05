# 🚦 Rate Limiter — System Design

> A rate limiter controls how many requests a client can make in a time window. It's a fundamental building block for API protection.

---

## 📋 Requirements

### Functional Requirements
- Limit requests per client (by IP, user ID, or API key)
- Return `429 Too Many Requests` when limit exceeded
- Configurable: different limits for different endpoints/tiers
- Fast — should not add significant latency

### Non-Functional Requirements
- **Low latency**: Rate limiting decision in <5ms
- **Accurate**: Don't allow significant rate limit bypass
- **Distributed**: Works across multiple app servers
- **Fault tolerant**: If limiter fails, requests should still pass (fail-open)

---

## 🔑 Rate Limiting Algorithms

### 1. Token Bucket (Most Common)
- A bucket holds tokens (max capacity = limit)
- Tokens refill at a fixed rate (e.g., 10 tokens/second)
- Each request consumes 1 token; if empty → reject

```python
import time
import redis

class TokenBucket:
    def __init__(self, redis_client, capacity, refill_rate):
        self.redis = redis_client
        self.capacity = capacity        # Max tokens
        self.refill_rate = refill_rate  # Tokens per second
    
    def is_allowed(self, user_id):
        key = f"rate_limit:{user_id}"
        now = time.time()
        
        # Lua script for atomic token bucket in Redis
        lua_script = """
        local tokens_key = KEYS[1]
        local last_update_key = KEYS[2]
        local capacity = tonumber(ARGV[1])
        local refill_rate = tonumber(ARGV[2])
        local now = tonumber(ARGV[3])
        
        local last_update = tonumber(redis.call('get', last_update_key) or now)
        local tokens = tonumber(redis.call('get', tokens_key) or capacity)
        
        -- Add tokens since last request
        local new_tokens = tokens + (now - last_update) * refill_rate
        new_tokens = math.min(new_tokens, capacity)
        
        if new_tokens >= 1 then
            -- Allow request
            redis.call('set', tokens_key, new_tokens - 1)
            redis.call('set', last_update_key, now)
            return 1
        else
            return 0
        end
        """
        
        result = self.redis.eval(lua_script, 2, 
            f"{key}:tokens", f"{key}:last", 
            self.capacity, self.refill_rate, now)
        
        return result == 1
```

**Pros:** Allows bursting (up to capacity), smooth traffic
**Cons:** More complex to implement correctly

### 2. Fixed Window Counter
```python
def is_allowed_fixed_window(redis_client, user_id, limit, window_seconds=60):
    key = f"rate_limit:{user_id}:{int(time.time() // window_seconds)}"
    
    current = redis_client.incr(key)
    if current == 1:
        redis_client.expire(key, window_seconds)
    
    return current <= limit
```

**Pros:** Simple to implement
**Cons:** Boundary problem — user can make 2x limit requests at window boundary

### 3. Sliding Window Log
```python
def is_allowed_sliding_log(redis_client, user_id, limit, window_seconds=60):
    key = f"rate_limit_log:{user_id}"
    now = time.time()
    window_start = now - window_seconds
    
    pipe = redis_client.pipeline()
    # Remove old entries
    pipe.zremrangebyscore(key, '-inf', window_start)
    # Add current request
    pipe.zadd(key, {str(now): now})
    # Count requests in window
    pipe.zcard(key)
    # Set expiry
    pipe.expire(key, window_seconds)
    
    _, _, count, _ = pipe.execute()
    return count <= limit
```

**Pros:** Accurate, no boundary problem
**Cons:** High memory (stores timestamps of all requests)

### 4. Sliding Window Counter (Best Balance)
Combine fixed window efficiency with sliding window accuracy:

```
Requests in window = 
    (requests in previous window × overlap_ratio) + requests in current window

where overlap_ratio = time remaining in previous window / window_size
```

```python
def is_allowed_sliding_counter(redis_client, user_id, limit, window_size=60):
    now = int(time.time())
    curr_window = now // window_size
    prev_window = curr_window - 1
    
    curr_key = f"rl:{user_id}:{curr_window}"
    prev_key = f"rl:{user_id}:{prev_window}"
    
    pipe = redis_client.pipeline()
    pipe.get(curr_key)
    pipe.get(prev_key)
    pipe.incr(curr_key)
    pipe.expire(curr_key, window_size * 2)
    
    curr_count_before, prev_count, curr_count, _ = pipe.execute()
    curr_count_before = int(curr_count_before or 0)
    prev_count = int(prev_count or 0)
    
    # How far into current window are we?
    elapsed = now % window_size
    prev_weight = (window_size - elapsed) / window_size
    
    # Estimated total in sliding window
    estimated = prev_count * prev_weight + curr_count_before
    
    return estimated < limit
```

---

## 🏗️ Architecture: Distributed Rate Limiter

```
                    ┌─────────────────┐
Clients → Nginx LB  │   API Gateway   │
                    │  (Rate Limiter  │
                    │   Middleware)   │
                    └────────┬────────┘
                             │ Check/Update
                    ┌────────▼────────┐
                    │  Redis Cluster  │
                    │  (Shared state) │
                    └────────────────┘
```

### Rate Limiter Middleware (Express.js)

```javascript
const redis = require('redis');
const client = redis.createClient();

const rateLimiter = (limit, windowSeconds) => {
    return async (req, res, next) => {
        const key = `rate_limit:${req.ip}:${Math.floor(Date.now() / 1000 / windowSeconds)}`;
        
        try {
            const count = await client.incr(key);
            if (count === 1) {
                await client.expire(key, windowSeconds);
            }
            
            // Set headers so client knows limits
            res.setHeader('X-RateLimit-Limit', limit);
            res.setHeader('X-RateLimit-Remaining', Math.max(0, limit - count));
            
            if (count > limit) {
                return res.status(429).json({
                    error: 'Too Many Requests',
                    retryAfter: windowSeconds
                });
            }
            
            next();
        } catch (err) {
            // Fail open — don't block requests if Redis is down
            next();
        }
    };
};

// Apply to routes
app.use('/api/', rateLimiter(100, 60));  // 100 req/min
app.post('/api/auth/login', rateLimiter(5, 60));  // 5 login attempts/min
```

---

## 🔑 Rate Limiting Tiers

Different limits for different clients:

```javascript
const getLimitForUser = (user) => {
    if (user.plan === 'enterprise') return { limit: 10000, window: 60 };
    if (user.plan === 'pro') return { limit: 1000, window: 60 };
    if (user.plan === 'free') return { limit: 100, window: 60 };
    return { limit: 10, window: 60 };  // Anonymous
};
```

---

## 📊 Algorithm Comparison

| Algorithm | Memory | Accuracy | Burst Allowed | Complexity |
|-----------|--------|----------|---------------|------------|
| Token Bucket | O(1) | High | Yes | Medium |
| Fixed Window | O(1) | Low (boundary) | Yes | Low |
| Sliding Log | O(n) | High | No | Medium |
| Sliding Counter | O(1) | Medium-High | Partial | Medium |

> **Recommendation:** Use **Token Bucket** (allows bursting) or **Sliding Window Counter** (accurate + efficient) for most production systems.

---

## ❓ Interview Questions

**Q: Why use Redis for distributed rate limiting?**
> Rate limiting state must be shared across all app servers. Redis provides atomic operations (INCR), fast in-memory access (<1ms), and handles concurrent requests safely with atomic commands.

**Q: What is the boundary problem with fixed window?**
> If the window is 60 seconds and the limit is 100 requests, a user could make 100 requests at 00:59 and 100 more at 01:01 — effectively 200 requests in 2 seconds without violating the rule. Sliding window algorithms solve this.

**Q: What is fail-open vs fail-closed?**
> Fail-open: If the rate limiter breaks (Redis down), allow all requests. Fail-closed: Block all requests. For most APIs, fail-open is preferred — better to have some abuse than block all traffic during a Redis outage.

**Q: How would you rate limit by API key instead of IP?**
> Change the Redis key to use the API key: `rate_limit:apikey:${req.headers['x-api-key']}:${window}`. IP-based limiting is useful for unauthenticated endpoints; API key-based is better for authenticated APIs.

---

## ✅ Key Takeaways

- Token Bucket is the most flexible — allows bursting within limits
- Use Redis for distributed state (atomic INCR, fast)
- Always set rate limit headers (X-RateLimit-Limit, Remaining)
- Fail-open is safer than fail-closed in most cases
- Apply different limits to different endpoint categories
