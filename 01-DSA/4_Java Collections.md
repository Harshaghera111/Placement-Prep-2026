# ☕ Java Collections Framework — Placement & LeetCode Notes

> 🎯 Goal: Revise this in 20–30 minutes before any SDE interview. No textbook theory — only what gets asked.

---

## 📚 Table of Contents
1. Introduction
2. Collection Hierarchy
3. Every Collection (deep dive)
4. Comparison Tables
5. Time Complexity Cheat Sheet
6. Frequently Used Methods
7. Collections Utility Class
8. Comparator vs Comparable
9. Iterator vs ListIterator
10. Fail-Fast vs Fail-Safe
11. Which Collection Should I Use? (Decision Tree)
12. LeetCode Cheat Sheet
13. Interview Corner
14. Quick Code Snippets

---

## 1️⃣ Introduction

### What is Java Collections Framework (JCF)?
A unified architecture (interfaces + implementations + algorithms) to **store, retrieve, and manipulate groups of objects**. Lives in `java.util`.

### Why do we need it?
- Before JCF: arrays (fixed size), Vector/Hashtable (inconsistent APIs, slow).
- JCF gives: dynamic resizing, ready-made algorithms (`sort`, `search`), interoperability, reduced coding effort.

### `Collection` vs `Collections`
| | `Collection` | `Collections` |
|---|---|---|
| Type | Interface | Utility class (final, all static methods) |
| Package | `java.util` | `java.util` |
| Purpose | Root interface for List/Set/Queue | Provides algorithms (sort, reverse, binarySearch...) on collections |
| Example | `Collection<Integer> c = new ArrayList<>();` | `Collections.sort(list);` |

### `Collection` vs `Map`
| | `Collection` | `Map` |
|---|---|---|
| Stores | Single elements | Key-value pairs |
| Extends | `Iterable` | Does **not** extend Collection |
| Examples | List, Set, Queue | HashMap, TreeMap |

💡 **Interview Tip:** `Map` is NOT a sub-interface of `Collection` — a classic trick question.

---

## 2️⃣ Complete Collection Hierarchy

```
                     Iterable
                        │
                    Collection
        ┌───────────────┼────────────────┐
       List             Set             Queue
        │                │                │
  ┌─────┼─────┐    ┌─────┼──────┐   ┌─────┼──────┐
ArrayList LinkedList Vector  HashSet LinkedHashSet TreeSet  Queue  Deque
   │         (also Queue+List)         │             │      │      │
 Stack(extends Vector)            PriorityQueue   ArrayDeque  LinkedList(also Deque)


                        Map (separate hierarchy, extends nothing from Collection)
        ┌───────────────┼────────────────┬────────────┐
     HashMap      LinkedHashMap       TreeMap      Hashtable
                                    (implements
                                    SortedMap/
                                    NavigableMap)
```

📌 **Revision Notes:**
- `List`, `Set`, `Queue` → extend `Collection`.
- `Map` → standalone hierarchy.
- `Stack` extends `Vector` (legacy, avoid in new code — use `Deque`).
- `LinkedList` implements both `List` and `Deque`.
- `PriorityQueue` implements `Queue` (NOT `Deque`).

---

## 3️⃣ Every Collection — Deep Dive

### 📦 ArrayList
- **What:** Resizable array implementation of `List`.
- **Internal Working:** Backed by `Object[]`. Default capacity 10. On overflow → new array of `1.5x` size, old elements copied (`Arrays.copyOf`).
- **When to use:** Frequent random access (`get(i)`), infrequent insert/delete in middle.
- **When NOT to use:** Frequent insertions/deletions at start/middle (shifting cost O(n)).
- **Creation:** `List<Integer> list = new ArrayList<>();`
- **Important Methods:** `add()`, `get()`, `set()`, `remove()`, `size()`, `contains()`, `indexOf()`
- **Time Complexity:**

