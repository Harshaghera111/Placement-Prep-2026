# #️⃣ Hashing — Placement Edition
### Notes from a "been-there, interviewed-that" professor's lens

> 🎯 Goal: Build a solid mental model of Hashing — understand it well enough that any interview question on frequency counting, lookups, or duplicates feels mechanical.
> Source: [Striver's A2Z DSA Sheet — Learn Basic Hashing](https://takeuforward.org/dsa/strivers-a2z-sheet-learn-dsa-a-to-z/)

---

## 📚 Table of Contents

**Part 1 — Theory & Fundamentals**
1. [A Word Before We Start](#a-word-before-we-start)
2. [What is Hashing?](#1-what-is-hashing)
3. [Why Do We Need Hashing?](#2-why-do-we-need-hashing)
4. [The Problem Hashing Solves](#3-the-problem-hashing-solves)
5. [Brute-Force Frequency Counting](#4-brute-force-frequency-counting)
6. [Precomputation / Preprocessing](#5-precomputation--preprocessing)
7. [Hash Array — Frequency Counting with Arrays](#6-hash-array--frequency-counting-with-arrays)
8. [Character Hashing](#7-character-hashing)
9. [Number Hashing](#8-number-hashing)
10. [HashMap](#9-hashmap)
11. [HashSet](#10-hashset)
12. [Hash Function](#11-hash-function)
13. [Hash Table & Hash Index](#12-hash-table--hash-index)
14. [Collision](#13-collision)
15. [Division Method](#14-division-method)
16. [Handling Collisions — High Level](#15-handling-collisions--high-level)
17. [Average-Case vs Worst-Case Complexity](#16-average-case-vs-worst-case-complexity)
18. [Why HashMap Operations Are O(1) on Average](#17-why-hashmap-operations-are-o1-on-average)
19. [When Array Hashing is Better Than HashMap](#18-when-array-hashing-is-better-than-hashmap)
20. [When HashMap is Necessary](#19-when-hashmap-is-necessary)
21. [Space vs Time Trade-Off](#20-space-vs-time-trade-off)

**Part 2 — Striver Problems**
22. [Problem 1 — Basic Hashing](#problem-1--basic-hashing)
23. [Problem 2 — Counting Frequencies of Array Elements](#problem-2--counting-frequencies-of-array-elements)
24. [Problem 3 — Highest Occurring Element in an Array](#problem-3--highest-occurring-element-in-an-array)

**Part 3 — Cheat Sheet & Revision**
25. [How Hashing Connects to Future DSA Problems](#how-hashing-connects-to-future-dsa-problems)
26. [Hashing Patterns Learned](#hashing-patterns-learned)
27. [Hashing Cheat Sheet](#hashing-cheat-sheet)
28. [Important Java HashMap / HashSet Methods](#important-java-hashmap--hashset-methods)
29. [Common Interview Questions](#common-interview-questions)
30. [Common Mistakes](#common-mistakes)
31. [Final Revision Notes](#final-revision-notes)
32. [Revision Checklist](#revision-checklist)

---

## A Word Before We Start

Hashing is the topic that separates candidates who write O(n²) solutions from candidates who write O(n) solutions — and interviewers know exactly which side you're on the moment they see your first attempt.

The honest truth: most beginners already know about `HashMap`. The problem is they don't understand *why* it works, when to use it versus a simple array, and what it costs. That vagueness costs you points in the explanation round.

This section fixes that. Same format as the rest of this repository:
1. **What it is** (with real intuition, not textbook language)
2. **Why it bites you** (the traps interviewers exploit)
3. **How to not embarrass yourself** (the instincts you need cold)

Don't rush to the problems. The theory section here is short enough to read in one sitting and dense enough to carry you through six months of placement rounds.

---

## 1. What is Hashing?

### Concept

Hashing is a technique to **map data to a fixed-size index** so that you can store and retrieve information in O(1) time — regardless of how much data you have.

Instead of searching through the entire data to find something, you compute a **hash index** directly from the key, go to that index, and either store or retrieve the value in one step.

```java
// Without hashing: to find if 7 exists in [3, 1, 7, 9, 2]
// you must scan every element → O(n)

// With hashing: compute hash(7) → gives index 7
// go to index 7 in your storage → check → O(1)
```

### Professor's Note — Hashing is Not HashMap

> ⚠️ **Common Trap:** Students say "hashing" when they mean "`HashMap`." They're not the same thing.
>
> **Hashing** is the *concept* — a technique of mapping keys to indices using a function.
> **HashMap** is a *Java class* that *implements* hashing internally.
>
> You can do hashing with a plain `int[]` array (no `HashMap` needed). Understanding this distinction is the difference between a candidate who *uses* a tool and one who *understands* it.

### Memory Tip
Hashing = mapping key → index using a function. The point is O(1) access. HashMap is one implementation. A plain array is another.

---

## 2. Why Do We Need Hashing?

### Concept

The entire point of hashing is **speed**. Specifically, trading a bit of extra space for drastically faster lookups.

Consider the classic question: *"Given an array, how many times does each element appear?"*

Without hashing, you'd need a nested loop — O(n²). That's unacceptable in any interview after the first round.

With hashing, you scan once to build a frequency map, then answer every query in O(1). The total work is O(n), regardless of how many queries follow.

> 💡 **Placement Insight:** The single most common interview follow-up after a brute-force solution is "can you do better?" In a majority of array and string problems, the answer is "yes, using a HashMap or frequency array." Having this reflex trained is worth more than memorizing any single algorithm.

### Memory Tip
Nested loops on an array → ask yourself immediately: "Can I precompute with a hash?" Nine times out of ten, you can.

---

## 3. The Problem Hashing Solves

### Concept

Let's make this concrete. Suppose you have this array:

```
arr = [1, 3, 2, 1, 3, 1, 4]
```

And someone asks: *"How many times does 3 appear?"*

**Without hashing:** loop through all 7 elements every time someone asks. Ask 100 questions → 700 operations.

**With hashing:** loop once (7 operations) to build a frequency table. Answer every question in O(1) forever after. Ask 100 questions → 7 + 100 = 107 operations.

This is called **precomputation** — do the hard work once upfront, make every lookup trivially cheap afterward.

The table you build is called a **hash array** (if the range of keys is small and known) or a **HashMap** (if the range is large or unknown).

---

## 4. Brute-Force Frequency Counting

### Concept

Before seeing the hashing solution, understand exactly what you're replacing and why you're replacing it.

```java
// Brute force: for each query element, scan the entire array
public static int countOccurrences(int[] arr, int target) {
    int count = 0;
    for (int i = 0; i < arr.length; i++) {   // O(n) per query
        if (arr[i] == target) {
            count++;
        }
    }
    return count;
}
```

**Time complexity:** O(n) per query.
**For q queries:** O(n × q).

If `n = 100,000` and `q = 100,000`, that's **10 billion operations**. A modern CPU does about 10⁸–10⁹ simple operations per second. This **will not pass** any judge's time limit.

> ⚠️ **Interview Trap:** Writing this brute force and saying "it works" without mentioning time complexity is exactly the mistake that gets you filtered at larger companies. They're not testing if your code runs — they're testing if you know *why* it's slow and what to do about it.

---

## 5. Precomputation / Preprocessing

### Concept

Precomputation means **doing work upfront once** so that future queries become cheap.

The pattern is always the same:
1. **Build phase** — scan the array once: O(n). Store frequency/existence/value into a structure.
2. **Query phase** — answer each question: O(1) using the precomputed structure.

```java
// Precomputation pattern (conceptual):

// Step 1: Build (O(n))
for (each element in array) {
    store information about this element
}

// Step 2: Query (O(1) each)
for (each query) {
    look up stored information directly
}
```

This is not specific to hashing — prefix sums work the same way. But hashing is the most flexible precomputation tool because it handles arbitrary keys, not just indices.

> 💡 **Placement Insight:** When an interviewer says "multiple queries on the same array," that's their signal that they want precomputation. Recognize the hint — don't re-scan the array for every query.

---

## 6. Hash Array — Frequency Counting with Arrays

### Concept

The simplest form of hashing is a plain `int[]` used as a frequency table. No `HashMap` needed.

If your data values fall in a small, known, non-negative range — say, integers from 0 to 9, or characters 'a' to 'z' — you can use the **value itself as the index**.

```java
int[] arr = {1, 3, 2, 1, 3, 1, 4};

// Create a hash array (frequency array)
int[] freq = new int[10];  // supports values 0–9

// Build: O(n)
for (int num : arr) {
    freq[num]++;   // use the number itself as the array index
}

// Query: O(1)
System.out.println(freq[1]);  // → 3  (1 appears 3 times)
System.out.println(freq[3]);  // → 2  (3 appears 2 times)
System.out.println(freq[7]);  // → 0  (7 doesn't appear)
```

### What's Actually Happening

```
Value:     1    3    2    1    3    1    4
           ↓    ↓    ↓    ↓    ↓    ↓    ↓
           freq[1]++ freq[3]++ freq[2]++ ... and so on

After build:
Index:    0    1    2    3    4    5    6    7    8    9
freq:    [0,   3,   1,   2,   1,   0,   0,   0,   0,   0]
                ↑              ↑
           1 appears 3    3 appears 2
              times           times
```

### The Key Insight
The array *index* IS the key. No mapping function needed when values can serve directly as indices.

> ⚠️ **Common Trap:** Using a hash array when values can be negative or very large (e.g., values up to 10⁹). A `freq` array of size 10⁹ uses ~4GB of memory — that crashes immediately. For large or negative values, use `HashMap` instead.

### When This Works
- Values are non-negative integers.
- The maximum value is small (typically ≤ 10⁶).
- You know the range in advance.

### When This Doesn't Work
- Values can be negative.
- Values can be very large (10⁸ or more).
- Keys are strings or objects.

---

## 7. Character Hashing

### Concept

Characters are a natural fit for array-based hashing because:
- There are only 26 lowercase English letters.
- Each character has a known ASCII value.
- You can map any character to an index from 0 to 25 using subtraction.

```java
String s = "placement";

// Lowercase character hashing
int[] charFreq = new int[26];  // index 0 = 'a', index 25 = 'z'

for (char c : s.toCharArray()) {
    charFreq[c - 'a']++;   // 'a' → 0, 'b' → 1, ..., 'z' → 25
}

// Query: how many times does 'e' appear?
System.out.println(charFreq['e' - 'a']);  // → 2
```

For problems that include both uppercase and lowercase:
```java
int[] charFreq = new int[52];
// 'A'–'Z' → index 0–25
// 'a'–'z' → index 26–51

// For uppercase: charFreq[c - 'A']
// For lowercase: charFreq[c - 'a' + 26]
```

For all ASCII characters (128 total):
```java
int[] charFreq = new int[128];
charFreq[c]++;  // use ASCII value directly as index
```

> 💡 **Placement Insight:** Character hashing with a `int[26]` array is faster than using a `HashMap<Character, Integer>` for string problems. Interviewers don't penalize you for using a HashMap, but using the array shows you understand the underlying technique rather than just reaching for a standard library class. Use whichever you're more comfortable with — just know why both work.

### Memory Tip
Lowercase only → `new int[26]`, subtract `'a'`. All ASCII → `new int[128]`. Uppercase only → subtract `'A'`.

---

## 8. Number Hashing

### Concept

Number hashing with arrays works when numbers are small and non-negative. When numbers are large or can be negative, you must switch to `HashMap`.

```java
// Safe: small numbers (e.g., values 0 to 1000)
int[] freq = new int[1001];
for (int num : arr) {
    freq[num]++;   // direct index — O(1)
}

// Problem: numbers up to 10^9
// int[] freq = new int[1000000000];  ← DO NOT DO THIS — 4GB memory, crash
```

For large numbers — **use HashMap**:
```java
HashMap<Integer, Integer> freq = new HashMap<>();
for (int num : arr) {
    freq.put(num, freq.getOrDefault(num, 0) + 1);
}
```

> ⚠️ **Common Trap:** Forgetting that negative numbers cannot be used as array indices. If the problem says "integers" without specifying range, or says "can be negative," go directly to HashMap. Don't try to offset with a bias unless the range is explicitly bounded.

### Decision Rule

```
If values are non-negative AND max value is small (≤ 10^6):
    → Use int[] array (faster, less overhead)

If values can be negative OR max value is large (> 10^6):
    → Use HashMap<Integer, Integer>
```

---

## 9. HashMap

### Concept

`HashMap<K, V>` is Java's built-in hash table. It stores **key-value pairs** and provides O(1) average-case performance for `put`, `get`, and `containsKey`.

```java
import java.util.HashMap;

HashMap<Integer, Integer> map = new HashMap<>();

// put(key, value) — insert or update
map.put(1, 3);       // key=1, value=3 (element 1 appears 3 times)
map.put(3, 2);       // key=3, value=2

// get(key) — retrieve value for key (returns null if key not found)
int count = map.get(1);   // → 3

// getOrDefault(key, defaultValue) — safe retrieval
int count2 = map.getOrDefault(7, 0);  // → 0 (key 7 doesn't exist)

// containsKey(key) — check if key exists
if (map.containsKey(1)) {
    System.out.println("1 is in the map");
}

// remove(key) — delete a key-value pair
map.remove(3);

// size() — number of key-value pairs
System.out.println(map.size());  // → 1 (only key 1 remains)
```

### Method Explanations

| Method | What It Does | Common Use |
|---|---|---|
| `map.put(k, v)` | Inserts or replaces. If key exists, overwrites the value. | Adding/updating frequency counts |
| `map.get(k)` | Returns value for key. Returns `null` if key doesn't exist. | Retrieving a stored value when you're sure key exists |
| `map.getOrDefault(k, d)` | Returns value for key, or `d` if key doesn't exist. | Frequency counting without null-checks |
| `map.containsKey(k)` | Returns `true` if the key exists. | Checking existence before accessing |
| `map.remove(k)` | Removes the key-value pair. Returns the removed value. | Cleanup, sliding window shrinking |
| `map.size()` | Returns number of key-value pairs. | Checking if map is empty or has specific count |
| `map.entrySet()` | Returns all key-value pairs as a `Set<Map.Entry<K,V>>`. | Iterating over all entries |

### Frequency Counting Pattern — The One You Must Know Cold

```java
int[] arr = {1, 3, 2, 1, 3, 1, 4};
HashMap<Integer, Integer> freq = new HashMap<>();

for (int num : arr) {
    // If key exists → get its count and add 1
    // If key doesn't exist → start from 0 and add 1
    freq.put(num, freq.getOrDefault(num, 0) + 1);
}

// freq = {1→3, 3→2, 2→1, 4→1}
```

> 💡 **Placement Insight:** `freq.getOrDefault(num, 0) + 1` is the single most important HashMap idiom for frequency counting. You'll write this line in approximately 40% of all hashing interview problems. Know it without thinking. The alternative — checking `containsKey` before every `put` — works but is verbose and signals you haven't internalized the clean pattern.

> ⚠️ **Common Trap:** Confusing key and value. In a frequency map, the **key is the element** and the **value is its count**. Writing `map.put(count, element)` is a silent bug that produces wrong output and wastes your debug time in an interview.

---

## 10. HashSet

### Concept

`HashSet<E>` stores unique elements with O(1) average-case operations. It is essentially a `HashMap` with only keys and no values — internally, it uses a `HashMap<E, Object>` with a dummy value.

Use `HashSet` when you care about **existence** (is this element present?), not frequency.

```java
import java.util.HashSet;

HashSet<Integer> set = new HashSet<>();

// add(element) — inserts element, does nothing if already present
set.add(1);
set.add(3);
set.add(1);  // duplicate — silently ignored

System.out.println(set);  // [1, 3]

// contains(element) — O(1) membership check
System.out.println(set.contains(1));   // → true
System.out.println(set.contains(7));   // → false

// remove(element) — removes element
set.remove(3);

// size() — number of unique elements
System.out.println(set.size());  // → 1
```

### Method Explanations

| Method | What It Does | Returns |
|---|---|---|
| `set.add(e)` | Adds element. Ignores if duplicate. | `true` if added, `false` if already present |
| `set.contains(e)` | Checks if element exists. | `true` / `false` |
| `set.remove(e)` | Removes the element. | `true` if removed, `false` if not found |
| `set.size()` | Number of unique elements. | `int` |
| `set.isEmpty()` | Whether the set has no elements. | `true` / `false` |

### HashMap vs HashSet — When to Use Which

| Need | Use |
|---|---|
| Count how many times something appears | `HashMap<K, Integer>` |
| Check if something exists (yes/no) | `HashSet<E>` |
| Find duplicates | `HashSet<E>` |
| Find the most frequent element | `HashMap<K, Integer>` |
| Check if two arrays share elements | `HashSet<E>` |

> ⚠️ **Common Trap:** Using `HashMap` when you only need `HashSet`. If you're never using the value in your `HashMap` (always setting it to `true` or `1`), switch to `HashSet`. It's cleaner, signals better understanding, and uses less memory.

---

## 11. Hash Function

### Concept

A **hash function** is a mathematical function that maps an input key to an index in a fixed-size array (the hash table).

The requirements of a good hash function:
1. **Deterministic** — the same key always produces the same index.
2. **Uniform distribution** — keys should spread evenly across the table to avoid clustering.
3. **Fast to compute** — must be O(1), otherwise hashing is pointless.

```java
// A simple hash function:
int hashIndex = key % tableSize;

// For key = 42, tableSize = 10:
int index = 42 % 10;  // → 2   (element 42 goes to index 2)

// For key = 52, tableSize = 10:
int index = 52 % 10;  // → 2   (element 52 also goes to index 2!)
// ↑ This is a COLLISION — both 42 and 52 map to index 2
```

> 💡 **Placement Insight:** You won't be asked to implement a hash function from scratch in placement interviews. But you *will* be asked "how does HashMap work internally?" — and the hash function is the first step. Java's `HashMap` computes `key.hashCode()`, then applies a secondary mixing operation to spread bits, then does `index = (n-1) & hash` where `n` is the table size. You don't need to memorize this exactly — you need to understand that *some function maps key → index*, and that *collisions can happen*.

---

## 12. Hash Table & Hash Index

### Concept

A **hash table** is the underlying data structure — an array of fixed size where each position is called a **bucket**.

A **hash index** is the index computed by the hash function for a given key. This is where the key-value pair is stored in the table.

```
Key: 42
Hash function: 42 % 10 = 2
Hash index: 2

Hash Table (size 10):
Index:  0    1    2    3    4    5    6    7    8    9
       [ ]  [ ]  [42] [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]
                  ↑
             42 stored at hash index 2
```

When you call `map.get(42)`, Java:
1. Computes `42.hashCode() → (some hash)`
2. Maps it to a bucket index.
3. Goes directly to that bucket.
4. Returns the value stored there.

All in O(1) — no searching, no comparison with other keys.

---

## 13. Collision

### Concept

A **collision** occurs when two different keys produce the **same hash index**.

```
Key 42 → hash index 2
Key 52 → hash index 2  ← COLLISION
```

Collisions are **inevitable** (by the Pigeonhole Principle — if you have more possible keys than buckets, some keys must share). A good hash function minimizes collisions; a collision-handling strategy deals with the ones that remain.

> 💡 **Placement Insight:** "Does HashMap have O(1) or O(n) operations?" — the honest answer is: **O(1) average case, O(n) worst case**. The worst case happens when all keys hash to the same bucket (all collide), turning the bucket into a linear list of n elements. A good hash function makes this extremely unlikely in practice. This is why Java's `HashMap` applies additional bit-mixing beyond raw `hashCode()` — to reduce the chance of all keys clustering into one bucket.

---

## 14. Division Method

### Concept

The **division method** (also called the modulo method) is the most common hash function formula:

```
hashIndex = key % tableSize
```

Choose `tableSize` to be a prime number for better distribution (prime table sizes cause keys to spread more uniformly — fewer collisions).

```java
int tableSize = 7;  // prime number — distributes better than, say, 10

// Keys: 10, 22, 31, 4, 15, 28
// 10 % 7 = 3
// 22 % 7 = 1
// 31 % 7 = 3  ← collision with 10
//  4 % 7 = 4
// 15 % 7 = 1  ← collision with 22
// 28 % 7 = 0
```

Java's `HashMap` uses `2^n` as the table size (powers of 2) and replaces the modulo with a bitwise AND (`index = (n-1) & hash`) because bitwise operations are faster than modulo. This is an internal implementation detail — you don't need to implement this yourself.

> ⚠️ **Interview Note:** You don't need to memorize the division method formula precisely. The key takeaway: the hash function maps keys to indices using arithmetic, collisions are possible, and prime table sizes help reduce them. That's the answer level expected in a placement interview.

---

## 15. Handling Collisions — High Level

### Two Main Strategies

**1. Chaining (Separate Chaining)**

Each bucket holds a linked list (or, in modern Java's HashMap, a red-black tree when the list gets long). When a collision occurs, the new key-value pair is added to the list at that bucket.

```
Bucket 2: [42 → "A"] → [52 → "B"] → [12 → "C"]
```

- Java's `HashMap` uses chaining.
- In Java 8+, if a bucket's list exceeds 8 entries, it converts to a red-black tree → O(log n) worst case per bucket instead of O(n).

**2. Open Addressing**

All entries are stored in the hash table itself. When a collision occurs, probe for the next available slot (linear probing, quadratic probing, double hashing).

Java's `HashMap` uses **chaining**, NOT open addressing.

> 💡 **Placement Insight:** "What happens when two keys have the same hashCode in Java's HashMap?" → They go to the same bucket. The bucket stores a linked list. When you call `get(key)`, Java goes to the bucket and traverses the list, using `.equals()` to find the exact key. That `.equals()` check is why you must override both `hashCode()` and `equals()` when using custom objects as HashMap keys — a common advanced interview question.

---

## 16. Average-Case vs Worst-Case Complexity

### Concept

| Operation | Average Case | Worst Case |
|---|---|---|
| `put(key, value)` | O(1) | O(n) |
| `get(key)` | O(1) | O(n) |
| `containsKey(key)` | O(1) | O(n) |
| `remove(key)` | O(1) | O(n) |

**Why O(1) average:** With a good hash function and reasonable load factor, collisions are rare. Most buckets have 0–1 elements. Lookups are essentially direct array access.

**Why O(n) worst case:** If all n keys hash to the same bucket (adversarial input, or a terrible hash function), that bucket becomes a list of n elements. Every `get` now scans the entire list.

**Load Factor:** The ratio `(number of entries) / (table size)`. Java's default load factor is **0.75**. When the load factor exceeds 0.75, Java rehashes — doubles the table size and redistributes all entries. This keeps average bucket length low, keeping operations near O(1).

> ⚠️ **Interview Trap:** Saying "HashMap is always O(1)" without qualification will be challenged by a sharp interviewer. The correct answer is: "O(1) average case, O(n) worst case due to collisions. Java's HashMap mitigates this with rehashing and converting long chains to red-black trees (O(log n) worst case per bucket in modern Java)."

---

## 17. Why HashMap Operations Are O(1) on Average

### The Intuition

1. A good hash function distributes n keys across m buckets uniformly.
2. On average, each bucket holds `n/m` keys.
3. With a load factor ≤ 0.75, `n/m ≤ 0.75` — each bucket has fewer than 1 key on average.
4. `get` → hash the key → go to bucket → find the key with at most 1 comparison on average → O(1).

The magic is in step 4: **no searching through the rest of the data**. You go directly to the right bucket.

```
Array access: also O(1) → go to index directly.
HashMap get: O(1) → hash key → go to bucket → done.
```

They're both O(1) for the same fundamental reason: you skip searching.

> 💡 **Placement Insight:** "Why is HashMap O(1) and linear search O(n)?" is a real interview question. The answer: HashMap doesn't search — it *computes where to look* and goes there directly. Linear search has to *check each element* until it finds the target. That's the fundamental difference.

---

## 18. When Array Hashing is Better Than HashMap

### Use a plain `int[]` array when:
1. **Keys are non-negative integers** within a known, small range (≤ 10⁶).
2. **Performance matters** — arrays have lower overhead than HashMap (no hashing computation, no boxing, no object allocation).
3. **Space is predictable** — you know exactly how big the array needs to be.

```java
// Character frequency — always use int[26], not HashMap
int[] freq = new int[26];
for (char c : str.toCharArray()) {
    freq[c - 'a']++;
}
// O(1) per update — faster than HashMap due to no boxing overhead
```

**Why arrays are faster here:**
- No hash computation.
- No boxing (`int` → `Integer` → `int` is wasted work in HashMap).
- Better CPU cache behavior (contiguous memory vs HashMap's scattered allocation).

---

## 19. When HashMap is Necessary

### Use `HashMap` when:
1. **Keys are negative** — arrays can't have negative indices.
2. **Key range is large** — you can't allocate `int[10^9]`.
3. **Keys are non-integer** — strings, custom objects, etc.
4. **Key range is unknown at compile time.**

```java
// Must use HashMap:
// Problem: count frequency of each word in a sentence
HashMap<String, Integer> wordFreq = new HashMap<>();
for (String word : words) {
    wordFreq.put(word, wordFreq.getOrDefault(word, 0) + 1);
}

// Must use HashMap:
// Problem: elements can be negative or up to 10^9
HashMap<Integer, Integer> numFreq = new HashMap<>();
for (int num : arr) {
    numFreq.put(num, numFreq.getOrDefault(num, 0) + 1);
}
```

> ⚠️ **Common Trap:** Creating an array of the wrong size. If the problem says "elements can be up to 10^6", and you make `new int[10^6 + 1]`, that's fine (4MB). But if elements can be up to 10^9, that's 4GB — instant crash. When in doubt about range, default to HashMap.

---

## 20. Space vs Time Trade-Off

### Concept

Hashing is a **space-time trade-off**: you spend extra memory (the hash array or HashMap) to save time (from O(n) per query to O(1) per query).

| Approach | Time (build) | Time (query) | Space |
|---|---|---|---|
| Brute force (nested loop) | — | O(n) per query | O(1) |
| Hash array precomputation | O(n) | O(1) per query | O(range) |
| HashMap precomputation | O(n) | O(1) per query | O(n) |

There is no free lunch: the O(1) query speed always costs O(n) or O(range) extra space.

> 💡 **Placement Insight:** Interviewers who ask you to "optimize" a brute-force O(n²) solution are almost always inviting you to introduce this space-time trade-off. The pattern is: "I'll use extra O(n) space to precompute the information I need, bringing query time from O(n) to O(1)." Say that explicitly — it shows you understand the *trade-off*, not just the *technique*.

---

# Part 2 — Striver Problems

---

## Problem 1 — Basic Hashing

### Problem Statement

Given an array of integers and a list of queries, for each query `q`, find the number of times `q` appears in the array.

**Input:**
- Array: `{1, 3, 2, 1, 3, 1, 4}`
- Queries: `{1, 3, 4, 7, 2}`

**Output:** `3 2 1 0 1`

---

### Example

```
Array:    [1, 3, 2, 1, 3, 1, 4]
Queries:  [1, 3, 4, 7, 2]

For query 1: 1 appears 3 times → 3
For query 3: 3 appears 2 times → 2
For query 4: 4 appears 1 time  → 1
For query 7: 7 appears 0 times → 0
For query 2: 2 appears 1 time  → 1

Output: 3 2 1 0 1
```

---

### Intuition

Without hashing, you'd scan the entire array for every query — O(n) per query.

The key insight: **all queries are asking about the same array**. Why scan it every time? Scan it once, store what you learned, and answer every query in O(1).

This is the precomputation idea made concrete.

---

### Brute Force Approach

For each query, scan the entire array and count occurrences.

```java
public static int countOccurrences(int[] arr, int target) {
    int count = 0;
    for (int num : arr) {
        if (num == target) count++;
    }
    return count;
}

// For each query: call countOccurrences → O(n) per query
```

**Time Complexity:** O(n × q) where n = array size, q = number of queries.
**Space Complexity:** O(1).
**Problem:** For n = 10⁵ and q = 10⁵, this is 10¹⁰ operations — way too slow.

---

### Optimized Approach — Hashing

**Strategy:** Precompute a frequency map. Answer every query in O(1).

**Step-by-Step:**
1. Find the maximum value in the array to size the hash array.
2. Build a frequency array: for each element `arr[i]`, do `freq[arr[i]]++`.
3. For each query `q`, return `freq[q]`.

---

### Java Solution — Using int[] Array (When Range is Small)

```java
import java.util.Scanner;

public class BasicHashing {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int[] arr = new int[n];

        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }

        // Step 1: Find the maximum element to determine hash array size
        int maxVal = 0;
        for (int num : arr) {
            maxVal = Math.max(maxVal, num);
        }

        // Step 2: Build the frequency (hash) array
        int[] freq = new int[maxVal + 1];   // index = value, content = count
        for (int num : arr) {
            freq[num]++;   // the value itself IS the hash index
        }

        // Step 3: Answer queries in O(1) each
        int q = sc.nextInt();
        while (q-- > 0) {
            int query = sc.nextInt();
            // Bounds check: query might be larger than any element in array
            if (query > maxVal) {
                System.out.println(0);
            } else {
                System.out.println(freq[query]);
            }
        }
    }
}
```

---

### Java Solution — Using HashMap (General Purpose)

```java
import java.util.*;

public class BasicHashingMap {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }

        // Build frequency map — works for any integer values
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : arr) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
            //             ↑ if key doesn't exist, start from 0
        }

        // Answer queries
        int q = sc.nextInt();
        while (q-- > 0) {
            int query = sc.nextInt();
            System.out.println(freq.getOrDefault(query, 0));
            //                      ↑ returns 0 if query was never in array
        }
    }
}
```

---

### Dry Run

Array: `[1, 3, 2, 1, 3, 1, 4]`

**Build phase (HashMap):**

| Element Processed | Map After Update |
|---|---|
| 1 | `{1→1}` |
| 3 | `{1→1, 3→1}` |
| 2 | `{1→1, 3→1, 2→1}` |
| 1 | `{1→2, 3→1, 2→1}` |
| 3 | `{1→2, 3→2, 2→1}` |
| 1 | `{1→3, 3→2, 2→1}` |
| 4 | `{1→3, 3→2, 2→1, 4→1}` |

**Query phase:**

| Query | `freq.getOrDefault(q, 0)` | Output |
|---|---|---|
| 1 | 3 | 3 |
| 3 | 2 | 2 |
| 4 | 1 | 1 |
| 7 | 0 (not in map) | 0 |
| 2 | 1 | 1 |

---

### Time & Space Complexity

| | Array Hashing | HashMap |
|---|---|---|
| **Build time** | O(n) | O(n) |
| **Query time** | O(1) per query | O(1) average per query |
| **Space** | O(max value in array) | O(distinct elements) |

---

### Common Mistakes

| Mistake | Why It's Wrong | Fix |
|---|---|---|
| Scanning array for every query | O(n × q) — too slow | Precompute once, query in O(1) |
| Using `freq[query]` without bounds check | ArrayIndexOutOfBoundsException if query > max | Use `if (query <= maxVal)` or use HashMap |
| Allocating `int[10^9]` for large values | ~4GB memory → crash | Use HashMap for large/unknown ranges |
| Confusing key and value in HashMap | Silent wrong output | Key = element, Value = frequency |

---

### Interview Notes

> 💡 **Placement Insight:** This is the canonical "explain hashing" problem. An interviewer who gives you this problem is checking: (1) do you know to precompute instead of scan every time? (2) do you know the difference between array hashing and HashMap? (3) can you handle edge cases like queries outside the array's value range? Cover all three in your explanation.

---

### Key Takeaways

- Precompute frequency once → answer all queries in O(1).
- Array hashing: use when values are small, non-negative, and range is known.
- HashMap: use when values can be large, negative, or range is unknown.
- Always guard against out-of-bounds queries when using array hashing.

---

## Problem 2 — Counting Frequencies of Array Elements

### Problem Statement

Given an array of integers, print every distinct element along with its frequency (how many times it appears). Print in the order elements first appear.

**Input:** `[10, 5, 10, 15, 10, 5]`

**Output:**
```
10 3
5 2
15 1
```

---

### Example

```
Input:  [10, 5, 10, 15, 10, 5]

Element 10 appears 3 times
Element 5  appears 2 times
Element 15 appears 1 time

Output:
10 3
5  2
15 1
```

---

### Intuition

This is frequency counting taken to its logical conclusion: count every element, then report the counts.

The challenge is printing in **first-appearance order**. A plain `HashMap` in Java doesn't guarantee insertion order. For this, use `LinkedHashMap` (preserves insertion order) or track insertion order separately with a list.

---

### Brute Force Approach

For every element in the array, count its occurrences by scanning the array — but skip elements already processed.

```java
// Brute force: for each unprocessed element, scan full array
public static void bruteForce(int[] arr) {
    boolean[] visited = new boolean[arr.length];

    for (int i = 0; i < arr.length; i++) {
        if (visited[i]) continue;  // already counted

        int count = 1;
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] == arr[i]) {
                count++;
                visited[j] = true;  // mark to avoid re-counting
            }
        }
        System.out.println(arr[i] + " " + count);
    }
}
```

**Time Complexity:** O(n²) — for each element, we scan the rest of the array.
**Space Complexity:** O(n) — the `visited` array.

---

### Optimized Approach — HashMap

**Strategy:** Build a frequency map in one pass, then print in a second pass.

**Step-by-Step:**
1. Traverse the array. For each element, add it to a `HashMap` with its count.
2. To preserve order, also maintain a list of elements in their first-appearance order.
3. Traverse the order list, print each element and its count from the map.

---

### Java Solution

```java
import java.util.*;

public class CountingFrequencies {

    public static void main(String[] args) {
        int[] arr = {10, 5, 10, 15, 10, 5};

        // HashMap to store frequency
        HashMap<Integer, Integer> freq = new HashMap<>();

        // List to preserve first-appearance order
        List<Integer> order = new ArrayList<>();

        // Step 1: Build frequency map in one pass
        for (int num : arr) {
            if (!freq.containsKey(num)) {
                // First time seeing this element
                order.add(num);   // track insertion order
                freq.put(num, 1);
            } else {
                // Seen before — increment count
                freq.put(num, freq.get(num) + 1);
            }
        }

        // Step 2: Print in first-appearance order
        for (int num : order) {
            System.out.println(num + " " + freq.get(num));
        }
    }
}
```

**Alternative — Using LinkedHashMap (cleaner, same result):**

```java
import java.util.*;

public class CountingFrequenciesLinked {

    public static void main(String[] args) {
        int[] arr = {10, 5, 10, 15, 10, 5};

        // LinkedHashMap preserves insertion order automatically
        LinkedHashMap<Integer, Integer> freq = new LinkedHashMap<>();

        for (int num : arr) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
            //             ↑ inserts with count 1 on first occurrence
            //               LinkedHashMap remembers insertion order
        }

        // Print: iteration order = insertion order
        for (Map.Entry<Integer, Integer> entry : freq.entrySet()) {
            System.out.println(entry.getKey() + " " + entry.getValue());
        }
    }
}
```

---

### Step-by-Step Explanation

```java
LinkedHashMap<Integer, Integer> freq = new LinkedHashMap<>();
```
LinkedHashMap is a HashMap that also maintains a doubly-linked list of entries in insertion order. Iteration always follows the order keys were first inserted.

```java
freq.put(num, freq.getOrDefault(num, 0) + 1);
```
- First time `num` appears: `getOrDefault(num, 0)` returns 0, so we store `0 + 1 = 1`.
- Second time `num` appears: `getOrDefault(num, 0)` returns 1, so we store `1 + 1 = 2`.
- The key is only "inserted" once (on first occurrence) — subsequent `put`s update the value but don't change the key's position in the linked list.

```java
for (Map.Entry<Integer, Integer> entry : freq.entrySet()) {
    System.out.println(entry.getKey() + " " + entry.getValue());
}
```
`entrySet()` returns all key-value pairs. `entry.getKey()` is the element, `entry.getValue()` is its frequency.

---

### Dry Run

Array: `[10, 5, 10, 15, 10, 5]`

**Build pass:**

| i | arr[i] | freq before | Action | freq after |
|---|---|---|---|---|
| 0 | 10 | `{}` | First time: insert | `{10→1}` |
| 1 | 5 | `{10→1}` | First time: insert | `{10→1, 5→1}` |
| 2 | 10 | `{10→1, 5→1}` | Seen: increment | `{10→2, 5→1}` |
| 3 | 15 | `{10→2, 5→1}` | First time: insert | `{10→2, 5→1, 15→1}` |
| 4 | 10 | `{10→2, 5→1, 15→1}` | Seen: increment | `{10→3, 5→1, 15→1}` |
| 5 | 5 | `{10→3, 5→1, 15→1}` | Seen: increment | `{10→3, 5→2, 15→1}` |

**Print pass:** (LinkedHashMap preserves insertion order: 10, 5, 15)
```
10 3
5 2
15 1
```

---

### Time & Space Complexity

| | Brute Force | HashMap |
|---|---|---|
| **Time** | O(n²) | O(n) |
| **Space** | O(n) — visited array | O(n) — frequency map |

---

### Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Using `HashMap` and expecting sorted/insertion order | `HashMap` has no guaranteed iteration order | Use `LinkedHashMap` for insertion order, `TreeMap` for sorted order |
| Not handling the case where an element appears only once | Works fine — `getOrDefault` starts at 0 | No fix needed — but know why it works correctly |
| Iterating over `arr` again to print (risk of duplicate prints) | Would print element 10 three times | Iterate over the key set / order list, not the original array |

---

### Interview Notes

> 💡 **Placement Insight:** This problem specifically tests whether you know the difference between `HashMap`, `LinkedHashMap`, and `TreeMap`. If a problem says "print in original order," that's their signal for `LinkedHashMap` or a separate order list. If it says "print in sorted order," that's `TreeMap`. If order doesn't matter, use `HashMap` for best performance.

---

### Key Takeaways

- Use `LinkedHashMap` when you need frequency + first-appearance order.
- `freq.getOrDefault(num, 0) + 1` is the clean one-liner for frequency building.
- Use `entrySet()` to iterate over both keys and values together.
- Brute force is O(n²). HashMap is O(n). Always state this comparison explicitly.

---

## Problem 3 — Highest Occurring Element in an Array

### Problem Statement

Given an array of integers, find the element that appears the **maximum number of times**.

If multiple elements have the same maximum frequency, return the one that appears **first** in the array (or any one of them, depending on the problem's constraint — read it carefully).

**Input:** `[1, 1, 1, 2, 2, 3]`
**Output:** `1` (appears 3 times, which is the highest frequency)

---

### Example

```
Input:  [1, 1, 1, 2, 2, 3]

Frequencies:
  1 → 3
  2 → 2
  3 → 1

Maximum frequency is 3 → element 1

Output: 1
```

---

### Intuition

Step 1: Build a frequency map. (Identical to Problem 2.)
Step 2: Scan the map to find the key with the maximum value.

This is a two-step problem — precompute frequencies, then find the max.

---

### Brute Force Approach

For every element, count its occurrences by scanning the array. Track the element with the highest count.

```java
public static int highestOccurringBrute(int[] arr) {
    int maxCount = 0;
    int result = arr[0];

    for (int i = 0; i < arr.length; i++) {
        int count = 0;
        for (int j = 0; j < arr.length; j++) {   // O(n) per element
            if (arr[j] == arr[i]) count++;
        }
        if (count > maxCount) {
            maxCount = count;
            result = arr[i];
        }
    }
    return result;
}
```

**Time Complexity:** O(n²).
**Space Complexity:** O(1).

---

### Optimized Approach — Hashing

**Step-by-Step:**
1. Build a frequency map in one pass: O(n).
2. Scan the map to find the key with the maximum value: O(n).
3. Total: O(n).

---

### Java Solution

```java
import java.util.*;

public class HighestOccurring {

    public static void main(String[] args) {
        int[] arr = {1, 1, 1, 2, 2, 3};

        // Step 1: Build frequency map
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : arr) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        // Step 2: Find the element with maximum frequency
        int maxFreq = 0;
        int result = -1;

        for (Map.Entry<Integer, Integer> entry : freq.entrySet()) {
            if (entry.getValue() > maxFreq) {
                maxFreq = entry.getValue();
                result = entry.getKey();
            }
        }

        System.out.println("Highest occurring element: " + result);
        System.out.println("Frequency: " + maxFreq);
    }
}
```

**Alternative — Combined Single Pass (Track Max as You Build):**

```java
import java.util.*;

public class HighestOccurringSinglePass {

    public static void main(String[] args) {
        int[] arr = {1, 1, 1, 2, 2, 3};

        HashMap<Integer, Integer> freq = new HashMap<>();
        int maxFreq = 0;
        int result = arr[0];

        for (int num : arr) {
            // Update frequency
            int currentFreq = freq.getOrDefault(num, 0) + 1;
            freq.put(num, currentFreq);

            // Update max as we go
            if (currentFreq > maxFreq) {
                maxFreq = currentFreq;
                result = num;
            }
        }

        System.out.println("Highest occurring element: " + result);
        System.out.println("Frequency: " + maxFreq);
    }
}
```

---

### Step-by-Step Explanation

```java
int currentFreq = freq.getOrDefault(num, 0) + 1;
freq.put(num, currentFreq);
```
Compute the new frequency for `num` and store it. This is the standard increment pattern.

```java
if (currentFreq > maxFreq) {
    maxFreq = currentFreq;
    result = num;
}
```
If this element's new frequency beats the current maximum, update both `maxFreq` and `result`. By using strict `>` (not `>=`), ties are broken in favor of the **first element to reach that frequency** — which is usually the first-appearing element in the array.

---

### Dry Run

Array: `[1, 1, 1, 2, 2, 3]`

**Combined single-pass trace:**

| num | `getOrDefault(num,0)` | `currentFreq` | freq map (after) | `currentFreq > maxFreq`? | maxFreq | result |
|---|---|---|---|---|---|---|
| 1 | 0 | 1 | `{1→1}` | 1 > 0 → Yes | 1 | 1 |
| 1 | 1 | 2 | `{1→2}` | 2 > 1 → Yes | 2 | 1 |
| 1 | 2 | 3 | `{1→3}` | 3 > 2 → Yes | 3 | 1 |
| 2 | 0 | 1 | `{1→3, 2→1}` | 1 > 3 → No | 3 | 1 |
| 2 | 1 | 2 | `{1→3, 2→2}` | 2 > 3 → No | 3 | 1 |
| 3 | 0 | 1 | `{1→3, 2→2, 3→1}` | 1 > 3 → No | 3 | 1 |

**Final answer:** Element `1`, frequency `3`. ✅

---

### What If There's a Tie?

```
Input: [3, 3, 2, 2, 1]
Frequencies: {3→2, 2→2, 1→1}
```

Both 3 and 2 appear twice. With the single-pass approach using strict `>`, 3 wins because it was the first to reach frequency 2. If the problem says "return the smallest element in case of a tie," switch to `TreeMap` or post-process the results.

> ⚠️ **Interview Trap:** Always ask the interviewer: "What if multiple elements share the maximum frequency? Which one should I return?" Interviewers include ties deliberately to see if you read the problem carefully. If it's an online judge and the question says "any valid answer is accepted," use strict `>`. If order matters, clarify before coding.

---

### Time & Space Complexity

| | Brute Force | Hashing (Two-Pass) | Hashing (Single-Pass) |
|---|---|---|---|
| **Time** | O(n²) | O(n) | O(n) |
| **Space** | O(1) | O(n) | O(n) |

---

### Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Initializing `maxFreq = Integer.MIN_VALUE` | Works, but unnecessary — frequency is always ≥ 1 | Initialize `maxFreq = 0` |
| Using `>=` instead of `>` for max tracking | Changes which element wins in tie-breaking | Use `>` (first-found) unless problem specifies otherwise |
| Returning just the frequency, not the element | The problem asks for the element | Return `result`, not `maxFreq` |
| Not handling empty array | `arr[0]` crashes on empty input | Check `if (arr.length == 0) return -1;` first |

---

### Interview Notes

> 💡 **Placement Insight:** This problem appears in multiple disguises: "most frequent character in a string," "most repeated word in a sentence," "mode of an array." The underlying algorithm is always the same: build a frequency map, find the max value in the map, return the key. Recognizing this pattern across different problem phrasings is what placement-ready thinking looks like.

---

### Key Takeaways

- Build frequency map: O(n). Find max in map: O(n). Total: O(n).
- Single-pass approach (update max as you build the map) saves one iteration.
- Tie-breaking strategy depends on the problem — always clarify.
- This pattern appears everywhere: most common character, most repeated word, mode of an array.

---

# Part 3 — Cheat Sheet & Revision

---

## How Hashing Connects to Future DSA Problems

You now understand the core mechanics of hashing. Here's exactly where it appears in the problems ahead — understanding the *connection* now makes those problems much less intimidating when you encounter them.

### Two Sum
Hashing turns the O(n²) "check every pair" approach into O(n). You store each element in a `HashSet` or `HashMap` as you go, and for each new element `x`, check if `target - x` already exists in the map. The entire technique is built on O(1) HashMap lookup.

### Frequency Maps
Any problem asking about "how many times," "most common," "elements appearing more than k times" is a frequency map problem — exactly what you practiced in Problems 2 and 3.

### Sliding Window
Many sliding window problems (longest substring with at most k distinct characters, minimum window substring) use a `HashMap` to maintain a frequency count of elements in the current window. As the window slides, you update frequencies and query the map in O(1).

### Longest Substring Problems
"Longest substring with all unique characters," "longest substring with no repeated characters" — these use a `HashSet` to track what's currently in the window. Existence checks in O(1) make the sliding window fast.

### Longest Consecutive Sequence
You store all elements in a `HashSet`. For each element, check if `element - 1` exists (O(1)). If not, this element is the start of a sequence — extend it by checking `element + 1`, `element + 2`, etc. Without a HashSet, this would be O(n²).

### HashSet-Based Problems
"Find duplicates," "find the intersection of two arrays," "check if an array is a permutation of another" — all solved by building a `HashSet` and querying membership in O(1).

### Graph Visited Sets
In BFS and DFS graph traversal, you use a `HashSet<Integer>` (or `boolean[] visited` array) to track which nodes have been visited. Without O(1) membership check, traversal becomes O(n²).

### Memoization / Dynamic Programming
Memoization stores the result of each subproblem in a `HashMap<parameters, result>`. When you encounter the same subproblem again, you look it up in O(1) instead of recomputing it. This turns exponential recursion into polynomial DP — the entire technique depends on O(1) HashMap access.

> 💡 **Placement Insight:** Hashing is not one topic — it's the infrastructure that makes O(n) solutions possible across virtually every DSA category. Every time you solve a Two Sum, a graph traversal, a sliding window problem, or a DP problem, you are using what you learned here. This is why Striver places hashing early in the A2Z sheet.

---

## Hashing Patterns Learned

| # | Pattern | When to Apply |
|---|---|---|
| 1 | **Frequency counting with int[] array** | Small, non-negative integer values (range ≤ 10⁶) |
| 2 | **Character hashing with int[26]** | Lowercase letter frequency in strings |
| 3 | **Frequency counting with HashMap** | Large values, negative values, or unknown range |
| 4 | **Precompute + query** | Multiple queries on the same dataset |
| 5 | **Find max frequency element** | Mode of an array; most common word/character |
| 6 | **Existence check with HashSet** | "Does X exist?", "Find duplicates", "Intersection" |
| 7 | **LinkedHashMap for ordered frequency** | Print elements in first-appearance order with counts |

---

## Hashing Cheat Sheet

```
Problem type                         → Recommended structure
─────────────────────────────────────────────────────────────
Count element frequency              → HashMap<Integer, Integer>
Count character frequency            → int[26] or int[128]
Check if element exists              → HashSet<Integer>
Most frequent element                → HashMap + max tracking
Print freq in original order         → LinkedHashMap<Integer, Integer>
Print freq in sorted order           → TreeMap<Integer, Integer>
Two Sum / pair problems              → HashMap<Integer, Integer>
Duplicates in array                  → HashSet<Integer>
Large / negative key range           → HashMap (never int[])
Small, non-negative key range        → int[] (faster than HashMap)
```

---

## Important Java HashMap / HashSet Methods

### HashMap<K, V> — Methods You Must Know

```java
HashMap<Integer, Integer> map = new HashMap<>();

// INSERT / UPDATE
map.put(key, value);
// → If key exists, overwrites value. If not, creates new entry.

// READ
map.get(key);
// → Returns value, or null if key doesn't exist. Can cause NullPointerException.

map.getOrDefault(key, defaultValue);
// → Returns value, or defaultValue if key doesn't exist. SAFE — no null issues.
// → Most important method for frequency counting.

// CHECK
map.containsKey(key);
// → Returns true if key exists, false otherwise.

map.containsValue(value);
// → Returns true if any key maps to this value. O(n) — rarely used.

// DELETE
map.remove(key);
// → Removes key-value pair. Returns the removed value (or null).

// SIZE
map.size();
// → Number of key-value pairs.

map.isEmpty();
// → Returns true if size is 0.

// ITERATE
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    int k = entry.getKey();
    int v = entry.getValue();
}

for (int k : map.keySet()) { /* iterate keys */ }
for (int v : map.values()) { /* iterate values */ }
```

### HashSet<E> — Methods You Must Know

```java
HashSet<Integer> set = new HashSet<>();

// INSERT
set.add(element);
// → Returns true if added (first time), false if already present (duplicate ignored).

// CHECK
set.contains(element);
// → Returns true if element exists. O(1) average case.

// DELETE
set.remove(element);
// → Removes element. Returns true if found and removed.

// SIZE
set.size();

set.isEmpty();

// ITERATE
for (int e : set) { /* no guaranteed order */ }

// CONVERT ARRAY TO SET (remove duplicates)
int[] arr = {1, 2, 2, 3};
HashSet<Integer> set2 = new HashSet<>();
for (int num : arr) set2.add(num);
// set2 = {1, 2, 3}
```

---

## Common Interview Questions

| Question | Key Answer Points |
|---|---|
| What is hashing? | Mapping keys to indices using a hash function for O(1) access |
| How does HashMap work internally? | Hash function → bucket index → chaining for collisions |
| What is a collision? | Two different keys mapping to the same hash index |
| How does Java resolve collisions? | Chaining (linked list, or red-black tree for large chains in Java 8+) |
| Is HashMap always O(1)? | No — O(1) average, O(n) worst case. O(log n) worst case per bucket in Java 8+ |
| HashMap vs HashSet? | Map stores K-V pairs; Set stores only keys (for existence checks) |
| HashMap vs TreeMap? | HashMap: O(1) unordered; TreeMap: O(log n) sorted by key |
| HashMap vs LinkedHashMap? | HashMap: unordered; LinkedHashMap: insertion-order preserved |
| When to use array hashing vs HashMap? | Array: small non-negative integer range; HashMap: everything else |
| What is the default load factor of HashMap? | 0.75 — triggers rehashing when exceeded |
| What is getOrDefault()? | Returns value for key, or a default if key not found — avoids null checks |
| What happens if you use a mutable object as a key? | hashCode may change → key becomes unreachable — use immutable keys |

---

## Common Mistakes

| Mistake | Why It Happens | How to Fix It |
|---|---|---|
| Saying "HashMap is always O(1)" | Memorized the average case | Qualify: "O(1) average, O(n) worst case" |
| Using `int[]` for large or negative values | Range seems small until it isn't | Check constraints first; default to HashMap for unknown ranges |
| Using `freq.get(key)` without null check | Trusting the key exists when it might not | Use `getOrDefault(key, 0)` instead |
| Confusing key and value | Conceptually reversed | Key = what you search by. Value = what you find. |
| Using HashMap when HashSet suffices | Overusing the more complex structure | If you never use the value, use HashSet |
| Ignoring collision possibility | Never thought about it | Understand that it exists; know chaining handles it |
| Ignoring space complexity | Counting only time | Every hash structure uses O(n) or O(range) space — state this |
| Not handling tie-breaking | Forgetting edge cases | Ask/check the problem for what to do when frequencies are equal |
| Iterating HashMap and expecting sorted order | HashMap has no order | Use TreeMap for sorted, LinkedHashMap for insertion order |

---

## Final Revision Notes

1. **Hashing = key → index using a function**. The goal is O(1) access.
2. **Two implementations for placement**: `int[]` array (small range) and `HashMap` (general case).
3. **HashMap operations**: O(1) average, O(n) worst case (due to collisions). Java 8+: O(log n) worst case per bucket.
4. **HashSet = HashMap with no values**. Use it for existence checks, deduplication.
5. **Frequency pattern**: `map.put(k, map.getOrDefault(k, 0) + 1)` — know this cold.
6. **Precompute once, query many times** — the core trade-off hashing enables.
7. **Space cost**: always O(n) or O(range) — never free. Mention this when discussing complexity.
8. **LinkedHashMap** preserves insertion order. **TreeMap** maintains sorted order. **HashMap** has no guaranteed order.
9. **Character hashing**: `int[26]` with `c - 'a'` is faster than `HashMap<Character, Integer>` for string problems.
10. Hashing underpins Two Sum, Sliding Window, Graph BFS/DFS visited sets, and Memoization — it's not a standalone topic.

---

## Revision Checklist

**Theory**
- [ ] I can explain what hashing is and why it's useful without looking at notes.
- [ ] I can explain the difference between hashing (concept) and HashMap (implementation).
- [ ] I can explain what a hash function does.
- [ ] I can explain what a collision is and how Java's HashMap handles it.
- [ ] I can explain why HashMap is O(1) average case but O(n) worst case.
- [ ] I know when to use `int[]` array hashing vs `HashMap`.
- [ ] I know when negative values or large ranges force me to use `HashMap`.

**Java Methods**
- [ ] I can write `freq.put(k, freq.getOrDefault(k, 0) + 1)` from memory.
- [ ] I know the difference between `map.get(k)` and `map.getOrDefault(k, 0)`.
- [ ] I can iterate over a HashMap using `entrySet()`.
- [ ] I know `set.add()`, `set.contains()`, and `set.remove()`.
- [ ] I know `LinkedHashMap` preserves insertion order.

**Problems**
- [ ] I can implement Basic Hashing with both `int[]` and `HashMap` from memory.
- [ ] I can implement Counting Frequencies in O(n) time using `LinkedHashMap`.
- [ ] I can find the highest occurring element in O(n) time.
- [ ] I can do a complete dry run of frequency building on a small array without making mistakes.

**Interview Readiness**
- [ ] I won't say "HashMap is always O(1)" — I'll say "O(1) average, O(n) worst case."
- [ ] I know to ask about tie-breaking before coding the highest-frequency problem.
- [ ] I can explain the space-time trade-off when introducing a HashMap in any solution.
- [ ] I can name at least 3 future problems where hashing is the key technique.

---

*Part of [Placement-Prep-2026](https://github.com/Harshaghera111/Placement-Prep-2026) — Hashing series.*
*File 6 of the DSA section | Follows: 5_Recursion.md | Covers: Striver A2Z — Learn Basic Hashing*
