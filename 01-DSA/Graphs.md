# Graphs

> Concepts, patterns, and revision notes — not code.

---

## Core Concepts

- Graph = Vertices (nodes) + Edges.
- **Directed:** edges have direction. **Undirected:** edges go both ways.
- **Weighted:** edges have weights. **Unweighted:** all edges equal.
- **Connected:** path exists between every pair of nodes.
- **Cyclic:** contains at least one cycle. **Acyclic:** no cycles (DAG if directed).

---

## Representations

| Type | Space | Access Edge | Best For |
|---|---|---|---|
| Adjacency Matrix | O(V²) | O(1) | Dense graphs |
| Adjacency List | O(V+E) | O(degree) | Sparse graphs (most problems) |

---

## BFS (Breadth-First Search)

- Uses a **queue**.
- Explores level by level — finds **shortest path** in unweighted graphs.
- Mark visited **before** adding to queue (not after dequeue).
- Time: O(V+E), Space: O(V)

**When to use:** Shortest path, level-order problems, multi-source BFS.

---

## DFS (Depth-First Search)

- Uses **recursion** (or explicit stack).
- Explores as deep as possible before backtracking.
- Time: O(V+E), Space: O(V) recursion stack.

**When to use:** Cycle detection, connected components, topological sort, path existence.

---

## Cycle Detection

- **Undirected:** DFS — if you visit an already-visited node that isn't the parent → cycle.
- **Directed:** DFS with 3 states — unvisited (0), in-stack (1), done (2). If you reach a node in-stack → cycle.

---

## Topological Sort

- Only possible in **DAGs** (Directed Acyclic Graphs).
- **Kahn's Algorithm (BFS):** Start with nodes of in-degree 0. Process, reduce in-degree of neighbors, add new 0-in-degree nodes to queue. If all nodes processed → valid topo order. If not → cycle exists.
- **DFS approach:** After exploring all neighbors, push node to stack. Reverse the stack.

---

## Dijkstra's Algorithm (Shortest Path, Weighted)

- Works only with **non-negative weights**.
- Uses a min-heap (priority queue).
- Greedy: always process the node with the current shortest distance.
- Time: O((V+E) log V)

---

## Union-Find (Disjoint Set Union)

- Efficiently checks if two nodes are connected (same component).
- **Union:** merge two sets.
- **Find:** find root/representative of a set.
- Optimizations: **Path Compression** + **Union by Rank** → nearly O(1) per operation.
- Used in: Kruskal's MST, detecting cycle in undirected graph.

---

## Important Graph Algorithms

| Algorithm | Purpose | Time |
|---|---|---|
| BFS | Shortest path (unweighted) | O(V+E) |
| DFS | Connectivity, cycles | O(V+E) |
| Dijkstra | Shortest path (weighted, non-neg) | O((V+E) log V) |
| Bellman-Ford | Shortest path (negative weights) | O(V*E) |
| Floyd-Warshall | All-pairs shortest path | O(V³) |
| Kruskal / Prim | Minimum Spanning Tree | O(E log E) |
| Kahn's | Topological sort | O(V+E) |

---

## Common Problems

- Number of Islands (BFS/DFS on grid)
- Clone Graph
- Course Schedule (cycle detection / topo sort)
- Shortest Path in Binary Matrix
- Number of Provinces (connected components)
- Rotten Oranges (multi-source BFS)

---

## Common Mistakes

- Forgetting to mark visited before BFS enqueue (infinite loop).
- Using BFS for weighted shortest path — use Dijkstra.
- Applying topological sort to graphs with cycles.
- Not handling disconnected graphs — need to start BFS/DFS from every unvisited node.

---

## Key Observations

- **Grid problems are graphs** — each cell is a node, adjacent cells are edges.
- Multi-source BFS: add all sources to queue at start with distance 0.
- For connected components: run BFS/DFS from every unvisited node, count iterations.
- Topo sort + DP → solve many DAG optimization problems.

---

*Add notes as you encounter new graph problems and patterns.*
