# Problem-Solving Patterns

> A reference guide for recognizing which technique to apply to a problem.

---

## Pattern Recognition Guide

| Keyword in Problem | Think |
|---|---|
| Subarray / substring | Sliding Window, Prefix Sum |
| Sorted array + target | Two Pointer, Binary Search |
| Pairs with sum | Two Pointer (sorted), Hashmap |
| Combinations / permutations | Backtracking |
| Min / max path / cost | DP or Greedy |
| Count ways | DP |
| Shortest path (unweighted) | BFS |
| Shortest path (weighted) | Dijkstra |
| "Next greater / smaller" | Monotonic Stack |
| Tree/graph connectivity | BFS/DFS |
| Overlapping intervals | Sort by start, Greedy |
| Top K elements | Heap (Priority Queue) |
| Repeated subproblems | DP / Memoization |
| Contiguous subsequence | Kadane's / Sliding Window |

---

## The 5-Step Problem Solving Framework

1. **Understand** — Restate the problem. Identify input/output. Ask: what does a brute force look like?
2. **Examples** — Walk through 2-3 examples manually, including edge cases.
3. **Pattern** — Which pattern does this fit? (see table above)
4. **Code** — Write clean code with meaningful variable names.
5. **Test** — Run through examples, check edge cases.

---

## Binary Search Patterns

Binary search works whenever the search space is **monotonic** (answer doesn't decrease then increase).

- **Classic:** find exact target in sorted array.
- **Lower bound:** first position where condition is true.
- **Upper bound:** last position where condition is true.
- **On answer:** "Minimize the maximum" or "Maximize the minimum" → binary search on the answer.

Template:
```
low = min_possible, high = max_possible
while (low < high):
    mid = low + (high - low) / 2
    if condition(mid): high = mid
    else: low = mid + 1
return low
```

---

## Backtracking Pattern

```
function backtrack(state):
    if base_case: add to result, return
    for each choice:
        make choice
        backtrack(new state)
        undo choice (backtrack)
```

Used for: Subsets, Permutations, Combinations, N-Queens, Sudoku.

**Pruning:** add conditions to skip invalid branches early → drastically reduces time.

---

## Greedy Pattern

- Make the locally optimal choice at each step.
- Works when: optimal substructure + greedy choice property.
- Always verify with a proof or counterexample.

Common greedy: Activity Selection, Fractional Knapsack, Huffman Encoding, Dijkstra.

---

## Heap / Priority Queue Patterns

- **Top K largest:** use min-heap of size K (remove smallest when size > K).
- **Top K smallest:** use max-heap of size K.
- **Merge K sorted lists:** always extract minimum from heap.
- **Sliding window maximum:** use deque (monotonic).

---

## Divide and Conquer Pattern

- Split problem into smaller subproblems.
- Solve each subproblem recursively.
- Combine results.

Examples: Merge Sort, Quick Sort, Binary Search, Maximum Subarray (but Kadane's is better).

---

## Common Interview Mistakes to Avoid

- Not clarifying constraints before coding (affects choice of algorithm).
- Starting with optimal solution — state brute force first, then optimize.
- Forgetting edge cases: empty input, single element, all same, negatives.
- Not checking for integer overflow in sum/product operations.
- Modifying input when it shouldn't be modified.

---

## Time Complexity Quick Reference

| Algorithm | Average | Worst |
|---|---|---|
| Binary Search | O(log n) | O(log n) |
| BFS/DFS | O(V+E) | O(V+E) |
| Merge Sort | O(n log n) | O(n log n) |
| Quick Sort | O(n log n) | O(n²) |
| Dijkstra (heap) | O((V+E) log V) | — |
| DP (most 2D) | O(n²) | — |
| Backtracking | O(2ⁿ) or O(n!) | — |

---

*This file is your go-to reference before starting any problem. Update it as you discover new patterns.*
