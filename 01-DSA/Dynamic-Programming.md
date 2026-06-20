# Dynamic Programming

> Concepts, patterns, and revision notes — not code.

---

## What is DP?

Dynamic Programming = **Recursion + Memoization** (top-down) or **Tabulation** (bottom-up).

Use DP when:
1. The problem has **overlapping subproblems** — same subproblem solved multiple times.
2. The problem has **optimal substructure** — optimal solution built from optimal sub-solutions.

---

## Approaches

### Top-Down (Memoization)
- Write recursion naturally.
- Add a `memo` array/map to cache results.
- `if (memo[i] != -1) return memo[i]`

### Bottom-Up (Tabulation)
- Build a `dp` table iteratively.
- Fill from base cases up to the answer.
- Usually more space-efficient.

---

## DP Patterns

### 1D DP
- State: `dp[i]` = answer for index `i`.
- Transition: `dp[i]` depends on `dp[i-1]`, `dp[i-2]`, etc.
- **Problems:** Fibonacci, Climbing Stairs, House Robber.

### 2D DP
- State: `dp[i][j]` = answer using first `i` elements and `j` capacity/index.
- **Problems:** Longest Common Subsequence, Edit Distance, 0/1 Knapsack.

### Substring/Subarray DP
- `dp[i][j]` = property of substring `s[i..j]`.
- **Problems:** Longest Palindromic Subsequence, Matrix Chain Multiplication.

---

## Classic DP Problems

### Knapsack (0/1)
- `dp[i][w]` = max value using first `i` items with weight limit `w`.
- Choice: include item `i` (if `weight[i] <= w`) or exclude.
- `dp[i][w] = max(dp[i-1][w], val[i] + dp[i-1][w - wt[i]])`

### Longest Common Subsequence (LCS)
- `dp[i][j]` = LCS of first `i` chars of s1 and first `j` chars of s2.
- If `s1[i] == s2[j]`: `dp[i][j] = 1 + dp[i-1][j-1]`
- Else: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`

### Longest Increasing Subsequence (LIS)
- `dp[i]` = length of LIS ending at index `i`.
- For each `j < i`: if `arr[j] < arr[i]`, `dp[i] = max(dp[i], dp[j] + 1)`
- O(n²). Optimal: O(n log n) with patience sorting.

### Edit Distance
- `dp[i][j]` = min operations to convert `word1[0..i]` to `word2[0..j]`.
- If chars match: `dp[i][j] = dp[i-1][j-1]`
- Else: `dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])` (delete, insert, replace)

---

## DP on Strings

- LCS → Longest Common Substring → Edit Distance → Shortest Common Supersequence.
- These all build on each other. Know LCS deeply.

---

## Common Mistakes

- Not initializing base cases correctly.
- Wrong transition (off-by-one indexing).
- Thinking greedy works when it doesn't — always ask: does greedy choice work for all cases?
- Stack overflow in deep recursion — use tabulation or increase stack size.

---

## Key Observations

- **"Count the ways"** → usually DP (not greedy).
- **"Min/max"** → could be greedy or DP. If choices depend on previous choices → DP.
- When you see exponential recursion with repeated subproblems → memoize.
- Space optimization: if `dp[i]` only depends on `dp[i-1]`, use 2 variables instead of full array.

---

## Space Optimization Trick

- 2D DP → 1D: if row `i` only depends on row `i-1`, you can use a single row.
- For Knapsack: iterate weights in **reverse** when optimizing to 1D.

---

*Add observations and problem patterns as you study DP.*
