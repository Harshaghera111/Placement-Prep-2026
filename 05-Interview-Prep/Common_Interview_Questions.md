# 🧪 Common Interview Questions

> A mix of technical, behavioral, and conceptual questions that commonly appear in SDE and AI Engineering interviews.

---

## 💻 Technical — DSA & CS Fundamentals

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

## 🗄️ DBMS Questions

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

## 🖥️ OOP Questions

- [ ] What are the 4 pillars of OOP? Explain with examples.
- [ ] What is the difference between method overloading and overriding?
- [ ] What is an abstract class? How is it different from an interface?
- [ ] What is the SOLID principle? Explain each letter briefly.
- [ ] What is the Singleton pattern? Write a thread-safe Singleton in Python.
- [ ] What is composition vs inheritance? When do you prefer each?
- [ ] What is duck typing in Python?

---

## 💻 OS Questions

- [ ] What is the difference between a process and a thread?
- [ ] What are the 4 conditions for deadlock?
- [ ] Explain the difference between mutex and semaphore.
- [ ] What is a context switch? What is its cost?
- [ ] What is virtual memory? What is a page fault?
- [ ] Compare paging vs segmentation.
- [ ] What is the difference between preemptive and non-preemptive scheduling?

---

## 🌐 Computer Networks Questions

- [ ] Explain the 7 layers of the OSI model.
- [ ] What is the difference between TCP and UDP?
- [ ] Describe the TCP 3-way handshake.
- [ ] What is DNS and how does DNS resolution work?
- [ ] What is the difference between HTTP and HTTPS?
- [ ] What happens when you type google.com in a browser? (Very commonly asked!)
- [ ] What is the difference between a hub, switch, and router?
- [ ] What are HTTP status codes? Give examples of 2xx, 4xx, and 5xx.

---

## 🤖 AI Engineering Questions

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

## 🏗️ System Design Questions (Intro Level)

- [ ] What is horizontal vs vertical scaling?
- [ ] What is a load balancer and why is it used?
- [ ] What is caching? What is a cache eviction policy?
- [ ] What is the difference between SQL and NoSQL? When do you use each?
- [ ] How would you design a URL shortener?
- [ ] What is a CDN (Content Delivery Network)?
- [ ] What is sharding in databases?

---

## 🔧 Project & Technical Depth Questions

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

## 📋 Quick-Fire Round Answers

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

## ✅ Interview Day Checklist

- [ ] Good sleep the night before
- [ ] 15 minutes early to the location/link
- [ ] Resume printed (in-person) or open in browser tab
- [ ] Notepad and pen ready for rough work
- [ ] Stable internet + quiet room (virtual)
- [ ] Camera on, good lighting (virtual)
- [ ] Think aloud — state your approach before coding
- [ ] Ask clarifying questions before diving in
- [ ] Test your code with examples before declaring done
