---
id: dsa-selection
title: DSA Selection
description: Choose a data structure or algorithm by answering two questions — what is the shape of the problem, and what is the bottleneck operation?
labels: [pragmatic, algorithms, practices]
---

# DSA Selection

**The right algorithm follows from two questions: what is the shape of the problem, and what is the bottleneck operation?**

Shape narrows the candidate set. Bottleneck makes the final selection.

## The Two Questions

| Question | Options |
|---|---|
| **Shape** — what structure does the input take? | array, graph, tree, string, set |
| **Bottleneck** — what operation must be fast? | lookup, ordering, range query, shortest path, counting, connectivity |

## Decision Tables

### Arrays & Strings

| Problem property | Tool |
|---|---|
| Sorted input, find a value | Binary Search |
| Sorted input, find a pair with constraint | Two Pointers |
| Contiguous subarray with constraint | Sliding Window |
| Any-index pair, fast lookup | HashMap |
| Range sums, no updates | Prefix Sum |
| Next greater / smaller element | Monotonic Stack |
| Sliding window min / max | Monotonic Deque |
| Prefix matching across many strings | Trie |
| Substring search / pattern match | KMP / Rolling Hash |

### Graphs

| Problem property | Tool |
|---|---|
| Shortest path, unweighted | BFS |
| Shortest path, non-negative weights | Dijkstra |
| Shortest path, negative edges exist | Bellman-Ford |
| All-pairs shortest paths | Floyd-Warshall |
| Connected components | Union-Find or BFS/DFS |
| Task ordering with dependencies | Topological Sort |
| Minimum spanning tree, sparse graph | Kruskal |
| Minimum spanning tree, dense graph | Prim |
| Cycle detection | DFS coloring |

### Trees

| Problem property | Tool |
|---|---|
| Aggregate over subtree | DFS post-order |
| Path from root to node | DFS pre-order |
| Level-by-level traversal | BFS |
| Lowest common ancestor, many queries | Binary Lifting |
| Range query on tree path | Euler Tour + Segment Tree |

### Dynamic Programming

| Problem property | DP shape |
|---|---|
| Single sequence, local choices | 1D DP |
| Two sequences (LCS, edit distance) | 2D DP |
| Partition into intervals | Interval DP |
| On subsets of a small set | Bitmask DP |
| On a tree | Tree DP (post-order) |
| Count ways / paths | DP on DAG |

### Data Structure Selection

| You need to… | Reach for |
|---|---|
| O(1) lookup by key | HashMap |
| Repeatedly get the min or max | Heap |
| Sorted iteration + fast insert/delete | Balanced BST |
| Range sum, point update | Fenwick Tree |
| Range query, range update | Segment Tree |
| Merge sets, check connectivity | Union-Find |

## Where It Gets Fuzzy

Shape → tool is clean for **paradigm selection**. It blurs for **data structure selection**, where the axis is implementation complexity vs. generality:

| Choice | Trade-off |
|---|---|
| Fenwick vs. Segment Tree | Fenwick: simpler, point update only. Segment: range update, more code. |
| Union-Find vs. BFS | Union-Find: O(α) per query, online. BFS: single-pass, offline, richer info. |
| Dijkstra vs. A\* | Same algorithm; A\* adds a goal heuristic when the target is known. |
| Kruskal vs. Prim | Edge count drives the choice, not problem shape. |

## Anti-Patterns

- **Reaching for a hammer.** Applying a familiar tool (DFS, HashMap) before classifying shape. Shape comes first.
- **Skipping the bottleneck question.** Choosing a data structure by intuition rather than by what operation must be fast.
- **Over-engineering the structure.** Using a Segment Tree when a Prefix Sum array suffices. Match the simplest tool that satisfies the bottleneck.

## Complements

- `papert-paradigm` — the selected tool should also be approachable (low floor) and extensible (high ceiling)
