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

## 9. Striver A2Z — Array Problems

This section serves as the dedicated revision and progress tracker for the **Arrays** module of **Striver's A2Z DSA Sheet** ([takeuforward reference](https://takeuforward.org/prep-hub/strivers-a2z-dsa-sheet)). Problems are strictly categorized by Striver's official progression: **Easy (14)**, **Medium (14)**, and **Hard (12)**.

---

### Easy (14 Problems)

| # | Problem | Pattern / Main Idea | Status |
|---|---|---|---|
| 1 | [Largest Element in an Array](#1-largest-element-in-an-array) | Traversal / Track maximum initialized to `arr[0]` | ✅ Solved |
| 2 | [Second Largest Element in an Array without sorting](#2-second-largest-element-in-an-array-without-sorting) | Single Pass / Maintain `largest` and `secondLargest` | ⬜ Not Yet Solved |
| 3 | [Check if Array is Sorted](#3-check-if-array-is-sorted) | Traversal / Check if `arr[i] >= arr[i - 1]` for all indices | ⬜ Not Yet Solved |
| 4 | [Remove Duplicates from Sorted Array](#4-remove-duplicates-from-sorted-array) | Two Pointer / In-place slow pointer tracks unique elements | ⬜ Not Yet Solved |
| 5 | [Left Rotate an Array by One Place](#5-left-rotate-an-array-by-one-place) | In-Place Shifting / Save first element, shift rest left, place at end | ⬜ Not Yet Solved |
| 6 | [Left Rotate an Array by D Places](#6-left-rotate-an-array-by-d-places) | Reversal Algorithm / Reverse `0..d-1`, reverse `d..n-1`, reverse whole | ⬜ Not Yet Solved |
| 7 | [Move Zeroes to End](#7-move-zeroes-to-end) | Two Pointer / In-place maintain pointer for next non-zero position | ⬜ Not Yet Solved |
| 8 | [Linear Search](#8-linear-search) | Traversal / Scan sequentially until match found | ✅ Solved |
| 9 | [Find Union and Intersection of Two Sorted Arrays](#9-find-union-and-intersection-of-two-sorted-arrays) | Two Pointer / Coordinated traversal of two sorted arrays | ⬜ Not Yet Solved |
| 10 | [Find Missing Number in an Array](#10-find-missing-number-in-an-array) | Math / Sum Formula (`n*(n+1)/2 - sum`) or XOR | ✅ Solved |
| 11 | [Maximum Consecutive Ones](#11-maximum-consecutive-ones) | Traversal / Running streak counter, reset on 0 | ⬜ Not Yet Solved |
| 12 | [Single Number (Appears Once, Others Twice)](#12-single-number-appears-once-others-twice) | Bit Manipulation / XOR cancellation (`a ^ a = 0`) | ⬜ Not Yet Solved |
| 13 | [Longest Subarray with Given Sum K (Positives)](#13-longest-subarray-with-given-sum-k-positives) | Two Pointer / Sliding Window expanding right, shrinking left | ⬜ Not Yet Solved |
| 14 | [Longest Subarray with Sum K (Positives + Negatives)](#14-longest-subarray-with-sum-k-positives--negatives) | Prefix Sum + HashMap / Store earliest index of each prefix sum | ⬜ Not Yet Solved |

---

### Medium (14 Problems)

| # | Problem | Pattern / Main Idea | Status |
|---|---|---|---|
| 1 | [2Sum Problem](#1-2sum-problem) | Hashing / Target complement lookup in HashMap | ⬜ Not Yet Solved |
| 2 | [Sort an Array of 0s, 1s, and 2s](#2-sort-an-array-of-0s-1s-and-2s) | Dutch National Flag / 3 pointers (`low`, `mid`, `high`) partitioning | ⬜ Not Yet Solved |
| 3 | [Majority Element (> n/2 times)](#3-majority-element--n2-times) | Boyer-Moore Voting / Candidate tracking with balance count | ⬜ Not Yet Solved |
| 4 | [Maximum Subarray Sum (Kadane's Algorithm)](#4-maximum-subarray-sum-kadanes-algorithm) | Kadane's / Extend current subarray or restart fresh at each index | ⬜ Not Yet Solved |
| 5 | [Print Subarray with Maximum Subarray Sum](#5-print-subarray-with-maximum-subarray-sum) | Kadane's with Index Tracking / Track `start`, `ansStart`, `ansEnd` | ⬜ Not Yet Solved |
| 6 | [Stock Buy and Sell](#6-stock-buy-and-sell) | Single Pass Greedy / Track lowest buying price seen so far | ⬜ Not Yet Solved |
| 7 | [Rearrange Array Elements by Sign](#7-rearrange-array-elements-by-sign) | Two Pointer / Place positives at even indices, negatives at odd | ⬜ Not Yet Solved |
| 8 | [Next Permutation](#8-next-permutation) | Lexicographical Scan / Find pivot from right, swap with next greater, reverse | ⬜ Not Yet Solved |
| 9 | [Leaders in an Array](#9-leaders-in-an-array) | Reverse Traversal / Scan right-to-left tracking running maximum | ⬜ Not Yet Solved |
| 10 | [Longest Consecutive Sequence](#10-longest-consecutive-sequence) | HashSet Lookup / Start counting streak only if `(num - 1)` not in set | ⬜ Not Yet Solved |
| 11 | [Set Matrix Zeroes](#11-set-matrix-zeroes) | In-Place Markers / Use 1st row & 1st col as markers, plus 2 flag variables | ⬜ Not Yet Solved |
| 12 | [Rotate Image by 90 Degrees](#12-rotate-image-by-90-degrees) | Transpose + Row Reversal / In-place matrix rotation | ⬜ Not Yet Solved |
| 13 | [Spiral Matrix](#13-spiral-matrix) | Simulation / 4 boundary pointers (`top`, `bottom`, `left`, `right`) | ⬜ Not Yet Solved |
| 14 | [Count Subarray Sum Equals K](#14-count-subarray-sum-equals-k) | Prefix Sum + HashMap / Accumulate frequencies of `(prefix - k)` | ⬜ Not Yet Solved |

---

### Hard (12 Problems)

| # | Problem | Pattern / Main Idea | Status |
|---|---|---|---|
| 1 | [Pascal's Triangle](#1-pascals-triangle) | Combinatorics / Compute row values via `prev * (row - col) / col` | ⬜ Not Yet Solved |
| 2 | [Majority Element (n/3 times)](#2-majority-element-n3-times) | Extended Boyer-Moore / Track up to 2 candidates and 2 counters | ⬜ Not Yet Solved |
| 3 | [3-Sum Problem](#3-3-sum-problem) | Sorting + Two Pointer / Fix element `i`, two pointers on remainder, skip duplicates | ⬜ Not Yet Solved |
| 4 | [4-Sum Problem](#4-4-sum-problem) | Sorting + Two Pointer / Fix `i` & `j`, two pointers on remainder, cast to `long` | ⬜ Not Yet Solved |
| 5 | [Largest Subarray with 0 Sum](#5-largest-subarray-with-0-sum) | Prefix Sum + HashMap / Maximum span between identical prefix sums | ⬜ Not Yet Solved |
| 6 | [Count Subarrays with Given XOR K](#6-count-subarrays-with-given-xor-k) | Prefix XOR + HashMap / Count frequency of `(prefixXOR ^ K)` | ⬜ Not Yet Solved |
| 7 | [Merge Overlapping Subintervals](#7-merge-overlapping-subintervals) | Sorting + Linear Scan / Sort by start time; extend boundary if overlapping | ⬜ Not Yet Solved |
| 8 | [Merge Two Sorted Arrays Without Extra Space](#8-merge-two-sorted-arrays-without-extra-space) | Gap Method (Shell Sort) / Compare & swap at decreasing `gap = ceil(len/2)` | ⬜ Not Yet Solved |
| 9 | [Find the Repeating and Missing Number](#9-find-the-repeating-and-missing-number) | Math (Sum Equations) or XOR / Solve `X - Y` and `X² - Y²` | ⬜ Not Yet Solved |
| 10 | [Count Inversions in an Array](#10-count-inversions-in-an-array) | Divide and Conquer / Count inversions during Merge Sort merge step | ⬜ Not Yet Solved |
| 11 | [Reverse Pairs](#11-reverse-pairs) | Divide and Conquer / Count pairs with `nums[i] > 2 * nums[j]` in Merge Sort | ⬜ Not Yet Solved |
| 12 | [Maximum Product Subarray](#12-maximum-product-subarray) | Modified Kadane's / Track running min and max products (handles negative signs) | ⬜ Not Yet Solved |

---

### Problem Revision Notes — Easy

#### 1. Largest Element in an Array
- **Pattern:** Traversal / Single Pass
- **Core idea:** Maintain running `max` initialized to `arr[0]`. Traverse from index `1` to `n - 1`, updating `max = arr[i]` whenever a larger value is encountered.
- **Complexity:** O(n) time, O(1) space
- **Status:** ✅ Solved
- **Key Learnings & Traps:**
  - Initialize `max` with `arr[0]`, **not** `0` or `Integer.MIN_VALUE` arbitrarily without reason — if all elements are negative, initializing with `0` silently gives a wrong answer.
  - Edge case: empty array or single-element array — ensure code doesn't crash on index out of bounds.

*(Foundation Bonus Note)*: **Reverse an Array In-Place** (Two Pointer, O(n) time, O(1) space) — Swap `arr[left]` and `arr[right]`, then `left++`, `right--`, until `left >= right`. Loop condition `left < right` leaves the center element untouched for odd lengths. Status: ✅ Solved.

#### 2. Second Largest Element in an Array without sorting
- **Pattern:** Single Pass / Two Variable Tracking
- **Core idea:** Maintain `largest = arr[0]` and `secondLargest = -1` (or `Integer.MIN_VALUE`). For each element: if `num > largest`, `secondLargest = largest` and `largest = num`. Else if `num < largest && num > secondLargest`, `secondLargest = num`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 3. Check if Array is Sorted
- **Pattern:** Traversal
- **Core idea:** Scan from index `1` to `n - 1`. If `arr[i] < arr[i - 1]` at any point, the array is not sorted; return `false`. For rotated sorted check (LeetCode 1752), allow at most one count where `arr[i] > arr[(i + 1) % n]`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 4. Remove Duplicates from Sorted Array
- **Pattern:** Two Pointer / In-Place
- **Core idea:** Pointer `i = 0` marks the boundary of unique elements. Pointer `j` iterates from `1` to `n - 1`. Whenever `arr[j] != arr[i]`, increment `i` and set `arr[i] = arr[j]`. Return `i + 1`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 5. Left Rotate an Array by One Place
- **Pattern:** In-Place Shifting
- **Core idea:** Store `arr[0]` in a temporary variable `temp`. Shift all elements one step left (`arr[i - 1] = arr[i]` for `i` from `1` to `n - 1`). Assign `arr[n - 1] = temp`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 6. Left Rotate an Array by D Places
- **Pattern:** Reversal Algorithm
- **Core idea:** First normalize `d = d % n`. Reverse first `d` elements `arr[0..d-1]`, reverse remaining `n - d` elements `arr[d..n-1]`, then reverse the entire array `arr[0..n-1]`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 7. Move Zeroes to End
- **Pattern:** Two Pointer / In-Place
- **Core idea:** Find the first zero index `j`. Then iterate pointer `i` from `j + 1` to `n - 1`. Whenever `arr[i] != 0`, swap `arr[i]` with `arr[j]` and increment `j`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 8. Linear Search
- **Pattern:** Traversal
- **Core idea:** Sequentially compare every element `arr[i]` with the target value. Return the index on match; return `-1` if the end is reached without a match.
- **Complexity:** O(n) time, O(1) space
- **Status:** ✅ Solved
- **Code Reference:** Preserved in [Section 5: Searching](#5-searching)

#### 9. Find Union and Intersection of Two Sorted Arrays
- **Pattern:** Two Pointer
- **Core idea:** For union: maintain pointers `i` and `j`; append the smaller element while skipping adjacent duplicates, then flush leftovers. For intersection: advance smaller pointer; if equal, add to result and advance both.
- **Complexity:** O(n + m) time, O(n + m) space
- **Status:** ⬜ Not Yet Solved

#### 10. Find Missing Number in an Array
- **Pattern:** Math (Sum Formula) / Bitwise XOR
- **Core idea:** Expected sum of numbers from `0` to `n` is `n * (n + 1) / 2`. Missing number is `expectedSum - actualSum`. Alternatively, XOR all array values with numbers `0..n`; duplicate values cancel out (`x ^ x = 0`), leaving the missing number.
- **Complexity:** O(n) time, O(1) space
- **Status:** ✅ Solved
- **Key Learnings & Traps:**
  - For large `n`, `n * (n + 1)` can overflow 32-bit `int` — cast to `long` before multiplication, or use XOR to remain strictly overflow-immune.

#### 11. Maximum Consecutive Ones
- **Pattern:** Traversal / Counter
- **Core idea:** Maintain `count` and `maxCount`. Iterate through the array: if `arr[i] == 1`, increment `count` and update `maxCount = max(maxCount, count)`. If `arr[i] == 0`, reset `count = 0`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 12. Single Number (Appears Once, Others Twice)
- **Pattern:** Bit Manipulation / XOR
- **Core idea:** XOR all numbers together. Since `x ^ x = 0` and `x ^ 0 = x`, all numbers appearing twice cancel each other out, leaving only the single unique number.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 13. Longest Subarray with Given Sum K (Positives)
- **Pattern:** Two Pointer / Sliding Window
- **Core idea:** Maintain `left` and `right` pointers with running `sum`. Expand `right` to increase `sum`. While `sum > K` and `left <= right`, shrink by subtracting `arr[left++]`. When `sum == K`, update `maxLen = max(maxLen, right - left + 1)`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 14. Longest Subarray with Sum K (Positives + Negatives)
- **Pattern:** Prefix Sum + HashMap
- **Core idea:** Running prefix sum `currSum`. If `currSum == K`, `maxLen = i + 1`. If `(currSum - K)` exists in the map, a subarray sums to `K` with length `i - map.get(currSum - K)`. Only store `currSum` in map if not already present (to maximize subarray length).
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

---

### Problem Revision Notes — Medium

#### 1. 2Sum Problem
- **Pattern:** Hashing / Two Pointer
- **Core idea:** For each element, look up `target - arr[i]` in a HashMap. If present, return indices. If only returning boolean existence, sorting plus two pointers achieves O(n log n) time and O(1) space.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 2. Sort an Array of 0s, 1s, and 2s
- **Pattern:** Dutch National Flag Algorithm / Three Pointer
- **Core idea:** Maintain 3 pointers: `low = 0`, `mid = 0`, `high = n - 1`. If `arr[mid] == 0`, swap with `arr[low]` and increment `low`, `mid`. If `arr[mid] == 1`, increment `mid`. If `arr[mid] == 2`, swap with `arr[high]` and decrement `high`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 3. Majority Element (> n/2 times)
- **Pattern:** Boyer-Moore Voting Algorithm
- **Core idea:** Maintain `candidate` and `count = 0`. Iterate through array: if `count == 0`, set `candidate = arr[i]`. If `arr[i] == candidate`, increment `count`, else decrement `count`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 4. Maximum Subarray Sum (Kadane's Algorithm)
- **Pattern:** Kadane's Algorithm
- **Core idea:** At each element, decide whether to add it to the running sum or start fresh: `currSum = max(arr[i], currSum + arr[i])`. Track `maxSum = max(maxSum, currSum)`. If `currSum < 0`, reset `currSum = 0`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 5. Print Subarray with Maximum Subarray Sum
- **Pattern:** Kadane's Algorithm with Index Tracking
- **Core idea:** Track starting index `s` whenever `sum` resets to 0. When updating `maxSum`, record `ansStart = s` and `ansEnd = i`. The subarray is `arr[ansStart..ansEnd]`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 6. Stock Buy and Sell
- **Pattern:** Single Pass Greedy
- **Core idea:** Maintain `minPrice` initialized to `prices[0]` and `maxProfit = 0`. At each day, update `minPrice = min(minPrice, prices[i])` and `maxProfit = max(maxProfit, prices[i] - minPrice)`.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 7. Rearrange Array Elements by Sign
- **Pattern:** Two Pointer / Extra Array
- **Core idea:** Allocate result array of size `n`. Place positive numbers at even indices `posIndex = 0, 2, 4...` and negative numbers at odd indices `negIndex = 1, 3, 5...`.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 8. Next Permutation
- **Pattern:** Lexicographical Scan & Swap
- **Core idea:** 1) Find largest index `i` from right where `arr[i] < arr[i + 1]`. 2) If no such `i`, reverse whole array. 3) Otherwise, find smallest element in right suffix greater than `arr[i]`, swap them, and reverse suffix from `i + 1` to end.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 9. Leaders in an Array
- **Pattern:** Reverse Traversal
- **Core idea:** Scan from right to left maintaining `maxFromRight`. An element is a leader if `arr[i] >= maxFromRight`. Update `maxFromRight` whenever a new leader is found. Reverse result at end to restore original order.
- **Complexity:** O(n) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 10. Longest Consecutive Sequence
- **Pattern:** HashSet Lookup
- **Core idea:** Insert all numbers into a `HashSet`. Iterate through set: only attempt to build a sequence if `num - 1` is NOT in the set (ensures we only start at streak beginnings). While `set.contains(current + 1)`, increment streak.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 11. Set Matrix Zeroes
- **Pattern:** In-Place Markers
- **Core idea:** Use the first row and first column of the matrix as tracking flags. Use two separate booleans `firstRowZero` and `firstColZero` to mark whether the first row/col themselves originally had zeroes.
- **Complexity:** O(m × n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 12. Rotate Image by 90 Degrees
- **Pattern:** Transpose & Reverse
- **Core idea:** Clockwise 90-degree rotation equals: 1) Transpose matrix along main diagonal (`swap(matrix[i][j], matrix[j][i])` for `j > i`). 2) Reverse every row horizontally.
- **Complexity:** O(n²) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 13. Spiral Matrix
- **Pattern:** Simulation / 4 Boundary Pointers
- **Core idea:** Maintain `top = 0`, `bottom = m - 1`, `left = 0`, `right = n - 1`. Traverse left→right along `top`, top→bottom along `right`, right→left along `bottom`, bottom→top along `left`. Shrink boundaries inward after each side until pointers cross.
- **Complexity:** O(m × n) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 14. Count Subarray Sum Equals K
- **Pattern:** Prefix Sum + HashMap
- **Core idea:** Maintain running prefix sum. Check if `(prefixSum - k)` has been seen before in our HashMap; if yes, add its frequency to `count`. Initialize map with `map.put(0, 1)` to capture subarrays starting at index 0.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

---

### Problem Revision Notes — Hard

#### 1. Pascal's Triangle
- **Pattern:** Combinatorics / Row Generation
- **Core idea:** Any element at row `r` and col `c` is given by formula `nCr(r - 1, c - 1)`. To generate a full row in O(row), multiply previous element by `(row - col) / col`.
- **Complexity:** O(n²) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 2. Majority Element (n/3 times)
- **Pattern:** Extended Boyer-Moore Voting
- **Core idea:** At most 2 elements can appear strictly more than `⌊n / 3⌋` times. Maintain 2 candidate variables and 2 counters. After one pass, run a verification pass to count exact occurrences of candidates.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 3. 3-Sum Problem
- **Pattern:** Sorting + Two Pointer
- **Core idea:** Sort array. Outer loop fixes `arr[i]`. Use two pointers `left = i + 1`, `right = n - 1` to find pairs summing to `-arr[i]`. Skip duplicate values for `i`, `left`, and `right` to guarantee unique triplets.
- **Complexity:** O(n²) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 4. 4-Sum Problem
- **Pattern:** Sorting + Two Pointer
- **Core idea:** Sort array. Two outer loops fix `arr[i]` and `arr[j]`. Two pointers `left` and `right` find remaining pair summing to `target - arr[i] - arr[j]`. Cast sums to `long` to prevent 32-bit integer overflow.
- **Complexity:** O(n³) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 5. Largest Subarray with 0 Sum
- **Pattern:** Prefix Sum + HashMap
- **Core idea:** Store first index of each prefix sum in a HashMap. If a prefix sum repeats at index `i`, the subarray between the previous index and `i` sums to zero. Maximize `i - map.get(prefixSum)`.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 6. Count Subarrays with Given XOR K
- **Pattern:** Prefix XOR + HashMap
- **Core idea:** Let current prefix XOR be `XR`. If there is an earlier prefix with XOR `Y` such that `Y ^ K = XR`, then the subarray between them has XOR `K`. Since `Y = XR ^ K`, lookup `map.get(XR ^ K)`.
- **Complexity:** O(n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 7. Merge Overlapping Subintervals
- **Pattern:** Sorting + Interval Merge
- **Core idea:** Sort intervals by start time. Iterate through intervals: if current interval overlaps with the previous (`start <= prevEnd`), merge by updating `prevEnd = max(prevEnd, end)`. Otherwise, push as a new distinct interval.
- **Complexity:** O(n log n) time, O(1) auxiliary space
- **Status:** ⬜ Not Yet Solved

#### 8. Merge Two Sorted Arrays Without Extra Space
- **Pattern:** Gap Method (Shell Sort intuition)
- **Core idea:** Initialize `gap = ceil((n + m) / 2)`. Compare and swap elements at distance `gap` across the virtual combined array. Continue halving `gap` until `gap == 0`.
- **Complexity:** O((n + m) log(n + m)) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 9. Find the Repeating and Missing Number
- **Pattern:** Math Equations / Bitwise XOR
- **Core idea:** Let `X` be repeating and `Y` be missing. 1) `S - Sn = X - Y`. 2) `S2 - S2n = X² - Y² = (X - Y)(X + Y)`. Dividing (2) by (1) gives `X + Y`. Solve both linear equations to find `X` and `Y` in O(1) space without modifying array.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

#### 10. Count Inversions in an Array
- **Pattern:** Divide and Conquer / Merge Sort
- **Core idea:** An inversion is `i < j` with `arr[i] > arr[j]`. Modify Merge Sort: during the merge step, if `leftArr[i] > rightArr[j]`, all remaining elements from `i` to `mid` also form inversions with `rightArr[j]`, contributing `(mid - i + 1)` inversions.
- **Complexity:** O(n log n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 11. Reverse Pairs
- **Pattern:** Divide and Conquer / Merge Sort
- **Core idea:** A reverse pair is `i < j` with `nums[i] > 2 * nums[j]`. In Merge Sort, count reverse pairs using a dedicated two-pointer pass before the actual merge step: for each element in left half, advance pointer in right half while condition holds.
- **Complexity:** O(n log n) time, O(n) space
- **Status:** ⬜ Not Yet Solved

#### 12. Maximum Product Subarray
- **Pattern:** Modified Kadane's / Prefix-Suffix Product
- **Core idea:** Because negative numbers flip signs when multiplied, track both `maxProd` and `minProd` ending at each position. When encountering a negative number, swap `maxProd` and `minProd` before multiplying.
- **Complexity:** O(n) time, O(1) space
- **Status:** ⬜ Not Yet Solved

---

## 10. Important Array Lessons

1. **Single Traversal Supremacy:** When a problem asks for running extremes or balance points, check if one pass with 1–2 state variables avoids nested loops (e.g., Min Price tracking in Stock Buy & Sell, running sums in Pivot balance).
2. **Two Pointer Trigger:** Sorted arrays almost always yield to two pointers moving from extremes (3Sum, 4Sum, Union/Intersection) or in-place slow/fast pointers (Remove Duplicates, Move Zeroes).
3. **Prefix Sum + Hashing Trigger:** Subarray sum or XOR problems (`sum == K`, `sum == 0`, `XOR == K`) transform from O(n²) to O(n) by storing historical prefix values in a HashMap.
4. **In-Place Matrix Markers:** When 2D matrix problems restrict auxiliary space to O(1), use row 0 and col 0 as indicator flags (Set Matrix Zeroes).
5. **Integer Overflow Discipline:** Always cast to `long` before calculating `n * (n + 1)` or summing 4 integers in 4Sum to prevent silent 32-bit signed overflow.
6. **Edge Cases to Always Probe Out Loud:**
   - Empty array (`n == 0`) or single element (`n == 1`).
   - Array with all negative numbers (initializing maximum to 0 fails).
   - Array with all duplicate elements.
   - Elements where sum or product exceeds `Integer.MAX_VALUE`.

---

## Closing Thought from the Professor's Chair

Arrays look "simple" precisely because the syntax is simple — but that's exactly why interviewers use them to test how deep your thinking actually goes: memory model, complexity trade-offs, overflow discipline, and pattern recognition, all in one topic. Everything you learned in Basic Programming (overflow, references, complexity reasoning) gets its first real workout here. Master the **patterns** in section 8 — almost every "new" array problem you see going forward is just one of those five patterns wearing a different costume.

Next logical step: pick one problem per pattern (two-pointer, sliding window, prefix sum, Kadane's, hashing) and solve it without looking at notes — then come back and write your own "Common Mistakes I Made" line under each, like we discussed for spaced repetition.