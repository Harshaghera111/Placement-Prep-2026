# Technical Questions

> Combined: Common Interview Questions + Project-specific Questions.

---

## Common Interview Questions

# ðŸ§ª Common Interview Questions

> A mix of technical, behavioral, and conceptual questions that commonly appear in SDE and AI Engineering interviews.

---

## ðŸ’» Technical â€” DSA & CS Fundamentals

### Arrays & Strings
- [ ] What is the time complexity of sorting algorithms? Which is best for nearly sorted data?
- [ ] Explain the sliding window pattern with an example.
- [ ] How would you find the longest substring without repeating characters?
- [ ] What is Kadane's algorithm? When is it used?

### Data Structures
- [ ] When would you use a stack vs a queue?
- [ ] Explain how a HashMap works internally. What happens when two keys hash to the same bucket?
- [ ] What is the time complexity of HashMap operations? What causes worst-case O(n)?
- [ ] What is a deque? How is it different from a queue?

### Trees & Graphs
- [ ] What's the difference between BFS and DFS? When do you use each?
- [ ] How do you detect a cycle in a directed graph?
- [ ] What is topological sort? Give a real-world example.
- [ ] What is the difference between a binary tree and a BST?
- [ ] How does Dijkstra's algorithm work? What's its time complexity?

### Dynamic Programming
- [ ] What is dynamic programming? How is memoization different from tabulation?
- [ ] What is the 0/1 knapsack problem?
- [ ] How do you identify if a problem can be solved with DP?

---

## ðŸ—„ï¸ DBMS Questions

- [ ] What is normalization? Explain 1NF, 2NF, 3NF.
- [ ] What is the difference between clustered and non-clustered indexes?
- [ ] Explain the ACID properties with real-world examples.
- [ ] What are the different types of SQL joins? When do you use each?
- [ ] What is the difference between DELETE, TRUNCATE, and DROP?
- [ ] What is a transaction isolation level? Name the 4 levels.
- [ ] Write a SQL query to find the second highest salary.
  ```sql
  SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);
  -- OR
  SELECT salary FROM employees ORDER BY salary DESC LIMIT 1 OFFSET 1;
  ```
- [ ] Write a query to find employees earning more than their manager.
  ```sql
  SELECT e.name FROM employees e
  JOIN employees m ON e.manager_id = m.id
  WHERE e.salary > m.salary;
  ```

---

## ðŸ–¥ï¸ OOP Questions

- [ ] What are the 4 pillars of OOP? Explain with examples.
- [ ] What is the difference between method overloading and overriding?
- [ ] What is an abstract class? How is it different from an interface?
- [ ] What is the SOLID principle? Explain each letter briefly.
- [ ] What is the Singleton pattern? Write a thread-safe Singleton in Python.
- [ ] What is composition vs inheritance? When do you prefer each?
- [ ] What is duck typing in Python?

---

## ðŸ’» OS Questions

- [ ] What is the difference between a process and a thread?
- [ ] What are the 4 conditions for deadlock?
- [ ] Explain the difference between mutex and semaphore.
- [ ] What is a context switch? What is its cost?
- [ ] What is virtual memory? What is a page fault?
- [ ] Compare paging vs segmentation.
- [ ] What is the difference between preemptive and non-preemptive scheduling?

---

## ðŸŒ Computer Networks Questions

- [ ] Explain the 7 layers of the OSI model.
- [ ] What is the difference between TCP and UDP?
- [ ] Describe the TCP 3-way handshake.
- [ ] What is DNS and how does DNS resolution work?
- [ ] What is the difference between HTTP and HTTPS?
- [ ] What happens when you type google.com in a browser? (Very commonly asked!)
- [ ] What is the difference between a hub, switch, and router?
- [ ] What are HTTP status codes? Give examples of 2xx, 4xx, and 5xx.

---

## ðŸ¤– AI Engineering Questions

- [ ] What is the difference between AI, ML, and Deep Learning?
- [ ] What is an LLM? How does it differ from traditional ML models?
- [ ] What is a hallucination in LLMs? How do you mitigate it?
- [ ] What is RAG? How does it work?
- [ ] What is an embedding? What is cosine similarity?
- [ ] What is a vector database? Name 3 examples.
- [ ] What is the difference between fine-tuning and RAG? When do you use each?
- [ ] What is an AI agent? What is the ReAct pattern?
- [ ] What is prompt engineering? What is chain-of-thought prompting?
- [ ] What is a token? What is a context window?

---

## ðŸ—ï¸ System Design Questions (Intro Level)

