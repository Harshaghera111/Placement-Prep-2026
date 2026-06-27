# Arrays — Placement Edition
### Notes from a "been-there, interviewed-that" professor's lens

---

## A Word Before We Start

If Basic Programming was the alphabet, Arrays are your first real word in DSA. This is where "I can write a `for` loop" turns into "I can reason about memory, indices, and complexity at the same time" — and where most fresher interviews actually begin.

Same format as before: **What it is → Why it bites you → How to not embarrass yourself.**

---

## 1. What is an Array?

### Concept
A contiguous block of memory storing elements of the **same type**, accessed via an index.

```java
int[] arr = new int[5];       // declaration + memory allocation
int[] arr2 = {10, 20, 30};    // declaration + initialization
```

### Professor's Note — "Contiguous" is the Whole Point
The reason `arr[i]` is O(1) isn't magic — it's because the array's memory block is contiguous, so the address of any element can be computed directly:

```
address(arr[i]) = base_address + i * size_of(element)
```

**Interview trap:** "Why is array access O(1) but linked list access O(n)?" — if you can't explain it via this address-arithmetic logic, you've memorized the answer, not understood it. Say this out loud once; it never leaves you again.

### Memory Tip
Array = fixed-size, same-type, contiguous, index-based. Index starts at **0**.

---

## 2. Declaration, Initialization & Default Values

### Syntax
```java
int[] arr = new int[5];      // all elements default to 0
boolean[] flags = new boolean[3]; // all default to false
String[] names = new String[3];   // all default to null
```

### Professor's Note — Default Values Are an Interview Favorite
```java
int[] arr = new int[5];
System.out.println(arr[0]); // 0, NOT garbage like in C
```

**Interview trap:** "What's in an uninitialized Java array?" → Java auto-initializes (0 / false / null), unlike C/C++ where it's garbage memory. Confusing this with your C background is one of the most common slip-ups for students transitioning languages.

### Memory Tip
`int/long/double` → 0. `boolean` → false. Objects (`String`, custom classes) → `null`.

---

## 3. Traversal

### Syntax
```java
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}

for (int val : arr) {   // for-each, when index isn't needed
    System.out.println(val);
}
```

### Professor's Note — `.length` vs `.length()`
This trips up almost every fresher at least once:
- Array → `arr.length` (property, no parentheses)
- String → `str.length()` (method, parentheses)

**Interview trap:** A live-coding round where you write `arr.length()` out of muscle memory from `String` — small, but interviewers absolutely notice these slips since they signal how much "real" coding you've actually done.

Also: for-each loops are great for readability but **you lose the index** — if your logic needs `i` (e.g., comparing neighbors), use the classic `for` loop.

### Complexity
Traversal: O(n) always — there's no way around touching every element once.

### Memory Tip
Array → `.length`. String → `.length()`. Need index → classic `for`. Just values → for-each.

---

## 4. Insertion & Deletion

### Concept
Arrays are **fixed-size** — you can't actually "insert" or "delete" in the traditional sense; you simulate it by shifting elements.

```java
// Insert x at index pos (assuming space exists at the end)
for (int i = n; i > pos; i--) {
    arr[i] = arr[i - 1];
}
arr[pos] = x;

// Delete element at index pos
for (int i = pos; i < n - 1; i++) {
    arr[i] = arr[i + 1];
}
```

### Professor's Note — The Question Behind the Question
**Interview trap:** "What's the time complexity of inserting at the beginning of an array?" → O(n), because you shift every existing element. This is the exact reason `ArrayList`/`LinkedList` exist, and interviewers often follow up with: "So why would you ever use an array over a `LinkedList`?" — the honest answer is **cache locality and O(1) random access**, which `LinkedList` sacrifices for flexible insertion/deletion. Knowing *why* you'd choose one over the other matters more than knowing both exist.

### Complexity
- Insert/delete at end: O(1) amortized (if space available)
- Insert/delete at beginning or middle: O(n) — shifting cost

### Memory Tip
Fixed size → insertion/deletion means **shifting**, not "making room." End = cheap. Beginning/middle = expensive.

---

## 5. Searching