| Operation | Complexity |
|---|---|
| Access (get) | O(1) |
| Search | O(n) |
| Insert at end | O(1) amortized |
| Insert at middle/start | O(n) |
| Delete | O(n) |

- **Internal DS:** Dynamic array
- **Duplicates:** ✅ Allowed
- **Null:** ✅ Allowed (multiple)
- **Ordered:** ✅ Insertion order maintained
- **Sorted:** ❌ (use `Collections.sort()`)
- **Thread Safe:** ❌ (use `CopyOnWriteArrayList` or `Collections.synchronizedList`)

> 💡 **Interview Tip:** Resize factor is `1.5x` for ArrayList vs `2x` for Vector — frequently asked!

> ⚠️ **Common Mistake:** Using `ArrayList.remove(int)` vs `remove(Object)` — `list.remove(1)` removes index 1, but `list.remove(Integer.valueOf(1))` removes the value 1. Causes silent bugs with `Integer` lists.

🚀 **LeetCode Usage:** Sliding window, prefix sums, storing intermediate results, 2D grids (`List<List<Integer>>` for backtracking output).

**Companies:** Asked everywhere — Amazon, Google, Microsoft (internal working Qs).

```java
List<Integer> list = new ArrayList<>(Arrays.asList(3,1,2));
list.add(0, 99);          // insert at index
Collections.sort(list);   // sort
System.out.println(list); // [1, 2, 3, 99]
```

---

### 🔗 LinkedList
- **What:** Doubly linked list implementing `List` and `Deque`.
- **Internal Working:** Each node has `prev`, `next`, `data` pointers. No contiguous memory.
- **When to use:** Frequent insert/delete at head/tail, implementing Deque/Queue.
- **When NOT to use:** Random access needed (O(n) traversal).
- **Time Complexity:**

| Operation | Complexity |
|---|---|
| Access | O(n) |
| Insert/Delete at ends | O(1) |
| Insert/Delete in middle | O(n) (search) + O(1) (link) |
| Search | O(n) |

- **Duplicates:** ✅ | **Null:** ✅ | **Ordered:** ✅ | **Sorted:** ❌ | **Thread Safe:** ❌

> 💡 **Interview Tip:** `LinkedList` uses **more memory per element** (node overhead: 2 pointers + object header) vs ArrayList's raw array slot.

> ⚠️ **Common Mistake:** Using `LinkedList` thinking it's always faster for insertion — true only at known head/tail reference; if you must `get(i)` first, you lose the benefit.

🚀 **LeetCode Usage:** Implementing actual Linked List problems, LRU Cache (with HashMap), Deque-based sliding window max.

```java
LinkedList<Integer> ll = new LinkedList<>();
ll.addFirst(1); ll.addLast(2);
ll.removeFirst();
```

---

### 🧓 Vector
- **What:** Legacy synchronized resizable array (since JDK 1.0).
- **Internal Working:** Same as ArrayList but every method is `synchronized`; grows by **2x**.
- **When to use:** Rarely — only legacy code or true thread-safety need (better: `CopyOnWriteArrayList`).
- **Thread Safe:** ✅ (method-level lock, but NOT compound-action safe)
- **Duplicates:** ✅ | **Null:** ✅ | **Ordered:** ✅ | **Sorted:** ❌

> ⚠️ **Common Mistake:** Assuming `Vector` is fully thread-safe for compound operations like check-then-act — it isn't; needs external sync.

🚀 **LeetCode Usage:** Almost never used — avoid.

---

### 🥞 Stack
- **What:** LIFO structure, extends `Vector` (legacy).
- **Methods:** `push()`, `pop()`, `peek()`, `isEmpty()`
- **Time Complexity:** All O(1)
- **When NOT to use:** Modern code — prefer `Deque` (`ArrayDeque`) as stack.
- **Thread Safe:** ✅ (inherited from Vector, but same caveats)

> 💡 **Interview Tip:** Interviewers expect you to say *"In production I'd use `Deque<Integer> stack = new ArrayDeque<>();` instead of `Stack`"*.