- [ ] What is horizontal vs vertical scaling?
- [ ] What is a load balancer and why is it used?
- [ ] What is caching? What is a cache eviction policy?
- [ ] What is the difference between SQL and NoSQL? When do you use each?
- [ ] How would you design a URL shortener?
- [ ] What is a CDN (Content Delivery Network)?
- [ ] What is sharding in databases?

---

## ðŸ”§ Project & Technical Depth Questions

### Full Stack
- [ ] What is the difference between REST and GraphQL?
- [ ] What is CORS and why does it exist?
- [ ] What is JWT? How does authentication with JWT work?
- [ ] What is the difference between session-based and token-based auth?
- [ ] What is middleware in Express.js?
- [ ] What is server-side rendering (SSR) vs client-side rendering (CSR)?
- [ ] What is the difference between SQL JOIN and a subquery? When to use each?

### React/Frontend
- [ ] What is the Virtual DOM?
- [ ] What are React hooks? Explain useState and useEffect.
- [ ] What is the difference between controlled and uncontrolled components?
- [ ] What is prop drilling and how do you solve it?

---

## ðŸ“‹ Quick-Fire Round Answers

| Question | Short Answer |
|----------|-------------|
| Stack overflow? | When call stack exceeds memory limit (deep/infinite recursion) |
| Null vs Undefined (JS)? | Null = intentionally empty, Undefined = not yet assigned |
| Array vs Linked List? | Array: O(1) access, O(n) insert. LL: O(n) access, O(1) insert at head |
| Stack vs Heap memory? | Stack: local variables, fast, limited. Heap: dynamic, slower, larger |
| Git rebase vs merge? | Merge preserves history. Rebase rewrites history for clean log |
| == vs === (JS)? | == type coercion, === strict equality (no coercion) |
| Synchronous vs Async? | Sync: blocks until done. Async: continues, callback/promise/await on completion |
| HTTP GET vs POST? | GET: retrieve data (URL params). POST: send data (body, not in URL) |
| Primary vs Foreign Key? | PK uniquely identifies row. FK references another table's PK |
| Pass by value vs reference? | Primitives: by value. Objects/arrays: by reference |

---

## âœ… Interview Day Checklist

- [ ] Good sleep the night before
- [ ] 15 minutes early to the location/link
- [ ] Resume printed (in-person) or open in browser tab
- [ ] Notepad and pen ready for rough work
- [ ] Stable internet + quiet room (virtual)
- [ ] Camera on, good lighting (virtual)
- [ ] Think aloud â€” state your approach before coding
- [ ] Ask clarifying questions before diving in
- [ ] Test your code with examples before declaring done


---

## Project Questions

# ðŸ“ Project Interview Questions

> Interviewers will probe your projects deeply. Be ready to explain every architectural decision, technical choice, and trade-off.

---

## ðŸ”‘ How to Present a Project

Follow this structure for every project explanation:

1. **Hook (10 sec)**: What problem does it solve? For whom?
2. **Architecture (30 sec)**: Tech stack + key design decisions
3. **Your role (15 sec)**: What did YOU specifically build?
4. **Challenge (30 sec)**: One technical challenge and how you solved it
5. **Impact/Learning (15 sec)**: What was the outcome? What did you learn?

---

## ðŸŒ¾ GramSathi â€” Project Interview Q&A

### Q: Walk me through GramSathi.
> "GramSathi is a rural tech platform I built to help farmers access government schemes, get farming advice, and digitize their records. The problem was that most government schemes are inaccessible to small farmers due to language barriers and complex portals.

I built it as a full-stack application â€” React.js frontend, Node.js backend, MongoDB for the flexible farmer data model, and JWT authentication with role-based access control. The AI layer uses a RAG pipeline: farming documents and scheme eligibility rules are embedded and stored in ChromaDB; when a farmer asks a question, we retrieve relevant context and pass it to GPT-4o for a grounded answer.

The biggest challenge was building the offline-first experience using Service Workers. I used IndexedDB for local storage and implemented a sync queue for when connectivity is restored."

### Q: Why did you choose MongoDB over a relational database?
> "Farmer profiles vary significantly â€” some have land ownership documents, others don't; some grow multiple crops, others one. A rigid relational schema would require many nullable columns or complex join tables. MongoDB's flexible document model was a better fit. That said, if we were scaling to millions of farmers and needed complex reporting, I'd consider PostgreSQL with JSONB columns as a hybrid approach."

### Q: How does the scheme eligibility engine work?
> "Each scheme has an eligibility JSON schema stored in MongoDB â€” fields like max income, max land area, caste category, state restrictions. When a farmer requests eligible schemes, the backend runs a filter against their profile data. It's essentially a rule-engine pattern â€” iterate schemes, evaluate conditions, return matches. For a production system, I'd move this to a proper rule engine like Drools or a decision table service."