### Linear Search
```java
for (int i = 0; i < arr.length; i++) {
    if (arr[i] == target) return i;
}
return -1;
```
Time Complexity: **O(n)** — works on unsorted data.

### Binary Search
```java
int low = 0, high = arr.length - 1;
while (low <= high) {
    int mid = low + (high - low) / 2;
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) low = mid + 1;
    else high = mid - 1;
}
return -1;
```
Time Complexity: **O(log n)** — requires **sorted** data.

### Professor's Note — The Mid Calculation Bug That Fails Silently
```java
int mid = (low + high) / 2;        // ⚠️ overflow risk if low+high exceeds int range
int mid = low + (high - low) / 2;  // ✅ safe version
```
**Interview trap:** This exact overflow bug was a real, documented bug in the Java standard library's binary search for years. If you write the "naive" version in an interview, a sharp interviewer will ask "are you sure this is safe for large arrays?" — and you want to already know why.

Also: always clarify out loud — "Is the array sorted?" — before jumping to binary search. Assuming sorted data without checking is a classic rushed-candidate mistake.

### Memory Tip
Unsorted → linear search, O(n). Sorted → binary search, O(log n). Mid → always `low + (high-low)/2`, never `(low+high)/2`.

---

## 6. Sorting (Conceptual Overview)

You'll cover sorting algorithms in depth separately, but at the Array stage, know this much cold:

| Algorithm | Time (avg) | Space | Stable? |
|---|---|---|---|
| Bubble Sort | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(1) | No |
| Insertion Sort | O(n²) (O(n) best case) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) avg, O(n²) worst | O(log n) | No |
| `Arrays.sort()` (primitives) | O(n log n), Dual-Pivot Quicksort | O(log n) | No |
| `Arrays.sort()` (objects) | O(n log n), TimSort | O(n) | Yes |

### Professor's Note — Why Java's `Arrays.sort()` Behaves Differently for Objects
**Interview trap:** "Is `Arrays.sort()` stable?" — Trick question! It depends on what you're sorting. For primitives, Java uses Dual-Pivot Quicksort (not stable). For objects, it uses TimSort (stable, because object comparisons may rely on `.equals()`/`compareTo()` semantics where order matters, e.g., sorting a list of `Employee` objects by department but wanting name-order preserved within each department).

### Memory Tip
n² sorts → simple, but slow. n log n sorts → the real interview answer. Stability matters when sorting by multiple keys.

---

## 7. Multi-dimensional Arrays

### Concept
An "array of arrays" — Java doesn't have true 2D memory blocks like C; it's an array where each element is itself a reference to another array.

```java
int[][] matrix = new int[3][4]; // 3 rows, 4 columns
matrix[1][2] = 10;

int[][] jagged = new int[3][]; // jagged array — rows of different lengths
jagged[0] = new int[2];
jagged[1] = new int[5];
```

### Professor's Note — This is Where "Reference" Knowledge Pays Off
Because Java 2D arrays are arrays-of-array-references, you can have **jagged arrays** (rows of unequal length) — something C's true 2D arrays can't do natively. This directly connects back to the stack/heap reference concept from your Basic Programming notes — everything reinforces everything.

**Interview trap:** "Traverse a 2D matrix in spiral order / diagonal order" — extremely common fresher question. The trick is always about carefully managing 4 boundary pointers (top, bottom, left, right) — practice this pattern specifically, it recurs constantly.

### Complexity
Traversal: O(rows × cols) — essentially O(n) over total elements, but always state it in terms of both dimensions to show you're thinking about it correctly.

### Memory Tip
2D array in Java = array of array-references, not one solid memory block → enables jagged arrays.

---

## 8. Common Array Patterns You MUST Recognize Instantly

Just like the four loop patterns from Basic Programming, these patterns cover a disproportionate share of fresher array questions:

1. **Two-pointer** (sorted array, pair-sum, reversing in-place)
2. **Sliding window** (max/min sum subarray of size k, longest substring problems)
3. **Prefix sum** (range sum queries in O(1) after O(n) preprocessing)
4. **Kadane's Algorithm** (maximum subarray sum, O(n))
5. **Hashing for frequency/lookup** (turns O(n²) brute force into O(n) — e.g., two-sum)

