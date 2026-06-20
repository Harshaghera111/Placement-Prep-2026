# Trees

> Concepts, patterns, and revision notes — not code.

---

## Core Concepts

- Binary tree: each node has at most 2 children.
- Binary Search Tree (BST): left subtree < root < right subtree.
- Height of tree: longest path from root to leaf.
- Depth of node: distance from root.
- Complete tree: all levels filled except last (filled left to right).
- Perfect tree: all internal nodes have 2 children, all leaves at same level.

---

## Traversals

| Traversal | Order | Use Case |
|---|---|---|
| Inorder (LNR) | Left → Root → Right | BST → sorted order |
| Preorder (NLR) | Root → Left → Right | Copy tree, serialize |
| Postorder (LRN) | Left → Right → Root | Delete tree, evaluate expression |
| Level Order (BFS) | Level by level | Shortest path, level problems |

**Key insight:** Inorder of BST gives sorted sequence. Use this to verify BST.

---

## Recursion Pattern for Trees

Most tree problems follow:
1. Base case: `if node == null, return`
2. Solve for left subtree
3. Solve for right subtree
4. Combine results

Think: "What do I need from left? What from right? How do I combine?"

---

## Common Tree Patterns

### Height / Depth
- Height = `1 + max(height(left), height(right))`
- Diameter = `max height of left + max height of right` at each node.

### Path Sum
- Subtract target as you go down.
- At leaf, check if `target == 0`.
- For max path sum: pass up the best single path, track global max including both sides.

### LCA (Lowest Common Ancestor)
- If both nodes are in left → LCA is in left.
- If both nodes are in right → LCA is in right.
- Otherwise → current node is LCA.

### BST Properties
- Inorder traversal gives sorted order.
- Search, insert, delete: O(h) — O(log n) balanced, O(n) skewed.
- Valid BST: check with min/max range at each node (not just parent comparison).

### Level Order (BFS)
- Use a queue.
- Process one level at a time: `for (int i = 0; i < levelSize; i++)`.
- Store results per level in a list.

---

## Important Problems

- Height / Diameter of Binary Tree
- Balanced Binary Tree
- Symmetric Tree / Mirror
- Level Order Traversal (zigzag variant)
- Max Path Sum
- LCA of Binary Tree / BST
- Serialize and Deserialize Binary Tree
- Validate BST
- Kth Smallest in BST (inorder)
- Right Side View

---

## Common Mistakes

- Comparing `==` for nodes (reference) instead of values.
- Not handling null nodes before accessing children.
- Confusing height and depth.
- Validating BST by only comparing with direct parent — must carry min/max bounds.

---

## Key Observations

- If problem asks "for each node" → likely DFS with return value.
- If problem asks "level by level" → BFS with queue.
- Diameter of tree: at each node, `left_height + right_height` is a candidate.
- BST + sorted order → always think inorder traversal.
- Morris Traversal: O(1) space inorder — modify tree temporarily.

---

*Add notes as you learn more tree patterns.*
