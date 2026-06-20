# DSA Basics

> Concepts, observations, and revision notes — not code.

---

## Number System Tricks

### Count Digits
- Divide by 10 removes the last digit.
- Count loop iterations until `n == 0`.
- Edge case: `n = 0` → answer is 1.
- Formula: `floor(log10(n)) + 1`

### Reverse a Number
- `% 10` gives the last digit.
- `/ 10` removes the last digit.
- Build: `rev = rev * 10 + digit`
- Edge case: negative numbers, trailing zeros.

### Check Palindrome (Number)
- Reverse the number, compare with original.
- Negative numbers are never palindromes.

### Armstrong Number
- Sum of each digit raised to power of (number of digits) == original.
- e.g. 153 → 1³ + 5³ + 3³ = 153 ✓

---

## Bit Manipulation

| Operation | Trick |
|---|---|
| Check if even/odd | `n & 1` → 0 = even, 1 = odd |
| Check if power of 2 | `n & (n-1) == 0` |
| Set k-th bit | `n \| (1 << k)` |
| Clear k-th bit | `n & ~(1 << k)` |
| Toggle k-th bit | `n ^ (1 << k)` |
| Count set bits | `Integer.bitCount(n)` or loop with `n & 1` |
| XOR trick | `a ^ a = 0`, `a ^ 0 = a` — useful for finding unique elements |

---

## Math Patterns

### GCD / HCF
- `gcd(a, b) = gcd(b, a % b)` — Euclidean algorithm
- Base case: `gcd(a, 0) = a`
- `LCM(a, b) = (a * b) / gcd(a, b)`

### Prime Check
- Trial division up to `√n`.
- If no divisor found in `[2, √n]`, it's prime.
- Even numbers > 2 are never prime.

### Sieve of Eratosthenes
- Find all primes up to `n` in `O(n log log n)`.
- Mark multiples of each prime as composite.

---

## Complexity Cheat Sheet

| Complexity | Name | Example |
|---|---|---|
| O(1) | Constant | Array access |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Single loop |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | Nested loops |
| O(2ⁿ) | Exponential | Recursion without memo |
| O(n!) | Factorial | Permutations |

---

## Common Mistakes

- Integer overflow: use `long` when multiplying two ints.
- Off-by-one in loops: check `<` vs `<=`.
- Modifying array while iterating — use a copy or reverse iterate.
- Forgetting base cases in recursion.

---

*Add observations here as you solve problems.*