### Professor's Note — The Two-Sum Reflex
If you ever catch yourself writing nested loops for a "find a pair that..." problem, **stop** — that's almost always your cue to reach for a `HashMap` or sort-then-two-pointer instead. This single reflex shift (brute force → hash-based) is one of the most visible signals of growth interviewers look for between a beginner and a placement-ready candidate.

### Memory Tip
Nested loop on array → ask "can I trade space for time with a HashMap instead?"

---

## Quick Revision Cheatsheet

| Need | Tool / Complexity |
|---|---|
| Access by index | O(1) — contiguous memory math |
| Traverse | O(n), always |
| Search (unsorted) | Linear, O(n) |
| Search (sorted) | Binary, O(log n) |
| Insert/delete at end | O(1) amortized |
| Insert/delete at start/middle | O(n) — shifting |
| Default values | 0 / false / null |
| `.length` vs `.length()` | Array vs String |
| Pair-sum style problems | HashMap, O(n) — not nested loops |
| Max subarray sum | Kadane's Algorithm, O(n) |
| Range sum queries | Prefix sum, O(1) after O(n) prep |

---

## Interview Quick-Fire Notes

1. Array access is O(1) because of direct address computation — know the formula.
2. Java arrays auto-initialize to 0/false/null — no garbage values like C.
3. `arr.length` (no parentheses) vs `str.length()` (method) — don't mix these up live.
4. Insertion/deletion at the **start** costs O(n) due to shifting — this is *why* `LinkedList` exists.
5. Binary search requires sorted data — always say this out loud before using it.
6. Mid calculation: `low + (high-low)/2`, never `(low+high)/2` — overflow safety.
7. `Arrays.sort()` stability depends on type: primitives (unstable, dual-pivot quicksort) vs objects (stable, TimSort).
8. 2D arrays in Java are arrays-of-references → jagged arrays are possible.
9. Nested loop on an array screaming "find a pair/combo" → think HashMap first.

---

## Problems Solved — Worked Through Properly

### ✅ Find Maximum/Minimum in Array
**Pattern used:** single-pass traversal
**Key learnings:**
- Initialize `max` with `arr[0]`, **not** `0` or `Integer.MIN_VALUE` arbitrarily without reason — if all elements are negative, initializing with `0` silently gives a wrong answer.
- **Edge case interviewers always probe:** empty array or single-element array — does your code crash or silently return wrong output?

### ✅ Reverse an Array In-Place
**Pattern used:** two-pointer
**Key learnings:**
- Swap `arr[left]` and `arr[right]`, then `left++`, `right--`, until `left >= right`.
- **Edge case interviewers always probe:** odd-length array — the middle element shouldn't be touched; your loop condition (`left < right`, not `left <= right`) should naturally handle this. If you use `<=`, trace through it once to convince yourself it still works (it does, since swapping an element with itself is harmless) — but knowing *why* matters more than getting lucky.

### ✅ Find the "Missing Number" in 1 to N
**Pattern used:** sum formula / XOR trick
**Key learnings:**
- Expected sum = `n*(n+1)/2`, actual sum = sum of given array, missing = difference.
- **Interview trap:** for large `n`, `n*(n+1)` can overflow `int` — cast to `long` before multiplying, same overflow discipline from Basic Programming applies here directly.

---

## Closing Thought from the Professor's Chair

Arrays look "simple" precisely because the syntax is simple — but that's exactly why interviewers use them to test how deep your thinking actually goes: memory model, complexity trade-offs, overflow discipline, and pattern recognition, all in one topic. Everything you learned in Basic Programming (overflow, references, complexity reasoning) gets its first real workout here. Master the **patterns** in section 8 — almost every "new" array problem you see going forward is just one of those five patterns wearing a different costume.

Next logical step: pick one problem per pattern (two-pointer, sliding window, prefix sum, Kadane's, hashing) and solve it without looking at notes — then come back and write your own "Common Mistakes I Made" line under each, like we discussed for spaced repetition.