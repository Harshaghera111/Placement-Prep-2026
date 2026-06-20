# Linked List

> Concepts, patterns, and revision notes — not code.

---

## Core Concepts

- Singly linked list: each node has `data` and `next`.
- Doubly linked list: each node has `data`, `next`, and `prev`.
- No random access — traversal is always O(n).
- Insertion/deletion at head is O(1). At tail: O(n) singly, O(1) doubly.

---

## Common Operations

| Operation | Time |
|---|---|
| Access by index | O(n) |
| Insert at head | O(1) |
| Insert at tail | O(n) singly / O(1) with tail pointer |
| Delete a node (given pointer) | O(1) |
| Search | O(n) |

---

## Patterns

### Fast & Slow Pointers (Floyd's Algorithm)
- **Detect cycle:** slow moves 1 step, fast moves 2 steps. If they meet → cycle exists.
- **Find middle:** when fast reaches end, slow is at middle.
- **Find cycle start:** after detection, move one pointer to head; both move 1 step — they meet at cycle start.

### Reversal Pattern
- Reverse whole list: three pointers — `prev`, `curr`, `next`.
- Reverse in k-groups: reverse subgroups, reconnect.
- Reverse between positions l and r: find l, reverse the segment, reconnect.

### Two-Pointer Gap
- Find n-th node from end: move fast pointer n steps ahead, then both at same pace — slow lands on target.

### Merge Pattern
- Merge two sorted lists: use dummy node, compare and link.
- Used in merge sort on linked list.

---

## Important Problems

- Reverse a linked list (iterative + recursive)
- Detect cycle (Floyd's)
- Find middle of linked list
- Merge two sorted lists
- Remove n-th node from end
- Check if linked list is palindrome
- Intersection of two linked lists

---

## Palindrome Linked List

1. Find middle using slow/fast.
2. Reverse second half.
3. Compare first and reversed second half.
4. Restore list (optional).

---

## Common Mistakes

- Null pointer exceptions — always check `curr != null` before `curr.next`.
- Losing reference to head when modifying.
- Off-by-one in two-pointer gap problems.
- Forgetting to update `prev` in reversal.

---

## Key Observations

- Dummy/sentinel node simplifies head-insertion logic — avoids special cases.
- Most linked list problems involve: slow/fast pointers, reversal, or merge.
- For palindrome or cycle problems, in-place approach requires reversal trick.

---

*Add observations as you solve more linked list problems.*