### Q: How did you handle authentication and security?
> "OTP-based phone authentication using a 6-digit OTP stored in Redis with a 5-minute TTL. After verification, I issue a short-lived JWT (15 minutes) and a secure, httpOnly cookie refresh token (7 days). For sensitive data â€” Aadhaar numbers, bank accounts â€” I hash before storing using bcrypt. All API communication is HTTPS."

### Q: What would you do differently?
> "I'd add proper observability from the start â€” structured logging, error tracking (Sentry), and metrics. I also underestimated the complexity of multilingual support. I'd build the i18n architecture before any UI, not retrofit it later."

---

## ðŸ”§ GarageSathi â€” Project Interview Q&A

### Q: How did you implement the nearby garages feature?
> "I store garage coordinates as `latitude` and `longitude` in PostgreSQL. For proximity search, I use the Haversine formula in SQL to calculate great-circle distance between the user's GPS coordinates and garage locations, filtering by a configurable radius (default 10km). For production scale with millions of garages, I'd use PostGIS â€” PostgreSQL's spatial extension â€” which adds native geospatial indexing (GiST on geography type) for O(log n) range queries."

### Q: How does the real-time mechanic tracking work?
> "I use Socket.IO with room-based architecture. When a booking is confirmed, the customer joins a room named `booking:{id}`. The mechanic's mobile app emits location updates every 5 seconds to the same room. Socket.IO handles connection management; if the mechanic disconnects, we fall back to periodic REST polling. I'd improve this with WebSocket over HTTP/2 and binary encoding for lower latency."

### Q: How did you ensure payment reliability?
> "Razorpay issues an order ID before payment. After the user completes payment, the client sends `payment_id`, `order_id`, and `signature` to our server. We verify the HMAC-SHA256 signature using our Razorpay secret key â€” if it matches, the payment is authentic. We also listen to Razorpay webhooks for payment status changes as a second source of truth. This prevents false positives from network failures."

### Q: What is your database schema for bookings?
> "A bookings table with foreign keys to users, garages, and vehicles. Status transitions are: pending â†’ confirmed â†’ in_service â†’ completed â†’ reviewed. Payment has a separate status field to decouple service delivery from payment. I use a simple state machine pattern â€” each transition validates that the current status allows the requested change."

---

## ðŸš— TyreWebsite â€” Project Interview Q&A

### Q: How does the vehicle-to-tyre fitment work?
> "I maintain a `vehicle_fitments` table that maps make/model/variant/year ranges to front and rear tyre sizes. Users select their vehicle from cascading dropdowns (Make â†’ Model â†’ Variant â†’ Year). Once selected, we query the fitment table and get the tyre size spec, then filter the tyres table accordingly. I seeded this with ~500 popular Indian vehicles manually and plan to integrate with CarInfo API for comprehensive coverage."

### Q: How did you handle SEO for an e-commerce site?
> "I used Next.js with SSR (`getServerSideProps`) for product listing pages so Google can crawl fully-rendered HTML. Product pages have dynamic meta tags (title, description, OG tags) based on product data. I generated a sitemap.xml for all product URLs. Popular search queries (top 100 tyre sizes) use `getStaticProps` + ISR (Incremental Static Regeneration) with a 1-hour revalidation â€” so they load instantly from cache."

### Q: How would you handle inventory management at scale?
> "Currently I use a simple integer `stock` counter, which can have race conditions. At scale, I'd use PostgreSQL's `SELECT FOR UPDATE SKIP LOCKED` for reservation locking during checkout, or implement optimistic concurrency control with a version number on the inventory record. For very high volume (flash sales), I'd use Redis DECR as a fast atomic counter for reservation, then sync to DB asynchronously."

---

## ðŸ”‘ Common Follow-Up Questions (All Projects)

**Q: How would you scale this to 10x users?**
> Talk about: horizontal scaling, database read replicas, caching layer (Redis), CDN for static assets, message queues for async operations, load balancer.

**Q: How did you test this?**
> Mention: Unit tests for core business logic, integration tests for API endpoints, manual testing for UI. If you used a testing framework (Jest, pytest, Mocha), mention it.

**Q: How did you monitor it in production?**
> Talk about: logging (Winston, console.log), error tracking (Sentry), monitoring (UptimeRobot for uptime, Vercel analytics for frontend).

**Q: What was the hardest bug you faced?**
> Prepare a genuine story â€” debugging stories show problem-solving ability.

---

## âœ… Project Prep Checklist

- [ ] Can I explain all 3 projects in 90 seconds each?
- [ ] Can I justify every major technology choice?
- [ ] Can I describe the database schema for each project?
- [ ] Can I explain one major technical challenge per project?
- [ ] Can I describe what I would do differently?
- [ ] Can I discuss how I would scale each project?

