# Strings

> Concepts, patterns, and revision notes — not code.

---

## Core Concepts

- Strings are immutable in Java/Python — concatenation creates a new object each time.
- Use `StringBuilder` (Java) or `list + join` (Python) for building strings in loops.
- Character arrays are mutable and useful for in-place operations.

---

## ASCII Quick Reference

| Character | ASCII |
|---|---|
| '0' | 48 |
| '9' | 57 |
| 'A' | 65 |
| 'Z' | 90 |
| 'a' | 97 |
| 'z' | 122 |

- `ch - '0'` converts a digit character to its integer value.
- `ch - 'a'` gives index in alphabet (0–25).
- `Character.isDigit(ch)`, `Character.isLetter(ch)`, `Character.toLowerCase(ch)`.

---

## Common Patterns

### Frequency Count
- Use `int[26]` for lowercase letters, `int[128]` for ASCII.
- `freq[ch - 'a']++`
- Used in: anagram check, character frequency problems.

### Two Pointer
- Palindrome check: left pointer at start, right at end, move inward.
- Valid palindrome (ignoring non-alphanumeric): skip invalid chars, compare.

### Sliding Window
- Longest substring without repeating characters.
- Minimum window substring.
- Use a hashmap/array to track character counts inside window.

---

## Important String Problems

### Palindrome
- Simple: reverse and compare.
- In-place two pointer: O(n) time, O(1) space.
- Longest palindromic substring: expand around center (2n-1 centers).

### Anagram
- Two strings are anagrams if their sorted versions are equal, OR frequency maps match.
- Sorted approach: O(n log n). Frequency map: O(n).

### String Matching
- Naive: O(n*m).
- KMP: O(n+m) — uses failure function to skip unnecessary comparisons.

---

## Common Mistakes

- Comparing strings with `==` in Java (use `.equals()`).
- Forgetting that `String.substring(i, j)` is exclusive on `j`.
- Off-by-one in palindrome expansion.
- Not handling empty string or single character edge cases.

---

## Key Observations

- If the problem involves substrings → think sliding window.
- If it involves permutations/anagrams → think frequency array.
- If it involves pattern matching → think two pointers or KMP.
- Character frequency problems usually need an O(26) or O(128) array.

---

*Add notes as you encounter new string patterns.*
