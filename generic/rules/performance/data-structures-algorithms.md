---
id: data-structures-algorithms
title: Data Structures & Algorithms
description: Choosing the right structure and algorithm is a design decision with measurable consequences
labels: [performance, algorithms, data-structures]
---

# Data Structures & Algorithms

The choice of data structure determines the ceiling of your algorithm's performance. The choice of algorithm determines whether you reach that ceiling or waste it. Both are design decisions made before writing code, not optimizations applied after.

## Data structure decision framework

Ask three questions before choosing:

1. **What is the dominant operation?** Insert-heavy, lookup-heavy, iteration-heavy, or mixed?
2. **What is the expected size?** Constant factors dominate at small N; asymptotic complexity dominates at large N.
3. **What are the concurrency requirements?** Shared mutable state needs synchronization; the structure choice determines the cost.

### Common structures and their trade-offs

| Structure | Lookup | Insert | Delete | Iterate | When to use |
|-----------|--------|--------|--------|---------|-------------|
| Array/Slice | O(n) | O(1) amortized append | O(n) | O(n), cache-friendly | Default for ordered collections; best cache locality |
| Hash map | O(1) avg | O(1) avg | O(1) avg | O(n), cache-unfriendly | Key-value lookups; uniqueness checks |
| Sorted array + binary search | O(log n) | O(n) | O(n) | O(n), cache-friendly | Read-heavy with rare mutations; range queries |
| Balanced tree (red-black, AVL) | O(log n) | O(log n) | O(log n) | O(n), in-order | Ordered iteration + frequent mutation |
| Heap / priority queue | O(n) find, O(1) min/max | O(log n) | O(log n) | O(n) | Top-K, scheduling, priority-based processing |
| Trie | O(k) key length | O(k) | O(k) | prefix traversal | Prefix matching, autocomplete, IP routing |
| Ring buffer | O(1) | O(1) | O(1) | O(n) | Fixed-size FIFO, streaming windows |
| Bloom filter | O(k) hashes | O(k) hashes | not supported | not supported | Membership test with false positives acceptable |

### The small-N rule

For collections under ~100 elements, a linear scan over a slice often beats a hash map or tree due to cache locality and lower constant factors. Profile before reaching for complex structures.

## Algorithm selection principles

- **Match the problem class.** Sorting? Know when quicksort, mergesort, or radix sort wins. Searching? Binary search requires sorted input. Graph traversal? BFS for shortest path in unweighted graphs, Dijkstra for weighted.
- **Reduce the problem.** Many O(n^2) problems become O(n log n) with sorting as a preprocessing step. Many O(n) search problems become O(1) with a hash set built once.
- **Divide and conquer.** If a problem can be split into independent subproblems, it can often be parallelized. Merge sort, map-reduce, and binary search all exploit this.
- **Recognize dynamic programming.** Overlapping subproblems + optimal substructure = DP candidate. Memoize before reaching for brute force.

## Complexity awareness

Every function touching a collection should have its complexity understood:

```
// Good: complexity is obvious from the code
// O(n) - single pass over items
for _, item := range items { ... }

// Dangerous: hidden O(n^2)
// O(n) outer * O(n) contains check = O(n^2)
for _, item := range items {
    if slices.Contains(other, item.ID) { ... }
}

// Fix: O(n) with a set
set := toSet(other)
for _, item := range items {
    if set[item.ID] { ... }
}
```

## Concurrency-aware structures

- **Copy-on-write** for read-heavy shared data with rare writes.
- **Lock-free queues** for producer-consumer patterns.
- **Sharded maps** when contention on a single lock is measurable.
- **Channels/message passing** when data ownership can be transferred rather than shared.

## Anti-patterns

- Using a linked list when a slice would suffice.
- Building a hash map for a one-time linear scan.
- Nested loops over two collections without considering a join via hash set.
- Sorting a collection to find the min/max (use a single pass).
- Recursive algorithms without considering stack depth limits.
- Choosing a concurrent data structure before proving contention exists.
