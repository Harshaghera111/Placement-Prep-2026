# 🏗️ System Design — Basics

> System design interviews test your ability to architect scalable, reliable, and maintainable systems. Start here before tackling specific designs.

---

## 📖 What is System Design?

System design is the process of defining the architecture, components, interfaces, and data flow of a system to satisfy specified requirements.

In interviews, you're asked to design a system like "Design Twitter" or "Design a URL Shortener" — you need to think through scale, reliability, and tradeoffs.

---

## 🔑 Core Concepts

### 1. Scalability

**Vertical Scaling (Scale Up):** Add more power to one machine (more CPU, RAM).
- Pros: Simple, no code changes
- Cons: Limited by hardware, single point of failure, expensive

**Horizontal Scaling (Scale Out):** Add more machines.
- Pros: Near-infinite scale, fault tolerant
- Cons: Needs load balancer, distributed system complexity

**Stateless vs Stateful:**
- **Stateless**: Server doesn't store session — any server can handle any request (easier to scale)
- **Stateful**: Session stored on specific server — requests must route to same server

---

### 2. Load Balancer

A **load balancer** distributes incoming traffic across multiple servers.

```
Clients → [Load Balancer] → Server 1
                          → Server 2
                          → Server 3
```

**Algorithms:**
| Algorithm | Description | Use Case |
|-----------|-------------|---------|
| **Round Robin** | Distribute equally in rotation | Uniform servers |
| **Weighted Round Robin** | More to powerful servers | Heterogeneous servers |
| **Least Connections** | Send to server with fewest active connections | Varied request duration |
| **IP Hash** | Same client always goes to same server | Session stickiness |

**Types:**
- **Layer 4 (Transport)**: Routes based on IP/TCP — fast, less flexible
- **Layer 7 (Application)**: Routes based on URL, headers — smarter, more overhead

---

### 3. Caching

Caching stores frequently accessed data in fast storage (memory) to reduce database load and latency.

**Caching Hierarchy:**
```
Browser Cache → CDN → Server Cache (Redis/Memcached) → Database
    (ms)         (ms)        (ms)                         (10-100ms)
```

**Cache Eviction Policies:**
| Policy | Description |
|--------|-------------|
| **LRU** | Evict least recently used |
| **LFU** | Evict least frequently used |
| **FIFO** | Evict oldest item |
| **TTL** | Evict after expiry time |

**Caching Strategies:**

**Cache Aside (Lazy Loading):** App checks cache first; if miss, load from DB, then write to cache.
```
Read:  Cache Hit → Return data
       Cache Miss → DB Read → Write to Cache → Return data
Write: Write to DB → Invalidate Cache
```

**Write Through:** Write to cache AND DB simultaneously.
```
Write: Write to Cache + DB simultaneously
Read:  Always cache hit (but cold start issue)
```

**Write Behind (Write Back):** Write to cache first, async sync to DB.
```
Write: Write to Cache immediately
       Async batch write to DB (fast writes, risk of data loss)
```

---

### 4. Database Selection

**SQL (Relational):** PostgreSQL, MySQL
- Use when: Data is structured, ACID compliance needed, complex queries with JOINs
- Examples: Financial systems, e-commerce orders, user accounts

**NoSQL:** MongoDB, DynamoDB, Cassandra, Redis
- Use when: Flexible schema, massive scale, simple key-value or document access
- Types:
  - **Document**: MongoDB (JSON-like docs)
  - **Key-Value**: Redis, DynamoDB (fast lookups)
  - **Wide-Column**: Cassandra (IoT, time-series, high write)
  - **Graph**: Neo4j (social networks, recommendations)

**CAP Theorem:**
> A distributed system can guarantee at most **2 of 3**: **C**onsistency, **A**vailability, **P**artition Tolerance.
> In practice, network partitions happen — you choose CA, CP, or AP.

| Database | CAP Type |
|----------|---------|
| RDBMS (single node) | CA |
| MongoDB (replica set) | CP |
| Cassandra | AP |
| DynamoDB | AP (tunable) |

---

### 5. Database Replication & Sharding

**Replication:**
- **Master-Slave**: Master handles writes; slaves handle reads. If master fails, promote a slave.
- **Master-Master**: Multiple write nodes. Conflict resolution needed.

```
Writes → Master → Replicate → Slave 1 (reads)
                            → Slave 2 (reads)
```

**Sharding (Horizontal Partitioning):**
Split data across multiple DB instances (shards) based on a shard key.

```
UserID 1–10M  → Shard 1
UserID 10M–20M → Shard 2
UserID 20M–30M → Shard 3
```

**Sharding strategies:**
- **Range-based**: Easy to implement, hotspots possible
- **Hash-based**: Uniform distribution, hard to add shards
- **Directory-based**: Lookup table for shard location, flexible

---

### 6. Message Queues

Decouple producers from consumers for async processing.

```
Producer → [Message Queue] → Consumer 1
            (Kafka/RabbitMQ) → Consumer 2
```

**Use cases:**
- Email/notification sending
- Image processing (upload → queue → resize → store)
- Order processing (order placed → inventory update → shipping)
- Log aggregation

**Tools:**
- **RabbitMQ**: Traditional message broker, complex routing
- **Apache Kafka**: High-throughput event streaming, persistent
- **AWS SQS**: Managed, simple, good for AWS stack

---

### 7. CDN (Content Delivery Network)

Serve static assets (images, CSS, JS, videos) from geographically distributed edge servers closer to users.

```
User in India → India CDN Edge → CDN Origin → Your Server
                (cache hit)      (if miss)
```

**Examples:** Cloudflare, AWS CloudFront, Fastly

---

## 🔑 System Design Interview Framework

### How to Approach Any System Design Question

```
1. Clarify Requirements (5 min)
   - Functional: What features?
   - Non-functional: Scale? Latency? Availability? Consistency?

2. Estimate Scale (5 min)
   - DAU (Daily Active Users)
   - Read:Write ratio
   - Data volume (storage)
   - QPS (Queries per Second)

3. High-Level Design (15 min)
   - Draw major components (clients, LB, services, DB, cache)
   - Explain data flow

4. Deep Dive (15 min)
   - Pick 2-3 critical components
   - Discuss trade-offs, bottlenecks

5. Wrap Up (5 min)
   - Identify bottlenecks
   - Mention monitoring, alerting
   - Future improvements
```

---

## 📊 Back-of-Envelope Estimations

| Metric | Approximation |
|--------|--------------|
| 1 Million DAU reading 10 pages/day | ~100 reads/sec |
| 1 row in DB | ~1 KB |
| Image | ~100 KB |
| Video (1 hour, 720p) | ~1 GB |
| SSD read | ~1 ns |
| Network round trip (same DC) | ~1 ms |
| Database query | ~10 ms |

---

## ✅ Revision Checklist

- [ ] Can I explain vertical vs horizontal scaling?
- [ ] Can I explain how a load balancer works?
- [ ] Can I explain LRU vs LFU cache eviction?
- [ ] Can I explain the 3 caching strategies (cache-aside, write-through, write-behind)?
- [ ] Do I know when to use SQL vs NoSQL?
- [ ] Can I explain the CAP theorem?
- [ ] Do I understand database replication and sharding?
- [ ] Can I follow the system design interview framework?
