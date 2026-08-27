# 📊 Sorting Algorithms — Placement Edition
### Notes from a "been-there, interviewed-that" professor's lens

> 🎯 Goal: Understand the five core sorting algorithms deeply enough that you can implement any of them from scratch, explain every line, analyse the complexity, and handle every twist an interviewer throws at you.
> Source: [Striver's A2Z DSA Sheet — Sorting Techniques](https://takeuforward.org/strivers-a2z-dsa-course/strivers-a2z-dsa-course-sheet-2/)

---

## 📚 Table of Contents

**Part 1 — Basic Sorts**
1. [A Word Before We Start](#a-word-before-we-start)
2. [Selection Sort](#1-selection-sort)
3. [Bubble Sort](#2-bubble-sort)
4. [Insertion Sort](#3-insertion-sort)

**Part 2 — Divide and Conquer Sorts**

5. [Merge Sort](#4-merge-sort)
6. [Quick Sort](#5-quick-sort)

**Part 3 — Revision & Interview**

7. [Comparison Table — All Five Algorithms](#6-comparison-table--all-five-algorithms)
8. [How to Recognise Which Sort is Being Asked](#7-how-to-recognise-which-sort-is-being-asked)
9. [What I Should Remember for Interviews](#8-what-i-should-remember-for-interviews)

---

## A Word Before We Start

Sorting is the first topic in Striver's A2Z sheet where beginners write real algorithms from scratch — not just call a library method. And it's the first topic where interviewers catch you off guard not by asking hard questions, but by asking *simple* ones you never thought about properly.

"Is Bubble Sort stable?" "Why does Merge Sort need O(n) extra space?" "What is the worst case for Quick Sort and when does it happen?" These are not trick questions — they are questions for which the correct answer requires actually understanding the algorithm, not just memorising the code.

This section is structured the same way as all other notes in this repository:
1. **What it is** (with real intuition)
2. **Why it bites you** (the traps interviewers exploit)
3. **How to not embarrass yourself** (the instincts you need cold)

Take the basic sorts seriously even though they feel simple. The thinking patterns you build — scanning for a minimum, bubbling large elements, building a sorted prefix — appear in harder problems dressed in new clothing.

---

## 1. Selection Sort

### What the Algorithm Does
Selection Sort works by repeatedly **selecting the minimum element** from the unsorted portion of the array and placing it in its correct position at the front.

After each pass, one more element at the front is permanently in its final sorted position.

### Core Intuition / Mental Model
Imagine you have a row of numbered cards spread on a table, face-up and in random order. You look through all the cards, pick the smallest one, and place it in the first slot. Now ignore slot 1. Look through the remaining cards, pick the smallest, place it in slot 2. Repeat.

The **sorted region grows from left to right**, one element per pass. You never go back to the sorted region.

### Step-by-Step Working

Given array: `[7, 5, 9, 2, 8]`

- **Pass 1:** Find min from index 0 to 4 → `2` (at index 3). Swap with index 0.
  Result: `[2, 5, 9, 7, 8]`
- **Pass 2:** Find min from index 1 to 4 → `5` (at index 1). Already in place.
  Result: `[2, 5, 9, 7, 8]`
- **Pass 3:** Find min from index 2 to 4 → `7` (at index 3). Swap with index 2.
  Result: `[2, 5, 7, 9, 8]`
- **Pass 4:** Find min from index 3 to 4 → `8` (at index 4). Swap with index 3.
  Result: `[2, 5, 7, 8, 9]` ✅

### Visual Simulation

```
Initial:  [ 7  5  9  2  8 ]
           ↑ unsorted region starts here

Pass 1:   [ 2 | 5  9  7  8 ]   ← 2 is now fixed
Pass 2:   [ 2  5 | 9  7  8 ]   ← 5 was already minimum, stays
Pass 3:   [ 2  5  7 | 9  8 ]   ← 7 moved into position
Pass 4:   [ 2  5  7  8 | 9 ]   ← 8 moved into position
Done:     [ 2  5  7  8  9 ]   ✅ sorted

The | symbol marks the boundary between sorted (left) and unsorted (right).
```

### Pseudocode

```
for i from 0 to n-2:
    minIndex = i
    for j from i+1 to n-1:
        if arr[j] < arr[minIndex]:
            minIndex = j
    if minIndex != i:
        swap(arr[i], arr[minIndex])
```

### Java Implementation

```java
public static void selectionSort(int[] arr) {
    int n = arr.length;

    for (int i = 0; i < n - 1; i++) {
        // Assume the first element of the unsorted region is the minimum
        int minIndex = i;

        // Search the rest of the unsorted region for a smaller element
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;   // update the index of the minimum
            }
        }

        // Swap the found minimum into the correct position
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}
```

### Explanation of Important Code

| Line | Why it works |
|---|---|
| `int minIndex = i` | We assume the current position holds the minimum until proven otherwise. |
| Inner `for j = i+1` | We only scan the **unsorted** portion — indices before `i` are already sorted and locked. |
| `if (arr[j] < arr[minIndex])` | We track the *index* of the minimum, not the value, so we know where to swap from. |
| `if (minIndex != i)` | Avoids an unnecessary swap when the minimum is already at its correct position. |
| Outer loop goes to `n-2` | After n−1 passes, the last element is automatically the largest — no pass needed. |

### Time Complexity

| Case | Comparisons | Swaps | Time |
|---|---|---|---|
| Best | n(n−1)/2 | 0 | **O(n²)** |
| Average | n(n−1)/2 | O(n) | **O(n²)** |
| Worst | n(n−1)/2 | O(n) | **O(n²)** |

The inner loop always runs fully regardless of the input. Selection Sort makes the same number of comparisons whether the array is sorted or not — **it cannot short-circuit**.

### Space Complexity
**O(1)** — sorting is done in-place using only a few integer variables.

### Is it Stable?
**No.** Selection Sort is **not stable**.

A swap can move a duplicate element past another equal element, breaking relative order.
Example: `[5a, 5b, 2]` → After pass 1, `2` swaps with `5a` → `[2, 5b, 5a]` — relative order of `5a` and `5b` has flipped.

### Is it In-Place?
**Yes.** No auxiliary array is used.

### Important Interview Observations
- Selection Sort always performs at most `n-1` swaps — useful when swaps are extremely expensive (e.g., swapping large records).
- Despite O(n²) time, it has O(n) swaps — better than Bubble Sort's O(n²) swaps in the worst case.
- In practice, always use built-in sorts. Selection Sort is a learning exercise, not a production tool.

### Common Beginner Mistakes

| Mistake | Fix |
|---|---|
| Looping outer loop to `n-1` instead of `n-2` | The last element needs no pass — it's automatically sorted. |
| Not tracking `minIndex` and swapping the wrong positions | Track the *index* of the minimum, not its value. |
| Forgetting the `if (minIndex != i)` guard | Not a logical error, but wastes swaps on already-correct elements. |
| Calling it stable | It is **not stable** — this is a frequent multiple-choice trap. |

---

## 2. Bubble Sort

### What the Algorithm Does
Bubble Sort works by **repeatedly comparing adjacent elements** and swapping them if they are in the wrong order. After each complete pass through the array, the **largest unsorted element "bubbles up"** to its correct position at the end.

### Core Intuition / Mental Model
Think of heavy bubbles in water. In each pass, the heaviest bubble that hasn't settled yet rises to the top (rightmost position). After each pass, one more element at the right end is permanently in its final sorted position.

The **sorted region grows from right to left**, one element per pass.

### Step-by-Step Working

Given array: `[7, 5, 9, 2, 8]`

**Pass 1:**
- Compare `7, 5` → swap → `[5, 7, 9, 2, 8]`
- Compare `7, 9` → no swap → `[5, 7, 9, 2, 8]`
- Compare `9, 2` → swap → `[5, 7, 2, 9, 8]`
- Compare `9, 8` → swap → `[5, 7, 2, 8, 9]` ← `9` is now fixed

**Pass 2:**
- Compare `5, 7` → no swap
- Compare `7, 2` → swap → `[5, 2, 7, 8, 9]`
- Compare `7, 8` → no swap ← `8` is now fixed

**Pass 3:**
- Compare `5, 2` → swap → `[2, 5, 7, 8, 9]`
- Compare `5, 7` → no swap ← `7` is now fixed

**Pass 4:**
- Compare `2, 5` → no swap ← `5` is now fixed (no swaps → early exit if using flag)

Result: `[2, 5, 7, 8, 9]` ✅

### Visual Simulation

```
Initial: [ 7  5  9  2  8 ]

Pass 1:  [ 5  7  2  8 | 9 ]   ← 9 bubbled to the end
Pass 2:  [ 5  2  7 | 8  9 ]   ← 8 bubbled to position
Pass 3:  [ 2  5 | 7  8  9 ]   ← 7 settled
Pass 4:  [ 2 | 5  7  8  9 ]   ← 5 settled (or early exit with flag)
Done:    [ 2  5  7  8  9 ]   ✅

The | symbol marks the boundary — sorted region is on the RIGHT.
```

### Pseudocode

```
for i from 0 to n-2:
    swapped = false
    for j from 0 to n-2-i:
        if arr[j] > arr[j+1]:
            swap(arr[j], arr[j+1])
            swapped = true
    if not swapped:
        break   // array is already sorted — early exit
```

### Java Implementation

```java
public static void bubbleSort(int[] arr) {
    int n = arr.length;

    for (int i = 0; i < n - 1; i++) {
        boolean swapped = false;   // optimisation: detect early completion

        for (int j = 0; j < n - 1 - i; j++) {
            // Compare adjacent elements
            if (arr[j] > arr[j + 1]) {
                // Swap them
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }

        // If no swap occurred in this full pass, the array is sorted
        if (!swapped) break;
    }
}
```

### Explanation of Important Code

| Line | Why it works |
|---|---|
| `j < n - 1 - i` | After `i` passes, the last `i` elements are already in final position — no need to touch them. |
| `boolean swapped` | Optimisation flag. If an entire pass completes without any swap, the array is already sorted. |
| `if (!swapped) break` | Turns the best case from O(n²) into O(n) — critical for an already-sorted array. |
| Only adjacent elements compared | Adjacent-only comparisons are what make Bubble Sort stable — equal elements never cross. |

### Time Complexity

| Case | Condition | Time |
|---|---|---|
| **Best** | Already sorted (with `swapped` flag) | **O(n)** — one pass, no swaps |
| **Average** | Random order | **O(n²)** |
| **Worst** | Reverse sorted | **O(n²)** |

### Space Complexity
**O(1)** — in-place, only a `temp` variable used.

### Is it Stable?
**Yes.** Bubble Sort is **stable**.

Adjacent elements are only swapped when `arr[j] > arr[j+1]` — strict greater-than. Equal elements are never swapped, so their relative order is always preserved.

### Is it In-Place?
**Yes.** No extra array needed.

### Important Interview Observations
- The `swapped` flag is almost always expected in an interview. Writing Bubble Sort without it is considered incomplete.
- Bubble Sort is the only O(n²) sort that can detect an already-sorted array in O(n) time.
- **"Adaptive"**: Bubble Sort (with the flag) is an *adaptive* sort — performance improves on nearly-sorted input.
- Despite these properties, it is almost never used in practice — high constant factor and O(n²) average.

### Common Beginner Mistakes

| Mistake | Fix |
|---|---|
| Using `j < n - 1` instead of `n - 1 - i` in inner loop | Reprocesses already-sorted elements — correct but wastes time. |
| Forgetting the `swapped` flag | Loses the best-case O(n) performance — often tested as a follow-up. |
| Not knowing it's stable | Easy interview point — always mention stability when discussing sorts. |

---

## 3. Insertion Sort

### What the Algorithm Does
Insertion Sort builds the sorted array **one element at a time** by taking each new element and **inserting it into the correct position** within the already-sorted portion on its left.

### Core Intuition / Mental Model
Think of how you sort a hand of playing cards. You pick up cards one by one. Each time you pick up a new card, you scan left through the cards already in your hand (which you keep sorted) and slot the new card into the right position.

The **sorted region grows from left to right**. Unlike Selection and Bubble Sort, Insertion Sort doesn't find the global minimum — it simply takes the next element and finds its correct place in the already-sorted prefix.

### Step-by-Step Working

Given array: `[7, 5, 9, 2, 8]`

- **i=1 (key=5):** Compare with 7 → 7 > 5, shift 7 right. Insert 5 at index 0.
  Result: `[5, 7, 9, 2, 8]`
- **i=2 (key=9):** Compare with 7 → 7 < 9, no shift. 9 stays.
  Result: `[5, 7, 9, 2, 8]`
- **i=3 (key=2):** Compare with 9 → shift. Compare with 7 → shift. Compare with 5 → shift. Insert 2 at index 0.
  Result: `[2, 5, 7, 9, 8]`
- **i=4 (key=8):** Compare with 9 → shift. Compare with 7 → 7 < 8, stop. Insert 8 at index 3.
  Result: `[2, 5, 7, 8, 9]` ✅

### Visual Simulation

```
Initial: [ 7  5  9  2  8 ]

i=1:     [ 5  7 | 9  2  8 ]   ← 5 inserted before 7
i=2:     [ 5  7  9 | 2  8 ]   ← 9 needs no shift
i=3:     [ 2  5  7  9 | 8 ]   ← 2 inserted at front
i=4:     [ 2  5  7  8  9 ]   ← 8 inserted between 7 and 9  ✅

Left of | is the sorted prefix. Each step inserts one more element into it.
```

### Pseudocode

```
for i from 1 to n-1:
    key = arr[i]
    j = i - 1
    while j >= 0 and arr[j] > key:
        arr[j + 1] = arr[j]   // shift element right
        j = j - 1
    arr[j + 1] = key          // insert key at correct position
```

### Java Implementation

```java
public static void insertionSort(int[] arr) {
    int n = arr.length;

    for (int i = 1; i < n; i++) {
        int key = arr[i];   // the element we want to insert into the sorted prefix
        int j = i - 1;

        // Shift elements of the sorted prefix that are greater than key, one position right
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }

        // Place key in its correct sorted position
        arr[j + 1] = key;
    }
}
```

### Explanation of Important Code

| Line | Why it works |
|---|---|
| `int key = arr[i]` | Save the current element before shifting begins — shifting will overwrite `arr[i]`. |
| `j = i - 1` | Start comparing from the rightmost element of the sorted prefix and move left. |
| `while (j >= 0 && arr[j] > key)` | Two conditions: don't go out of bounds (`j >= 0`), and only shift if sorted element is larger than `key`. |
| `arr[j + 1] = arr[j]` | Shifts the element one position right, making room for `key`. One write per step, not three (unlike swap). |
| `arr[j + 1] = key` | After the loop, `j` points to the last element NOT shifted — so `j+1` is the gap where `key` belongs. |

### Time Complexity

| Case | Condition | Time |
|---|---|---|
| **Best** | Already sorted — inner while loop never runs | **O(n)** |
| **Average** | Random order | **O(n²)** |
| **Worst** | Reverse sorted — every element shifts all the way to front | **O(n²)** |

### Space Complexity
**O(1)** — in-place. Only `key` and `j` as extra variables.

### Is it Stable?
**Yes.** Insertion Sort is **stable**.

The while loop uses strict greater-than (`arr[j] > key`). Equal elements in the sorted prefix are never shifted, so a duplicate stops the shifting and `key` is placed after it — preserving relative order.

### Is it In-Place?
**Yes.** No auxiliary array is used.

### Important Interview Observations
- Insertion Sort is the **fastest practical sort for small arrays** (≤ 10–20 elements). Java's `Arrays.sort()` switches to an insertion sort internally for small sub-arrays.
- It is **adaptive**: for a nearly-sorted array, it runs in close to O(n) time.
- When you see a library sort described as "hybrid" (TimSort, IntroSort), Insertion Sort is the base case for small partitions.

### Common Beginner Mistakes

| Mistake | Fix |
|---|---|
| Using `arr[j] >= key` instead of `arr[j] > key` | Breaks stability — equal elements get shifted unnecessarily. |
| Forgetting the `j >= 0` bound check | Causes `ArrayIndexOutOfBoundsException` when `key` is the smallest element seen so far. |
| Swapping instead of shifting | Swapping uses 3 operations per step; shifting uses 1. Both give correct results but shifting is the standard form. |
| Outer loop starts at 0 | It starts at 1 — the element at index 0 is already a "sorted prefix" of length 1. |

---

## 4. Merge Sort

### What the Algorithm Does
Merge Sort is a **divide and conquer** algorithm. It recursively splits the array into two halves, sorts each half independently, and then **merges the two sorted halves** back into a single sorted array.

### Core Intuition / Mental Model
You cannot sort a chaotic pile of exam papers at once — but you *can* split the pile in two, give half to a friend, sort your half, and then *merge* the two sorted halves together. Your friend does the same thing recursively.

- **Divide:** Keep splitting until every piece is just 1 element (a single element is always sorted).
- **Merge:** Combine two sorted pieces into one sorted piece by comparing element-by-element.

The key insight: **merging two already-sorted arrays is easy and fast — O(n)**. Dividing takes O(log n) levels. Combined: O(n log n).

### Step-by-Step Working

The algorithm works in two phases:

**Phase 1 — Divide:**
- Find `mid = (low + high) / 2`
- Recursively call `mergeSort(arr, low, mid)` — sort left half
- Recursively call `mergeSort(arr, mid+1, high)` — sort right half
- **Base case:** If `low >= high`, the subarray has 0 or 1 element — already sorted, return.

**Phase 2 — Merge:**
- Create a temporary array `temp`
- Use pointer `i` (starting at `low`) and pointer `j` (starting at `mid+1`) to walk through both halves
- Compare `arr[i]` and `arr[j]`; pick the smaller one into `temp` using pointer `k`
- After one half is exhausted, copy the remaining elements of the other half
- Copy `temp` back into `arr[low...high]`

### Understanding `low`, `mid`, `high`

These three variables define which portion of the *original* array we are currently sorting:

```
arr:   [ _ _ _ _ _ _ _ _ ]
        ↑           ↑   ↑
       low         mid  high

Everything between low and high (inclusive) is our current responsibility.
mid = (low + high) / 2 splits this region into:
  Left half:  arr[low .. mid]
  Right half: arr[mid+1 .. high]
```

We never create smaller arrays — we always operate on the same original array using index boundaries.

### Understanding `i`, `j`, `k`

During the merge step:

```
i → pointer walking through the LEFT half   (starts at low)
j → pointer walking through the RIGHT half  (starts at mid+1)
k → pointer walking through temp[]          (starts at 0)
```

We compare `arr[i]` vs `arr[j]` and place the smaller into `temp[k]`, advancing both `k` and the pointer of the element just picked.

### The Temporary Array

Merge Sort **cannot merge in-place** efficiently. Instead:
- We create a `temp[]` array sized exactly for the current region.
- We fill `temp` with the merged result.
- We copy `temp` back into `arr[low...high]`.

This copying back is **why Merge Sort has O(n) space complexity**.

### Complete Dry Run — `[7, 5, 9, 2, 8]`

**Phase 1 — Divide (recursion going down):**

```
mergeSort([7, 5, 9, 2, 8], low=0, high=4)
    mid = 2
    ├── mergeSort(low=0, high=2)
    │       mid = 1
    │       ├── mergeSort(low=0, high=1)
    │       │       mid = 0
    │       │       ├── mergeSort(low=0, high=0) → BASE CASE  [7]
    │       │       └── mergeSort(low=1, high=1) → BASE CASE  [5]
    │       │       → MERGE [7] and [5] → temp=[5,7] → arr[0..1]=[5,7]
    │       └── mergeSort(low=2, high=2) → BASE CASE  [9]
    │       → MERGE [5,7] and [9] → temp=[5,7,9] → arr[0..2]=[5,7,9]
    └── mergeSort(low=3, high=4)
            mid = 3
            ├── mergeSort(low=3, high=3) → BASE CASE  [2]
            └── mergeSort(low=4, high=4) → BASE CASE  [8]
            → MERGE [2] and [8] → temp=[2,8] → arr[3..4]=[2,8]
    → FINAL MERGE [5,7,9] and [2,8] → arr[0..4]=[2,5,7,8,9] ✅
```

**Phase 2 — Merge trace for `[5, 7, 9]` and `[2, 8]`:**

```
Left half:  arr[0..2] = [5, 7, 9]    i starts at 0
Right half: arr[3..4] = [2, 8]       j starts at 3
temp = []                             k starts at 0

Step 1: arr[i=0]=5  vs  arr[j=3]=2  →  2 smaller → temp[0]=2,  j++, k++
Step 2: arr[i=0]=5  vs  arr[j=4]=8  →  5 smaller → temp[1]=5,  i++, k++
Step 3: arr[i=1]=7  vs  arr[j=4]=8  →  7 smaller → temp[2]=7,  i++, k++
Step 4: arr[i=2]=9  vs  arr[j=4]=8  →  8 smaller → temp[3]=8,  j++, k++
Step 5: j=5 > high=4, right exhausted. Copy remaining left: arr[i=2]=9 → temp[4]=9

temp = [2, 5, 7, 8, 9]
Copy back to arr[0..4] → arr = [2, 5, 7, 8, 9] ✅
```

### Java Implementation

```java
public static void mergeSort(int[] arr, int low, int high) {
    // Base case: a subarray of 0 or 1 element is already sorted
    if (low >= high) return;

    int mid = (low + high) / 2;

    mergeSort(arr, low, mid);       // sort left half
    mergeSort(arr, mid + 1, high);  // sort right half

    merge(arr, low, mid, high);     // merge the two sorted halves
}

public static void merge(int[] arr, int low, int mid, int high) {
    // Temporary array to hold the merged result
    int[] temp = new int[high - low + 1];

    int i = low;       // pointer for left half:  arr[low .. mid]
    int j = mid + 1;   // pointer for right half: arr[mid+1 .. high]
    int k = 0;         // pointer for temp[]

    // Compare elements from both halves and pick the smaller one
    while (i <= mid && j <= high) {
        if (arr[i] <= arr[j]) {
            temp[k] = arr[i];
            i++;
        } else {
            temp[k] = arr[j];
            j++;
        }
        k++;
    }

    // Copy remaining elements of the left half (if any)
    while (i <= mid) {
        temp[k] = arr[i];
        i++;
        k++;
    }

    // Copy remaining elements of the right half (if any)
    while (j <= high) {
        temp[k] = arr[j];
        j++;
        k++;
    }

    // Copy temp back into the original array at the correct positions
    for (int l = 0; l < temp.length; l++) {
        arr[low + l] = temp[l];
    }
}
```

### Explanation of Important Code

| Line | Why it works |
|---|---|
| `if (low >= high) return` | Base case — a single element is trivially sorted. Also handles `low > high` edge cases. |
| `int mid = (low + high) / 2` | Splits the current region into two roughly equal halves. |
| `mergeSort(arr, low, mid)` | Sorts the left half. When this returns, `arr[low..mid]` is guaranteed sorted. |
| `mergeSort(arr, mid+1, high)` | Sorts the right half. When this returns, `arr[mid+1..high]` is guaranteed sorted. |
| `int[] temp = new int[high - low + 1]` | Exact size of the region being merged. |
| `i = low`, `j = mid+1` | `i` starts at the beginning of the left half; `j` at the beginning of the right half. Both index into the original `arr`. |
| `if (arr[i] <= arr[j])` | Uses `<=` (not `<`) — this preserves stability. Equal elements from the left half are always placed first. |
| `arr[low + l] = temp[l]` | Copy back using `low + l` as offset — the region might not start at index 0. |

### Time Complexity

| Case | Time |
|---|---|
| **Best** | **O(n log n)** |
| **Average** | **O(n log n)** |
| **Worst** | **O(n log n)** |

**Why O(n log n)?**
- Divide phase: recursion tree of depth **log₂(n)** (we halve the problem every level).
- At each level: total merge work across all calls is **O(n)** (every element is visited once per level).
- Total: **O(n) × O(log n) = O(n log n)**.

### Space Complexity
**O(n)** — the temporary array used during merging is the dominant cost.

The recursion stack adds O(log n), but O(n) for the temp array dominates.

### Is it Stable?
**Yes.** Merge Sort is **stable**.

The merge condition `arr[i] <= arr[j]` ensures that when two elements are equal, the one from the **left half** (earlier in the original array) is always placed first. Relative order is preserved.

### Is it In-Place?
**No.** Merge Sort requires O(n) extra space for the temporary array.

### Important Interview Observations
- Merge Sort is the **preferred sort for linked lists** — linked lists don't support random access, and Merge Sort's sequential access pattern is ideal.
- Merge Sort is the basis for **external sorting** (sorting data too large to fit in memory).
- The merge step appears in many advanced problems: "count inversions", "sort nearly-sorted k-sorted array", "merge K sorted arrays".

### Common Beginner Mistakes

| Mistake | Fix |
|---|---|
| Forgetting the base case `if (low >= high) return` | Infinite recursion — function never stops dividing. |
| Using `<` instead of `<=` in the merge condition | Breaks stability — equal elements from the right half get priority. |
| Forgetting to copy `temp` back into `arr` | Sorted values in `temp` are lost — `arr` remains unsorted in that region. |
| Wrong offset when copying back (`arr[l]` instead of `arr[low + l]`) | Overwrites the wrong region — subtle bug that only appears when `low > 0`. |
| Saying Merge Sort is O(1) space | It is **O(n) space** — never forget the temporary array. |

---

## 5. Quick Sort

### What the Algorithm Does
Quick Sort is a **divide and conquer** algorithm that selects a **pivot** element and **partitions** the array around it: all elements smaller than the pivot go to its left, all elements greater go to its right. The pivot then sits at its **exact final sorted position**. The left and right sub-arrays are sorted recursively.

### Core Intuition / Mental Model
Pick one card from a shuffled deck — the pivot. Sweep through the rest: everything smaller to the left pile, everything larger to the right pile. The pivot card is now in its perfect final position. Repeat this process independently on the left pile and right pile.

Unlike Merge Sort, Quick Sort doesn't need to merge anything. The heavy lifting is in the **partition** step — which does the sorting by moving elements to the correct side of the pivot.

### The Pivot Concept
The pivot is the element that ends up in its **final sorted position** after partitioning. Choosing a good pivot is critical:
- **Bad pivot** (always picks smallest or largest) → O(n²) worst case.
- **Good pivot** (splits roughly in half) → O(n log n) average.

We use the **last element** as the pivot (Lomuto partition scheme).

### Partitioning — The Lomuto Approach

We use the **Lomuto partition scheme**:
- `pivot = arr[high]` (last element)
- `i = low - 1` (boundary pointer — index of last confirmed element ≤ pivot)
- `j` scans from `low` to `high - 1`

**The meaning of `i` and `j`:**
- `i` marks the **boundary**: everything at index ≤ `i` is confirmed to be ≤ pivot.
- `j` is the **scanner**: it checks each element to decide if it belongs in the left region.

**Why does `i` start at `low - 1`?**

At the start, no element has been confirmed as ≤ pivot yet. The boundary starts just *before* the first element — `low - 1` — indicating an empty left region. As `j` finds qualifying elements, `i` advances (expanding the left region by one) and the element is swapped into it.

**Why does the pivot reach its final position?**

After the scan is complete, `i` points to the last element confirmed as ≤ pivot. Placing the pivot at `i + 1` puts it exactly where it belongs: all elements to its left are ≤ pivot, all elements to its right are > pivot. Nothing will ever move this pivot again.

**No temporary array needed:**

Partitioning is done entirely in-place by swapping elements directly within `arr`. Only `i`, `j`, and the `temp` variable for a single swap are used.

### Understanding `low`, `high`, `pivot`, `i`, `j`

```
arr:  [ 7  5  9  2  8 ]
       ↑               ↑
      low             high

pivot = arr[high] = 8   ← the element finding its final position
i     = low - 1  = -1   ← boundary (starts before all elements — empty left region)
j     = low      =  0   ← scanner (starts at the first element)
```

### Complete Dry Run — `[7, 5, 9, 2, 8]`

**Call:** `quickSort(arr, 0, 4)`

**Partition:** `pivot = arr[4] = 8`, `i = -1`

```
j=0: arr[0]=7  <=  8? YES → i++ → i=0, swap arr[0] and arr[0] → [7, 5, 9, 2, 8]  (no visible change)
j=1: arr[1]=5  <=  8? YES → i++ → i=1, swap arr[1] and arr[1] → [7, 5, 9, 2, 8]  (no visible change)
j=2: arr[2]=9  <=  8? NO  → skip, i stays at 1
j=3: arr[3]=2  <=  8? YES → i++ → i=2, swap arr[2] and arr[3] → [7, 5, 2, 9, 8]

Scan done. Place pivot at i+1=3: swap arr[3] and arr[4]
→ [7, 5, 2, 8, 9]

Pivot 8 is now at index 3 — its FINAL position. ✅
Left sub-array:  arr[0..2] = [7, 5, 2]
Right sub-array: arr[4..4] = [9]
```

**Recursive calls:**
- `quickSort(arr, 0, 2)` → sort [7, 5, 2]
- `quickSort(arr, 4, 4)` → base case (single element 9 ✅)

---

**Call:** `quickSort(arr, 0, 2)` — sorting [7, 5, 2]

**Partition:** `pivot = arr[2] = 2`, `i = -1`

```
j=0: arr[0]=7  <=  2? NO  → skip
j=1: arr[1]=5  <=  2? NO  → skip

Scan done. Place pivot at i+1=0: swap arr[0] and arr[2]
→ [2, 5, 7, 8, 9]

Pivot 2 is now at index 0 — its FINAL position. ✅
Left sub-array:  arr[0..-1] → low > high → base case
Right sub-array: arr[1..2]  = [5, 7]
```

---

**Call:** `quickSort(arr, 1, 2)` — sorting [5, 7]

**Partition:** `pivot = arr[2] = 7`, `i = 0`

```
j=1: arr[1]=5  <=  7? YES → i++ → i=1, swap arr[1] and arr[1] → [2, 5, 7, 8, 9]

Scan done. Place pivot at i+1=2: swap arr[2] and arr[2]
→ no change.

Pivot 7 is now at index 2 — its FINAL position. ✅
```

**Final sorted array: `[2, 5, 7, 8, 9]`** ✅

### Recursion Tree

```
quickSort(0, 4)  → pivot=8 lands at index 3
├── quickSort(0, 2)  → pivot=2 lands at index 0
│   ├── quickSort(0, -1)  → BASE CASE (low > high)
│   └── quickSort(1, 2)   → pivot=7 lands at index 2
│       ├── quickSort(1, 1)  → BASE CASE
│       └── quickSort(3, 2)  → BASE CASE (low > high)
└── quickSort(4, 4)  → BASE CASE
```

### Java Implementation

```java
public static void quickSort(int[] arr, int low, int high) {
    // Base case: subarray of 0 or 1 element is already sorted
    if (low >= high) return;

    // Partition the array and get the final position of the pivot
    int pivotIndex = partition(arr, low, high);

    // Recursively sort elements to the left and right of the pivot
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
}

public static int partition(int[] arr, int low, int high) {
    int pivot = arr[high];   // last element is the pivot
    int i = low - 1;         // i is the boundary of the "≤ pivot" region (starts empty)

    for (int j = low; j < high; j++) {
        // If current element belongs to the left region, expand it and move element in
        if (arr[j] <= pivot) {
            i++;
            // Swap arr[i] and arr[j]
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }

    // Place pivot at its correct final position (one past the last ≤ element)
    i++;
    int temp = arr[i];
    arr[i] = arr[high];
    arr[high] = temp;

    return i;   // return the final index of the pivot
}
```

### Explanation of Important Code

| Line | Why it works |
|---|---|
| `if (low >= high) return` | Base case — subarrays of size 0 or 1 are trivially sorted. |
| `int pivot = arr[high]` | Lomuto scheme: last element is pivot. |
| `int i = low - 1` | Left region starts empty. `i` will expand as qualifying elements are found. |
| `for (int j = low; j < high; j++)` | `j` scans all elements except the pivot at `high`. |
| `if (arr[j] <= pivot)` | Element belongs in the left partition. |
| `i++; swap(arr[i], arr[j])` | Expand the left region by one, bring the qualifying element into it. |
| Final `i++; swap(arr[i], arr[high])` | Place the pivot at `i` (now `i+1` after increment). This is its final sorted position. |
| `return i` | Return the pivot's final index — caller uses it to split for recursive calls. |

### Why Swapping is Done In-Place

Quick Sort **directly modifies the original array** through swaps. No temporary array is created. The only temporary variable is the `temp` integer used during a single swap. This is why Quick Sort is in-place and uses only O(log n) extra space (recursion stack), unlike Merge Sort's O(n).

### Time Complexity

| Case | Condition | Time |
|---|---|---|
| **Best** | Pivot always splits array into two equal halves | **O(n log n)** |
| **Average** | Pivot splits reasonably (random input) | **O(n log n)** |
| **Worst** | Pivot is always the smallest or largest element | **O(n²)** |

**Why O(n²) worst case?**

If the array is already sorted and we always pick the last element as pivot, the pivot is always the largest. The left partition gets n−1 elements and the right gets 0. The recursion tree degenerates into a chain of n levels, with O(n) work at each → O(n²).

### Space Complexity

| Case | Space |
|---|---|
| **Average** | **O(log n)** — recursion stack depth |
| **Worst** | **O(n)** — degenerate recursion (already-sorted input with last-element pivot) |

No auxiliary array is used — all work is in-place.

### Is it Stable?
**No.** Quick Sort is **not stable**.

The partition step can swap non-adjacent elements, breaking the relative order of equal elements.

### Is it In-Place?
**Yes.** Quick Sort sorts in-place — all swaps happen directly in `arr`.

### Important Interview Observations
- Quick Sort is the **fastest sort in practice** for random data — excellent cache performance because the partition step accesses memory sequentially.
- Despite O(n²) worst case, Quick Sort outperforms Merge Sort on average in-memory because it has **no extra allocation overhead**.
- `Arrays.sort()` in Java for **primitive types** uses Dual-Pivot Quick Sort. For **objects**, it uses TimSort (Merge Sort variant) — because object sorting needs stability.
- Worst case is avoided in practice by **randomising the pivot** — pick a random index, swap it to `high`, then run Lomuto.
- "Why prefer Quick Sort over Merge Sort for arrays?" → cache locality, in-place, no memory allocation.

### Common Beginner Mistakes

| Mistake | Fix |
|---|---|
| Using `low > high` instead of `low >= high` as base case | Both work; `>=` is safer and handles single-element subarrays explicitly. |
| Forgetting to `return i` from `partition` | Without the pivot's final index, you cannot determine where to split for recursive calls. |
| Including the pivot in a recursive call | `quickSort(arr, low, pivotIndex - 1)` and `quickSort(arr, pivotIndex + 1, high)` — pivot is already in its final position, do not touch it again. |
| Calling it stable | Quick Sort is **not stable**. |
| Saying worst case is O(n log n) | Worst case is **O(n²)** — when pivot selection is consistently bad. |

---

## 6. Comparison Table — All Five Algorithms

| Algorithm | Best | Average | Worst | Space | Stable | In-Place | Adaptive |
|---|---|---|---|---|---|---|---|
| **Selection Sort** | O(n²) | O(n²) | O(n²) | O(1) | ❌ No | ✅ Yes | ❌ No |
| **Bubble Sort** | O(n) ⭐ | O(n²) | O(n²) | O(1) | ✅ Yes | ✅ Yes | ✅ Yes |
| **Insertion Sort** | O(n) ⭐ | O(n²) | O(n²) | O(1) | ✅ Yes | ✅ Yes | ✅ Yes |
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ Yes | ❌ No | ❌ No |
| **Quick Sort** | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ No | ✅ Yes | ❌ No |

> ⭐ Best case O(n) for Bubble Sort requires the `swapped` flag. For Insertion Sort it comes naturally from already-sorted input.

### Quick Reference Notes

| Question | Answer |
|---|---|
| Which sort is O(n²) in all cases? | Selection Sort |
| Which sorts have O(n) best case? | Bubble Sort (with flag), Insertion Sort |
| Which sort uses O(n) extra space? | Merge Sort |
| Which sort is best for nearly-sorted data? | Insertion Sort |
| Which sort is best for linked lists? | Merge Sort |
| Which sort does Java use for primitives? | Dual-Pivot Quick Sort |
| Which sort does Java use for objects? | TimSort (Merge Sort variant) |
| Which sort uses fewest swaps? | Selection Sort (at most n−1 swaps) |

---

## 7. How to Recognise Which Sort is Being Asked

### Signal Words and Patterns

| If you see / hear this... | It's probably... |
|---|---|
| "Find minimum, place at front, repeat" | Selection Sort |
| "Compare adjacent elements, swap if wrong order" | Bubble Sort |
| "Large elements sink / small elements bubble up" | Bubble Sort |
| "Insert card into a sorted hand" | Insertion Sort |
| "Build sorted prefix one element at a time" | Insertion Sort |
| "Divide in half, sort each half, merge" | Merge Sort |
| "O(n log n) guaranteed, stable, extra O(n) space" | Merge Sort |
| "Best sort for linked lists" | Merge Sort |
| "Pivot, partition, left/right recursion" | Quick Sort |
| "In-place, O(n log n) average, unstable" | Quick Sort |
| "What Java uses for primitives" | Quick Sort (Dual-Pivot) |
| "What Java uses for objects / stable sort" | Merge Sort (TimSort) |
| "Worst case O(n²), average O(n log n)" | Quick Sort |
| "Best for small arrays / base case in hybrid sorts" | Insertion Sort |

### Pattern Recognition for Complexity Problems

| Complexity | Possible algorithm |
|---|---|
| Always O(n²) regardless of input | Selection Sort |
| O(n²) worst, O(n) best | Bubble Sort or Insertion Sort |
| O(n log n) in all cases | Merge Sort |
| O(n log n) average, O(n²) worst | Quick Sort |

---

## 8. What I Should Remember for Interviews

### The Things You Must Know Cold

**Selection Sort:**
- O(n²) always — no best-case improvement
- Not stable (swaps can skip over equal elements)
- Minimum swaps among all O(n²) sorts — at most n−1 swaps

**Bubble Sort:**
- With `swapped` flag: O(n) best case. Without it: O(n²) always — always mention the flag.
- Stable — adjacent-only swaps, equal elements never cross
- Adaptive — performance improves naturally on nearly-sorted input

**Insertion Sort:**
- O(n) best case — naturally adaptive, no flag needed
- Best practical sort for small arrays (≤ 15 elements)
- Used as base case inside TimSort and IntroSort
- Stable — `>` (not `>=`) in while condition keeps equal elements in order

**Merge Sort:**
- O(n log n) in all cases — most predictable sort
- O(n) extra space — the only sort in this list that needs extra memory
- Stable — left element wins on tie (`arr[i] <= arr[j]`)
- Best sort for linked lists — sequential access, no random indexing
- The merge step: understand `i` (left pointer), `j` (right pointer), `k` (temp pointer), and the copy-back

**Quick Sort:**
- O(n²) worst case — when pivot is always min or max (avoid with random pivot)
- O(log n) space on average (recursion stack depth)
- Not stable — partitioning can move equal elements past each other
- Fastest in practice for random in-memory data (cache-friendly, in-place)
- Java uses Dual-Pivot Quick Sort for primitives; TimSort (stable) for objects

### Interview Questions to Prepare

| Question | Short Answer |
|---|---|
| Why is Merge Sort preferred for external sorting? | Naturally divides data into chunks and merges sorted chunks — works even when data doesn't fit in RAM. |
| Why is Quick Sort usually faster than Merge Sort in practice? | No extra memory allocation; excellent cache locality during partitioning. |
| When would you prefer Insertion Sort over Merge Sort? | When n is small (< 15–20) or the array is nearly sorted. |
| Can you make Quick Sort stable? | Yes, but it loses the in-place advantage — not standard practice. |
| What is the difference between stable and unstable sorts? | Stable: equal elements keep their original relative order. Unstable: they might not. |
| Why does Bubble Sort have O(n) best case but Selection Sort doesn't? | Bubble Sort detects already-sorted input (no swaps → early exit). Selection Sort always completes all comparisons regardless of input. |
| What is an adaptive sort? | A sort whose performance improves on nearly-sorted input. Bubble and Insertion Sort are adaptive; Selection Sort and Merge Sort are not. |
| What is the Lomuto partition scheme? | Pick the last element as pivot, use boundary pointer `i` starting at `low-1`, scan with `j`, and place pivot at `i+1` after the scan. |

### Template to Describe Any Sort in an Interview

When asked "explain algorithm X", always hit these points in order:
1. **What it does** — one clear sentence
2. **Core mechanism** — how one pass/step works
3. **Time complexity** — best / average / worst
4. **Space complexity** — and why (mention the call stack for recursive sorts)
5. **Stable?** — yes/no and the key reason
6. **When would you use it?** — practical context

---

*Part of [Placement-Prep-2026](https://github.com/Harshaghera111/Placement-Prep-2026) — DSA Notes series.*
*Follows Striver's A2Z DSA Sheet — Sorting Techniques section.*
*Next topic: Binary Search*
