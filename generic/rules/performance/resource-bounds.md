---
id: resource-bounds
title: Resource-Bound Taxonomy
description: Classify what limits a workload before choosing a language, concurrency model, or architecture.
labels: [performance, architecture, systems]
---

# Resource-Bound Taxonomy

**Identify the binding resource before choosing a language, concurrency model, or architecture.** A solution optimised for the wrong bound wastes complexity without improving performance.

## Taxonomy

| Bound | Limited by | Signals |
|---|---|---|
| **CPU-bound** (compute-bound) | Processor cycles | High CPU utilisation; adding cores helps; faster clock helps |
| **Memory-bound** | RAM bandwidth or capacity; Von Neumann bottleneck | Cache miss rate high; data movement dominates compute time |
| **I/O-bound** | Waiting for input/output | CPU idle while waiting; threads blocked on syscalls |
| **Disk-bound** | Storage throughput or seek latency | IOPS saturated; sequential vs random access pattern matters |
| **Network-bound** | Bandwidth or round-trip latency | Throughput limited by link speed or time waiting for remote response |

Disk-bound and network-bound are subcategories of I/O-bound.

## What improves performance per bound

| Bound | Lever |
|---|---|
| **CPU-bound** | Faster algorithm, SIMD, parallelism across cores |
| **Memory-bound** | Cache-friendly layout, smaller working set, avoid pointer chasing |
| **I/O-bound** | Async I/O, connection pooling, batching, fewer round-trips |
| **Disk-bound** | Sequential access, larger buffers, memory-mapped files |
| **Network-bound** | Compression, multiplexing, locality, protocol choice (HTTP/2, gRPC) |

Adding CPU cores to a network-bound workload does nothing. Optimising algorithms in a disk-bound hot path does nothing. Identify the constraint first, then apply the correct lever.

## References

- https://en.wikipedia.org/wiki/CPU-bound
- https://en.wikipedia.org/wiki/I/O_bound
- https://en.wikipedia.org/wiki/Memory_bound_function
