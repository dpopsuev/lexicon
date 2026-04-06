---
id: performance-engineering
title: Performance Engineering
description: Data structures, algorithms, and measurement discipline — the third pillar of software quality
labels: [performance, engineering]
---

# Performance Engineering

Performance is not an optimization phase. It is a design discipline that begins with data structure selection and algorithm analysis, and is verified through measurement.

## Data structure selection

Choose data structures based on access patterns, not familiarity.

- **Know the cost model.** Every data structure has a complexity profile for insert, lookup, delete, and iteration. Choose based on the dominant operation in the hot path.
- **Prefer contiguous memory.** Arrays and slices beat linked structures for cache locality in nearly all practical workloads. Reach for maps and trees when access pattern demands it, not by default.
- **Size matters.** A linear scan over 50 elements beats a hash lookup. Know the crossover points for your runtime.
- **Immutability enables sharing.** Immutable structures eliminate defensive copying and synchronization. Default to immutable; mutate only when profiling justifies it.

## Algorithm analysis

- **State complexity upfront.** Every non-trivial function should have its time and space complexity understood before review. O(n) vs O(n log n) vs O(n^2) is a design decision, not a footnote.
- **Worst case is the real case.** Amortized and average-case guarantees matter, but production systems hit worst cases. Design for them.
- **Avoid accidental quadratics.** Nested loops over collections, repeated list searches inside loops, string concatenation in loops — these are the most common performance defects and the easiest to prevent.
- **Trade space for time deliberately.** Caching, memoization, and precomputation are valid strategies when the memory budget allows. Make the trade explicit, not accidental.

## Measurement discipline

- **Benchmark before optimizing.** Intuition about bottlenecks is wrong more often than right. Profile first, then optimize the measured hot path.
- **Benchmark after optimizing.** Every optimization must produce a measurable improvement. If the benchmark doesn't move, revert the change.
- **Micro-benchmarks lie.** They measure isolated operations without cache pressure, GC pauses, or contention. Complement with end-to-end latency measurement under realistic load.
- **Track regressions.** Performance budgets (max latency, max memory, max allocations) are acceptance criteria. Treat a performance regression like a failing test.

## Performance budgets

Define budgets for critical paths:

| Concern | Example budget |
|---------|---------------|
| Response latency | p99 < 100ms |
| Memory per request | < 1MB allocated |
| Startup time | < 500ms to ready |
| Throughput | > 1000 req/s per core |

Budgets are project-specific. The practice of having them is universal.

## Hot path awareness

- **Identify the hot path early.** In most systems, 5% of code handles 95% of traffic. Know which functions are on the hot path before writing them.
- **Allocate outside loops.** Pre-allocate slices, reuse buffers, avoid allocation in tight loops.
- **Minimize syscalls.** Batch I/O, buffer writes, use connection pools. Every syscall is a context switch.
- **Reduce lock scope.** Hold locks for the minimum duration. Prefer lock-free structures or sharding when contention is measurable.

## Anti-patterns

- Optimizing without a benchmark.
- Choosing O(n^2) algorithms for convenience when O(n log n) alternatives exist.
- Defaulting to hash maps for collections under 100 elements.
- Ignoring allocation counts in garbage-collected languages.
- Treating performance as a phase that happens after "it works."
- Premature optimization of cold paths while ignoring hot path allocations.
