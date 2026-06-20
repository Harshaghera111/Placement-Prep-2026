# Stack & Queue

> Concepts, patterns, and revision notes — not code.

---

## Stack

- **LIFO** — Last In, First Out.
- Operations: `push`, `pop`, `peek`, `isEmpty` — all O(1).
- Implemented with array or linked list.

### When to Use Stack
- Matching/balancing problems (parentheses, brackets).
- Undo operations.
- DFS traversal (iterative).
- Monotonic stack problems.
- Expression evaluation (infix → postfix, calculator).

### Monotonic Stack Pattern
- **Monotonic Increasing Stack:** maintains elements in increasing order.
  - Pop when current element is **smaller** than top.
  - Used for: Next Greater Element to the LEFT.
- **Monotonic Decreasing Stack:** maintains elements in decreasing order.
  - Pop when current element is **larger** than top.
  - Used for: Next Greater Element to the RIGHT.

**Key insight:** When you pop an element, you've found the "boundary" for that element.

### Important Stack Problems
- Valid Parentheses
- Next Greater Element
- Largest Rectangle in Histogram
- Daily Temperatures
- Min Stack (track minimum with auxiliary stack)

---

## Queue

- **FIFO** — First In, First Out.
- Operations: `enqueue`, `dequeue`, `front`, `isEmpty` — all O(1) with deque.
- Implemented with array (circular) or linked list.

### When to Use Queue
- BFS traversal.
- Level-order tree traversal.
- Sliding window maximum.
- Process scheduling simulations.

### Deque (Double-Ended Queue)
- Can insert/delete from both ends.
- Used in: Sliding Window Maximum — maintain decreasing deque of indices.

---

## Monotonic Deque Pattern (Sliding Window Maximum)
- Maintain deque of indices in decreasing order of values.
- Remove indices that are out of window from front.
- Remove smaller elements from back before adding new index.
- Front of deque always holds the maximum.

---

## Stack vs Queue Comparison

| Feature | Stack | Queue |
|---|---|---|
| Order | LIFO | FIFO |
| Use case | DFS, backtracking | BFS, level order |
| Peek | Top element | Front element |

---

## Common Mistakes

- Using `Stack` class in Java — prefer `Deque<Integer> stack = new ArrayDeque<>()`.
- Forgetting to check `isEmpty()` before `pop`/`peek`.
- Off-by-one in sliding window maximum when removing out-of-window indices.

---

## Key Observations

- Parentheses problems always use a stack — push open, pop on close.
- Histogram problems → monotonic stack → think "what's the boundary?"
- Any problem asking "next greater/smaller" → monotonic stack.
- BFS → always use a queue; DFS → stack (or recursion).

---

*Add notes as you solve stack/queue problems.*