🚀 **LeetCode Usage:** Valid Parentheses, Next Greater Element, Monotonic Stack, Backtracking (call stack simulation), DFS iterative.

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1); stack.pop(); stack.peek();
```

---

### 🚪 Queue (Interface)
- **What:** FIFO interface. Implementations: `LinkedList`, `PriorityQueue`, `ArrayDeque`.
- **Methods:** `offer()`/`add()`, `poll()`/`remove()`, `peek()`/`element()`
- **Difference offer/add:** `offer` returns `false` on failure (capacity-restricted queues); `add` throws exception.

> ⚠️ **Common Mistake:** Calling `poll()` on empty queue (returns `null`, doesn't throw) vs `remove()` (throws `NoSuchElementException`) — mixing these causes silent `NullPointerException` later.

🚀 **LeetCode Usage:** BFS (graph/tree level order), task scheduling.

---

### ⛰️ PriorityQueue
- **What:** Heap-based queue; orders by natural ordering or custom `Comparator`. **Min-Heap by default**.
- **Internal DS:** Binary Heap (array-backed).
- **Time Complexity:**

| Operation | Complexity |
|---|---|
| Insert (offer) | O(log n) |
| Remove top (poll) | O(log n) |
| Peek | O(1) |
| Search | O(n) |

- **Duplicates:** ✅ | **Null:** ❌ (throws NPE) | **Ordered:** ❌ (heap order, not insertion) | **Sorted:** Partially (only root guaranteed)
- **Thread Safe:** ❌ (use `PriorityBlockingQueue`)

> 💡 **Interview Tip:** For Max-Heap → `new PriorityQueue<>(Collections.reverseOrder())`.

> ⚠️ **Common Mistake:** Iterating a `PriorityQueue` with a for-each and expecting **sorted order** — it only guarantees the **head** is smallest, not full order.

🚀 **LeetCode Usage:** Kth largest/smallest, Top K elements, Merge K sorted lists, Dijkstra, Huffman encoding, Median of stream.

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
PriorityQueue<int[]> pq = new PriorityQueue<>((a,b) -> a[0] - b[0]); // custom comparator
```

---

### ↔️ ArrayDeque
- **What:** Resizable-array implementation of `Deque`. Can act as **Stack** or **Queue**.
- **Internal Working:** Circular array, doubles capacity on overflow.
- **Time Complexity:** O(1) for `addFirst/addLast/removeFirst/removeLast`.
- **Null:** ❌ Not allowed | **Faster than `Stack`/`LinkedList`** for stack/queue ops (no node overhead, no sync overhead).

> 💡 **Interview Tip:** "Why is `ArrayDeque` preferred over `Stack`/`LinkedList`?" → No synchronization overhead, better cache locality, no null restriction confusion.

🚀 **LeetCode Usage:** Sliding Window Maximum, Monotonic Deque, Stack simulation, Palindrome checks (deque from both ends).

```java
Deque<Integer> dq = new ArrayDeque<>();
dq.offerFirst(1); dq.offerLast(2); dq.pollFirst(); dq.pollLast();
```

---

### 🔢 HashSet
- **What:** Set backed by `HashMap` internally (stores elements as keys with dummy value `PRESENT`).
- **Time Complexity:** O(1) average for add/remove/contains; O(n) worst case (hash collisions).
- **Duplicates:** ❌ | **Null:** ✅ (one null) | **Ordered:** ❌ | **Sorted:** ❌ | **Thread Safe:** ❌

> 💡 **Interview Tip:** `HashSet` = `HashMap` under the hood. If asked "how is uniqueness ensured?" → via `hashCode()` + `equals()`.

> ⚠️ **Common Mistake:** Forgetting to override both `hashCode()` and `equals()` for custom objects → duplicates slip in.

🚀 **LeetCode Usage:** Deduplication, "contains duplicate", visited-node tracking in graphs, fast lookup sets.

