# 📁 Project Interview Questions

> Interviewers will probe your projects deeply. Be ready to explain every architectural decision, technical choice, and trade-off.

---

## 🔑 How to Present a Project

Follow this structure for every project explanation:

1. **Hook (10 sec)**: What problem does it solve? For whom?
2. **Architecture (30 sec)**: Tech stack + key design decisions
3. **Your role (15 sec)**: What did YOU specifically build?
4. **Challenge (30 sec)**: One technical challenge and how you solved it
5. **Impact/Learning (15 sec)**: What was the outcome? What did you learn?

---

## 🌾 GramSathi — Project Interview Q&A

### Q: Walk me through GramSathi.
> "GramSathi is a rural tech platform I built to help farmers access government schemes, get farming advice, and digitize their records. The problem was that most government schemes are inaccessible to small farmers due to language barriers and complex portals.

I built it as a full-stack application — React.js frontend, Node.js backend, MongoDB for the flexible farmer data model, and JWT authentication with role-based access control. The AI layer uses a RAG pipeline: farming documents and scheme eligibility rules are embedded and stored in ChromaDB; when a farmer asks a question, we retrieve relevant context and pass it to GPT-4o for a grounded answer.

The biggest challenge was building the offline-first experience using Service Workers. I used IndexedDB for local storage and implemented a sync queue for when connectivity is restored."

### Q: Why did you choose MongoDB over a relational database?
> "Farmer profiles vary significantly — some have land ownership documents, others don't; some grow multiple crops, others one. A rigid relational schema would require many nullable columns or complex join tables. MongoDB's flexible document model was a better fit. That said, if we were scaling to millions of farmers and needed complex reporting, I'd consider PostgreSQL with JSONB columns as a hybrid approach."

### Q: How does the scheme eligibility engine work?
> "Each scheme has an eligibility JSON schema stored in MongoDB — fields like max income, max land area, caste category, state restrictions. When a farmer requests eligible schemes, the backend runs a filter against their profile data. It's essentially a rule-engine pattern — iterate schemes, evaluate conditions, return matches. For a production system, I'd move this to a proper rule engine like Drools or a decision table service."

### Q: How did you handle authentication and security?
> "OTP-based phone authentication using a 6-digit OTP stored in Redis with a 5-minute TTL. After verification, I issue a short-lived JWT (15 minutes) and a secure, httpOnly cookie refresh token (7 days). For sensitive data — Aadhaar numbers, bank accounts — I hash before storing using bcrypt. All API communication is HTTPS."

### Q: What would you do differently?
> "I'd add proper observability from the start — structured logging, error tracking (Sentry), and metrics. I also underestimated the complexity of multilingual support. I'd build the i18n architecture before any UI, not retrofit it later."

---

## 🔧 GarageSathi — Project Interview Q&A

### Q: How did you implement the nearby garages feature?
> "I store garage coordinates as `latitude` and `longitude` in PostgreSQL. For proximity search, I use the Haversine formula in SQL to calculate great-circle distance between the user's GPS coordinates and garage locations, filtering by a configurable radius (default 10km). For production scale with millions of garages, I'd use PostGIS — PostgreSQL's spatial extension — which adds native geospatial indexing (GiST on geography type) for O(log n) range queries."

### Q: How does the real-time mechanic tracking work?
> "I use Socket.IO with room-based architecture. When a booking is confirmed, the customer joins a room named `booking:{id}`. The mechanic's mobile app emits location updates every 5 seconds to the same room. Socket.IO handles connection management; if the mechanic disconnects, we fall back to periodic REST polling. I'd improve this with WebSocket over HTTP/2 and binary encoding for lower latency."

### Q: How did you ensure payment reliability?
> "Razorpay issues an order ID before payment. After the user completes payment, the client sends `payment_id`, `order_id`, and `signature` to our server. We verify the HMAC-SHA256 signature using our Razorpay secret key — if it matches, the payment is authentic. We also listen to Razorpay webhooks for payment status changes as a second source of truth. This prevents false positives from network failures."

### Q: What is your database schema for bookings?
> "A bookings table with foreign keys to users, garages, and vehicles. Status transitions are: pending → confirmed → in_service → completed → reviewed. Payment has a separate status field to decouple service delivery from payment. I use a simple state machine pattern — each transition validates that the current status allows the requested change."

---

## 🚗 TyreWebsite — Project Interview Q&A

### Q: How does the vehicle-to-tyre fitment work?
> "I maintain a `vehicle_fitments` table that maps make/model/variant/year ranges to front and rear tyre sizes. Users select their vehicle from cascading dropdowns (Make → Model → Variant → Year). Once selected, we query the fitment table and get the tyre size spec, then filter the tyres table accordingly. I seeded this with ~500 popular Indian vehicles manually and plan to integrate with CarInfo API for comprehensive coverage."

### Q: How did you handle SEO for an e-commerce site?
> "I used Next.js with SSR (`getServerSideProps`) for product listing pages so Google can crawl fully-rendered HTML. Product pages have dynamic meta tags (title, description, OG tags) based on product data. I generated a sitemap.xml for all product URLs. Popular search queries (top 100 tyre sizes) use `getStaticProps` + ISR (Incremental Static Regeneration) with a 1-hour revalidation — so they load instantly from cache."

### Q: How would you handle inventory management at scale?
> "Currently I use a simple integer `stock` counter, which can have race conditions. At scale, I'd use PostgreSQL's `SELECT FOR UPDATE SKIP LOCKED` for reservation locking during checkout, or implement optimistic concurrency control with a version number on the inventory record. For very high volume (flash sales), I'd use Redis DECR as a fast atomic counter for reservation, then sync to DB asynchronously."

---

## 🔑 Common Follow-Up Questions (All Projects)

**Q: How would you scale this to 10x users?**
> Talk about: horizontal scaling, database read replicas, caching layer (Redis), CDN for static assets, message queues for async operations, load balancer.

**Q: How did you test this?**
> Mention: Unit tests for core business logic, integration tests for API endpoints, manual testing for UI. If you used a testing framework (Jest, pytest, Mocha), mention it.

**Q: How did you monitor it in production?**
> Talk about: logging (Winston, console.log), error tracking (Sentry), monitoring (UptimeRobot for uptime, Vercel analytics for frontend).

**Q: What was the hardest bug you faced?**
> Prepare a genuine story — debugging stories show problem-solving ability.

---

## ✅ Project Prep Checklist

- [ ] Can I explain all 3 projects in 90 seconds each?
- [ ] Can I justify every major technology choice?
- [ ] Can I describe the database schema for each project?
- [ ] Can I explain one major technical challenge per project?
- [ ] Can I describe what I would do differently?
- [ ] Can I discuss how I would scale each project?
