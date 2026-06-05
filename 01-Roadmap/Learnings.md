# 💡 Learnings — Key Insights & Aha Moments

> This file captures the most important things you've learned — the insights that changed how you think, the patterns that unlocked clarity, and the mistakes that taught you the most.

---

## 🧠 How to Use This File

Every time you have an **"Aha! moment"**, write it here. This is different from topic notes — it's your **mental model log**.

Format each learning like this:

```
### [Date] — [Topic]
**What I learned:**
**Why it matters:**
**How I'll remember it:**
```

---

## June 2026

### Jun 5, 2026 — Repository Setup
**What I learned:** Organizing knowledge into a structured repository forces clarity. When you have to write something down, you realize how much you don't fully understand yet.

**Why it matters:** Teaching and documenting are the best ways to learn. This repo is both a reference and a learning tool.

**How I'll remember it:** "Write to learn, not just to remember."

---

## 🔑 Core DSA Insights

### Pattern Recognition > Memorization
Many LeetCode problems fall into patterns:
- **Two Pointers** → Whenever you need O(1) space for sorted/linked structures
- **Sliding Window** → Subarray/substring problems with a constraint
- **Monotonic Stack** → Next Greater Element type problems
- **BFS** → Shortest path in unweighted graphs, level-order traversals
- **DFS + Backtracking** → Permutations, combinations, subsets
- **Binary Search on Answer** → When the answer has a monotonic property

### Kadane's Algorithm Mental Model
> "At each position, decide: should I extend the current subarray or start fresh?"
> `max_ending_here = max(num, max_ending_here + num)`

### Two Pointers Mental Model
> "Use two pointers when the array is sorted (or can be sorted) and you need to find a pair, triplet, or subarray that satisfies a condition."

---

## 🔑 Core CS Insights

### ACID in One Line
- **Atomicity** = All or nothing
- **Consistency** = Valid state → Valid state
- **Isolation** = Transactions don't see each other's partial work
- **Durability** = Committed = permanent, even after crash

### OOP Pillars — Real-World Analogies
- **Encapsulation** = A TV remote. You press buttons (public interface) without knowing the circuit (private implementation).
- **Inheritance** = A Dog inherits from Animal — it IS an Animal but also has dog-specific behavior.
- **Polymorphism** = A shape.area() call works for Circle, Rectangle, Triangle — same call, different behavior.
- **Abstraction** = Driving a car — you use the steering wheel, not the engine internals.

---

## 🔑 AI Engineering Insights

### RAG vs Fine-Tuning
- **Use RAG** when your data changes frequently or is domain-specific (documents, wikis)
- **Use Fine-Tuning** when you need the model to behave differently (new style, format, or domain knowledge baked in)
- **RAG is cheaper and more flexible** — fine-tuning requires GPU compute and risks catastrophic forgetting

### Why Embeddings Work
> Text that means the same thing ends up close in vector space.
> Similarity = cosine similarity = `(A · B) / (|A| |B|)`
> This is why "king - man + woman ≈ queen" works.

---

## 🔑 Interview Insights

### STAR Method — The Key Insight
> Interviewers don't want to hear what happened. They want to hear **what YOU did** and **what changed because of it**. Always make yourself the agent of change.

### Why "Tell Me About Yourself" Is Your Best Opportunity
> This is the only question where you fully control the narrative. Use it to direct the interview toward your strongest talking points.

---

## 📌 Mistakes I Made (And What I Learned)

| Mistake | What I Should Have Done | Lesson |
|---------|------------------------|--------|
| — | — | — |

> Add mistakes here as they happen. They're your best teachers.

---

## 📚 Books / Articles Worth Remembering

| Resource | Key Insight |
|----------|-------------|
| — | — |

> Add impactful reads here.