---

### 🔗🔢 LinkedHashSet
- **What:** `HashSet` + maintains **insertion order** via internal doubly linked list.
- **Time Complexity:** Same as HashSet, slightly more memory.
- **Ordered:** ✅ (insertion order) | **Sorted:** ❌

🚀 **LeetCode Usage:** When you need uniqueness **and** order preserved (e.g., LRU-like dedup, "first unique element" problems).

---

### 🌳 TreeSet
- **What:** `NavigableSet` implementation backed by **Red-Black Tree**.
- **Time Complexity:** O(log n) for add/remove/contains.
- **Ordered:** ✅ Sorted (natural or Comparator) | **Null:** ❌ (NPE on null, except first add in old versions) | **Duplicates:** ❌
- **Extra methods:** `first()`, `last()`, `ceiling()`, `floor()`, `higher()`, `lower()`, `headSet()`, `tailSet()`.

> 💡 **Interview Tip:** `ceiling(x)` = smallest ≥ x, `floor(x)` = largest ≤ x, `higher(x)` = smallest > x, `lower(x)` = largest < x. Asked constantly in "closest element" problems.

🚀 **LeetCode Usage:** Order statistics, range queries, "find ceiling/floor", calendar/interval scheduling, sliding window with sorted constraint.

```java
TreeSet<Integer> ts = new TreeSet<>(List.of(5,1,9,3));
ts.ceiling(4); // 5
ts.floor(4);   // 3
```

---

### 🗺️ HashMap
- **What:** Hash table based `Map`. Stores key-value pairs.
- **Internal Working:** Array of `Node<K,V>[]` buckets. `index = hash(key) & (n-1)`. Since Java 8, buckets with >8 collisions convert to a **Red-Black Tree** (O(log n) instead of O(n)). Default capacity 16, load factor 0.75, resizes (doubles) when threshold crossed.
- **Time Complexity:** O(1) average, O(log n) worst case (Java 8+ with treeification).
- **Duplicates (keys):** ❌ | **Null:** ✅ one null key, multiple null values | **Ordered:** ❌ | **Thread Safe:** ❌ (use `ConcurrentHashMap`)

> 💡 **Interview Tip:** "Why load factor 0.75?" → Balances time-space tradeoff (too low wastes memory, too high increases collisions).

> 💡 **Interview Tip:** Treeification threshold = 8 collisions in a bucket, untreeify at 6 — Java 8 optimization, very commonly asked.

> ⚠️ **Common Mistake:** Mutating a key object after inserting into HashMap (e.g., mutable `List` as key) → breaks `hashCode()` consistency, lookup fails.

🚀 **LeetCode Usage:** Frequency counting, Two Sum, grouping (anagrams), memoization in DP, graph adjacency list.

```java
Map<String,Integer> freq = new HashMap<>();
freq.put("a", 1);
freq.merge("a", 1, Integer::sum);          // increment
freq.computeIfAbsent("b", k -> 0);
freq.getOrDefault("c", 0);
```

---

### 🔗🗺️ LinkedHashMap
- **What:** `HashMap` + maintains insertion order (or **access order** if configured) via linked list.
- **Time Complexity:** Same as HashMap.
- **Ordered:** ✅ | Special use: `removeEldestEntry()` override → **build LRU Cache** in ~10 lines.

> 💡 **Interview Tip:** `new LinkedHashMap<>(cap, 0.75f, true)` → access-order mode = foundation of LRU Cache.

