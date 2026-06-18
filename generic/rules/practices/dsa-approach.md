---
id: dsa-approach
title: DSA Problem Approach
description: A structured workflow for approaching DSA problems — read constraints, brute-force first, name the output, then select. Overwhelm comes from skipping these steps.
labels: [pragmatic, algorithms, practices]
---

# DSA Problem Approach

**DSA problems are reduction problems — translate the description into a known mathematical form before reaching for an algorithm.**

Design patterns are recognition problems: the trigger is visible in the code's structure. DSA triggers are hidden in the problem's properties. The translation step is the hard part, and it is a skill built through exposure, not intelligence.

## Workflow

### 1. Read constraints before the problem statement

Constraints define the required complexity class and eliminate most options before you reason about the problem at all.

| N | Complexity you can afford |
|---|---|
| ≤ 20 | O(2ⁿ) — backtracking, bitmask DP |
| ≤ 1,000 | O(N²) — nested loops, simple DP |
| ≤ 100,000 | O(N log N) — sort, binary search, heap |
| ≤ 1,000,000 | O(N) — single pass, prefix sum, hash map |
| ≤ 10⁹ | O(log N) or O(1) — binary search on answer, math |

If N ≤ 1,000, you do not need a clever algorithm. If N = 10⁹, you need a formula.

### 2. Write the brute force first

Never jump to the optimal solution. The brute force forces you to understand what you are computing, gives a correctness baseline, and reveals *why* it is slow — which tells you *what* to optimize.

### 3. Name the output

The answer type constrains the approach before you classify the shape.

| Output type | Suggests |
|---|---|
| Minimum / maximum value | Greedy or DP |
| Exists or not | BFS / DFS / Union-Find |
| Count of ways | DP on DAG |
| All valid configurations | Backtracking |
| Sorted order with dependencies | Topological Sort |
| k-th element | Heap or Quickselect |

### 4. Apply the two-question framework

Classify shape, identify bottleneck, consult the selection table. See `dsa-selection`.

### 5. Check for monotonicity

Ask: *if I increase X, does the answer only get better (or only worse)?* If yes, binary search on the answer. Many problems that look like search are "find the smallest X such that condition holds" — monotone, binary-searchable in O(log N).

### 6. State the invariant before coding

Every correct algorithm maintains a property throughout execution. Name it explicitly:

- Sliding window: *the window always satisfies the constraint*
- Two pointers: *all pairs to the left have already been considered*
- DP table: *cell [i][j] holds the optimal answer for the subproblem on input i..j*

If you cannot state the invariant, you do not understand the algorithm yet. Do not start coding.

## Rules of Thumb

| Situation | Rule |
|---|---|
| "Find a pair with property P" | Sort first, then two pointers or binary search |
| Scanning left-to-right, need a recent past value | Stack or deque |
| Subproblem answers overlap | DP — memoize, do not recompute |
| Subproblem answers are independent | Divide and conquer |
| Need connectivity or set merging | Union-Find |
| Unweighted / uniform-weight graph, shortest path | BFS |
| Weighted non-negative graph, shortest path | Dijkstra |
| Count paths or ways to reach a state | DP on DAG |
| Problem has a small hidden dimension (≤ 20) | Bitmask DP |
| "Minimum X such that condition holds" | Binary search on the answer |

## Anti-Patterns

- **Jumping to cleverness.** Skipping brute force because the optimal solution feels obvious. It rarely is, and the brute force almost always reveals something.
- **Ignoring constraints.** Designing an O(N log N) solution when N ≤ 100 and O(N²) is fine — and simpler to implement correctly.
- **Coding before stating the invariant.** Writing the loop before you know what it maintains produces bugs that are hard to reason about.
- **Mistaking the surface for the shape.** A problem about "meeting rooms" is an interval scheduling problem. A problem about "islands" is a connected-components problem. Read past the domain language.

## Complements

- `dsa-selection` — once the problem is classified, use the selection table to pick the tool