🚀 **LeetCode Usage:** **LRU Cache (#146)** is the signature problem — asked at almost every product company.

```java
class LRUCache extends LinkedHashMap<Integer, Integer> {
    int capacity;
    LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > capacity;
    }
}
```

---

### 🌲🗺️ TreeMap
- **What:** `NavigableMap`, backed by Red-Black Tree, keys sorted.
- **Time Complexity:** O(log n) for get/put/remove.
- **Ordered:** ✅ Sorted by key | **Null key:** ❌ | **Null value:** ✅
- **Extra methods:** `firstKey()`, `lastKey()`, `ceilingKey()`, `floorKey()`, `higherKey()`, `lowerKey()`, `headMap()`, `tailMap()`, `subMap()`.

🚀 **LeetCode Usage:** Range sum queries, calendar booking, interval merging, "nearest key" lookups, order-statistics maps.

---

### 🧓🗺️ Hashtable
- **What:** Legacy synchronized Map (JDK 1.0).
- **Null:** ❌ Neither null key nor null value allowed.
- **Thread Safe:** ✅ (but coarse-grained locking, slow) — prefer `ConcurrentHashMap`.

> ⚠️ **Common Mistake:** Confusing `Hashtable` (no nulls, synchronized) with `HashMap` (allows null) in MCQ-style questions.

🚀 **LeetCode Usage:** Essentially never used in practice — legacy only.

---

## 4️⃣ Comparison Tables

### ArrayList vs LinkedList
| Feature | ArrayList | LinkedList |
|---|---|---|
| Structure | Dynamic array | Doubly linked list |
| Access | O(1) | O(n) |
| Insert/Delete (end) | O(1) amortized | O(1) |
| Insert/Delete (middle) | O(n) | O(n) (search) |
| Memory | Less overhead | More (node pointers) |
| Implements | List, RandomAccess | List, Deque |

### HashSet vs LinkedHashSet vs TreeSet
| Feature | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Order | None | Insertion order | Sorted order |
| Backing DS | HashMap | LinkedHashMap | TreeMap (Red-Black Tree) |
| Time Complexity | O(1) | O(1) | O(log n) |
| Null | 1 allowed | 1 allowed | Not allowed |

### HashMap vs TreeMap vs LinkedHashMap vs Hashtable
| Feature | HashMap | TreeMap | LinkedHashMap | Hashtable |
|---|---|---|---|---|
| Order | None | Sorted | Insertion/Access | None |
| Time Complexity | O(1) avg | O(log n) | O(1) avg | O(1) avg |
| Null key | 1 allowed | Not allowed | 1 allowed | Not allowed |
| Thread Safe | No | No | No | Yes |
| Backing DS | Array + LinkedList/Tree | Red-Black Tree | Hash + LinkedList | Array |

### Queue vs Deque vs PriorityQueue
| Feature | Queue | Deque | PriorityQueue |
|---|---|---|---|
| Order | FIFO | Both ends | Priority (heap order) |
| Insert/Remove | one end each | both ends | O(log n) |
| Use case | BFS | Sliding window, stack+queue | Top-K, Dijkstra |

### Stack vs ArrayDeque (as Stack)
| Feature | Stack | ArrayDeque |
|---|---|---|
| Sync | Yes (slow) | No (fast) |
| Legacy | Yes | No (modern) |
| Recommended | ❌ | ✅ |

### Collections vs Collection
| Feature | Collection | Collections |
|---|---|---|
| Type | Interface | Final utility class |
| Purpose | Represents a group of objects | Algorithms on collections |

### Comparable vs Comparator
| Feature | Comparable | Comparator |
|---|---|---|
| Package | java.lang | java.util |
| Method | `compareTo(T o)` | `compare(T o1, T o2)` |
| Modifies class | Yes (implemented by the class itself) | No (external, separate logic) |
| Sorting logic | Single natural order | Multiple custom orders |
| Used by | `Collections.sort(list)` | `Collections.sort(list, comparator)` |

---

## 5️⃣ Master Time Complexity Cheat Sheet

| Collection | Insert | Delete | Search | Access | Iteration |
|---|---|---|---|---|---|
| ArrayList | O(1)* / O(n) | O(n) | O(n) | O(1) | O(n) |
| LinkedList | O(1) | O(1) | O(n) | O(n) | O(n) |
| Vector | O(1)* / O(n) | O(n) | O(n) | O(1) | O(n) |
| Stack/ArrayDeque | O(1) | O(1) | O(n) | O(n)/O(1) end | O(n) |
| PriorityQueue | O(log n) | O(log n) | O(n) | O(1) top | O(n) |
| HashSet | O(1) | O(1) | O(1) | — | O(n) |
| LinkedHashSet | O(1) | O(1) | O(1) | — | O(n) |
| TreeSet | O(log n) | O(log n) | O(log n) | O(log n) min/max | O(n) sorted |
| HashMap | O(1) | O(1) | O(1) | O(1) | O(n) |
| LinkedHashMap | O(1) | O(1) | O(1) | O(1) | O(n) ordered |
| TreeMap | O(log n) | O(log n) | O(log n) | O(log n) | O(n) sorted |
| Hashtable | O(1) | O(1) | O(1) | O(1) | O(n) |

*amortized at end; O(n) at start/middle.

---

## 6️⃣ Frequently Used Methods (Syntax + Examples)

```java
// List
list.add(5);                    // append
list.add(0, 5);                 // insert at index
list.remove(2);                 // remove by index
list.get(0);
list.contains(5);
list.size(); list.isEmpty(); list.clear();

// Map
map.put("a", 1);
map.get("a");
map.containsKey("a");
map.replace("a", 2);
map.putIfAbsent("b", 0);
map.computeIfAbsent("c", k -> new ArrayList<>());
map.merge("a", 1, Integer::sum);

// Deque/Queue
dq.offer(1); dq.poll(); dq.peek();
dq.push(1); dq.pop();            // stack-style

// Collections utility
Collections.sort(list);
Collections.sort(list, Collections.reverseOrder());
Collections.reverse(list);
Collections.binarySearch(list, 5);   // list must be sorted
Collections.fill(list, 0);
Collections.copy(dest, src);
Collections.shuffle(list);
Collections.min(list); Collections.max(list);
Collections.frequency(list, 5);
Collections.disjoint(list1, list2);  // true if no common elements
```

---

## 7️⃣ Collections Utility Class — Key Methods

| Method | Purpose |
|---|---|
| `sort(list)` | Sorts using natural order |
| `sort(list, comparator)` | Sorts with custom order |
| `reverse(list)` | Reverses order |
| `shuffle(list)` | Randomizes order |
| `binarySearch(list, key)` | O(log n) search — list must be pre-sorted |
| `min()/max()` | Min/max element |
| `frequency(list, obj)` | Count occurrences |
| `disjoint(c1, c2)` | True if no common elements |
| `unmodifiableList()` | Read-only wrapper |
| `synchronizedList()` | Thread-safe wrapper |
| `emptyList()/singletonList()` | Immutable helpers |

> 💡 **Interview Tip:** `Collections.unmodifiableList()` returns a **view** — modifying the original list still affects it. It's not a deep immutable copy.

---

## 8️⃣ Comparator & Comparable — Deep Dive

### Comparable (natural ordering, inside the class)
```java
class Student implements Comparable<Student> {
    int marks;
    public int compareTo(Student o) {
        return this.marks - o.marks; // ascending
    }
}
```

### Comparator (external, flexible, multiple orderings)
```java
Comparator<Student> byMarks = (a, b) -> a.marks - b.marks;
Comparator<Student> byMarksDesc = Comparator.comparingInt((Student s) -> s.marks).reversed();

// Multi-level sorting
list.sort(Comparator.comparing(Student::getGrade)
                     .thenComparing(Student::getMarks));
```

> ⚠️ **Common Mistake:** `return a.marks - b.marks;` overflows for large/negative numbers → use `Integer.compare(a, b)` instead.

> 💡 **Interview Question:** "Sort an array of Strings by length, then alphabetically" 
```java
Arrays.sort(arr, Comparator.comparingInt(String::length).thenComparing(Comparator.naturalOrder()));
```

---

## 9️⃣ Iterator vs ListIterator

```
Iterator           →  forward only,  works on all Collections
ListIterator        →  forward + backward, works only on List
```

| Feature | Iterator | ListIterator |
|---|---|---|
| Direction | Forward only | Both directions |
| Works on | Any Collection | List only |
| Modify | `remove()` only | `remove()`, `add()`, `set()` |
| Index access | ❌ | ✅ (`nextIndex()`, `previousIndex()`) |

```java
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() == 3) it.remove(); // safe removal during iteration
}
```

> ⚠️ **Common Mistake:** Removing elements with `list.remove()` while in a for-each loop → throws `ConcurrentModificationException`. Always use `Iterator.remove()`.

---

## 🔟 Fail-Fast vs Fail-Safe Iterators

| | Fail-Fast | Fail-Safe |
|---|---|---|
| Behavior | Throws `ConcurrentModificationException` on structural modification during iteration | Iterates over a **clone/snapshot**, no exception |
| Mechanism | Uses internal `modCount` check | Works on copy of data (or CoW) |
| Examples | `ArrayList`, `HashMap`, `HashSet` | `CopyOnWriteArrayList`, `ConcurrentHashMap` |
| Memory | Low | Higher (copy overhead) |

> 💡 **Interview Tip:** Fail-safe doesn't mean "no consistency issues" — you may iterate over **stale data**.

---

## 1️⃣1️⃣ Which Collection Should I Use? — Decision Tree

```
Need Key-Value pairs?
 ├─ Yes
 │   ├─ Need sorted by key?        → TreeMap
 │   ├─ Need insertion order?      → LinkedHashMap
 │   ├─ Need thread safety?        → ConcurrentHashMap
 │   └─ Else                       → HashMap
 │
 └─ No (single elements)
     ├─ Need uniqueness?
     │   ├─ Yes
     │   │   ├─ Need sorted order? → TreeSet
     │   │   ├─ Need insertion order? → LinkedHashSet
     │   │   └─ Else               → HashSet
     │   └─ No
     │       ├─ Need FIFO?         → LinkedList / ArrayDeque (as Queue)
     │       ├─ Need LIFO?         → ArrayDeque (as Stack)
     │       ├─ Need priority order? → PriorityQueue
     │       ├─ Need random access? → ArrayList
     │       └─ Need frequent insert/delete at ends → LinkedList/ArrayDeque
```

---

## 1️⃣2️⃣ LeetCode Cheat Sheet — Collection by Pattern

| Pattern | Go-to Collections |
|---|---|
| Arrays/Strings | ArrayList, HashMap (frequency) |
| Sliding Window | HashMap (char count), ArrayDeque (max/min window) |
| Binary Search | Arrays/Collections.binarySearch, TreeMap/TreeSet (ceiling/floor) |
| Trees | ArrayDeque (BFS), ArrayList (level order output) |
| BST | TreeMap, TreeSet |
| Heap | PriorityQueue |
| Graphs | ArrayList<List<Integer>> (adjacency), HashSet (visited), ArrayDeque (BFS), Stack/recursion (DFS) |
| Trie | HashMap<Character, Node> or array of 26 |
| Backtracking | ArrayList (path), boolean[] / HashSet (visited) |
| Dynamic Programming | HashMap (memoization), 2D array |
| Topological Sort | ArrayDeque (queue for Kahn's algo), HashMap (in-degree) |
| BFS | ArrayDeque |
| DFS | ArrayDeque/Stack (iterative) or recursion |
| Union-Find | int[] parent array (not a "Collection" but pairs with HashMap for compression) |
| Monotonic Stack | ArrayDeque |
| Greedy | PriorityQueue (often), TreeMap (intervals) |
| Intervals | ArrayList + sort by Comparator, TreeMap |

---

## 1️⃣3️⃣ Interview Corner

### 🔥 Most Asked Questions
1. How does `HashMap` work internally? (hashing, buckets, collision via tree, resize)
2. Why is `HashMap` not thread-safe? How does `ConcurrentHashMap` fix it? (segment/bucket-level locking)
3. Difference between `HashMap` and `Hashtable`?
4. How does `HashSet` ensure uniqueness? (hashCode + equals contract)
5. What happens if you don't override `hashCode()`/`equals()`?
6. Why does `ArrayList` resize by 1.5x and `Vector` by 2x?
7. Internal structure of `PriorityQueue`? How is heapify done?
8. `Comparable` vs `Comparator` — when to use which?
9. Fail-fast vs Fail-safe iterators?
10. How to make a `HashMap` thread-safe? (`ConcurrentHashMap`, `Collections.synchronizedMap`)
11. Implement LRU Cache using `LinkedHashMap`.
12. Why is `String` immutable and how does that help `HashMap` keys?
13. `TreeMap` vs `HashMap` — when does ordering matter enough to pay O(log n)?

### ⚠️ Common Pitfalls
- Using mutable objects as `HashMap`/`HashSet` keys.
- Modifying a collection while iterating without `Iterator.remove()`.
- Assuming `PriorityQueue` iteration order = sorted order.
- Confusing `remove(int index)` vs `remove(Object o)` in `ArrayList<Integer>`.
- Forgetting `Hashtable`/`TreeMap` don't allow null keys.

### ✅ Best Practices
- Prefer **interfaces** in declarations: `List<Integer> l = new ArrayList<>();`
- Use `ArrayDeque` instead of `Stack`/legacy `Queue`.
- Always override `equals()` + `hashCode()` together.
- Use `var` + diamond operator for cleaner code in modern Java.
- Pre-size collections when final size is roughly known (`new ArrayList<>(1000)`).

### 🚀 Performance Tips
- Avoid boxing/unboxing overhead in hot loops (`int` vs `Integer` in PQ comparators).
- Use `EnumMap`/`EnumSet` for enum keys (faster than HashMap/HashSet).
- Batch insert with `addAll()` instead of looping `add()` when possible.

---

## 1️⃣4️⃣ Quick Code Snippets Library

```java
// Group Anagrams pattern
Map<String, List<String>> groups = new HashMap<>();
for (String s : strs) {
    char[] arr = s.toCharArray();
    Arrays.sort(arr);
    String key = new String(arr);
    groups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
}

// Top K Frequent Elements
PriorityQueue<Map.Entry<Integer,Integer>> pq =
    new PriorityQueue<>((a,b) -> a.getValue() - b.getValue());

// BFS template
Queue<Node> queue = new ArrayDeque<>();
Set<Node> visited = new HashSet<>();
queue.offer(start); visited.add(start);
while (!queue.isEmpty()) {
    Node cur = queue.poll();
    for (Node nbr : cur.neighbors) {
        if (visited.add(nbr)) queue.offer(nbr);
    }
}

// Sliding Window Maximum (monotonic deque)
Deque<Integer> dq = new ArrayDeque<>(); // stores indices
for (int i = 0; i < nums.length; i++) {
    while (!dq.isEmpty() && nums[dq.peekLast()] < nums[i]) dq.pollLast();
    dq.offerLast(i);
    if (dq.peekFirst() <= i - k) dq.pollFirst();
}
```

---

## 📌 Final Revision Notes

- **List** → ordered, indexable, duplicates allowed → ArrayList (default), LinkedList (ends), Vector/Stack (avoid).
- **Set** → uniqueness → HashSet (fast), LinkedHashSet (order), TreeSet (sorted).
- **Queue/Deque** → FIFO/LIFO/Priority → ArrayDeque (default), PriorityQueue (heap).
- **Map** → key-value → HashMap (default), LinkedHashMap (order/LRU), TreeMap (sorted), Hashtable (legacy, avoid).
- When in doubt at an interview: **start with the use-case (ordering, uniqueness, sorting, thread-safety needs), then pick the collection** — that's exactly how interviewers expect you to reason out loud.

---
*Part of Placement-Prep-2026 repository.*