---
id: concurrent-programming
title: "Concurrent Programming"
description: "Multiple computations executing during overlapping time periods. Goroutines, channels, synchronization."
labels: [paradigm, concurrent, go]
---

# Concurrent Programming

**Core idea:** Structure a program as multiple independent activities that can execute in overlapping time periods.

## Go's Concurrency Model

> "Don't communicate by sharing memory; share memory by communicating." — Go Proverb

| Primitive | What | When |
|---|---|---|
| **goroutine** | Lightweight thread | Any concurrent work |
| **channel** | Typed communication pipe | Ownership transfer |
| **select** | Multiplex channel operations | Wait on multiple channels |
| **sync.Mutex** | Mutual exclusion | Protect shared state |
| **sync.WaitGroup** | Wait for goroutines to finish | Fan-out/fan-in |
| **sync.Once** | One-time initialization | Lazy singletons |
| **context.Context** | Cancellation propagation | Timeout, deadline |

## Rules

- Every goroutine must have a documented exit condition
- Never hold a lock across I/O or channel operations
- Use `context.Context` for cancellation, not signals
- Prefer channels for ownership transfer, mutex for shared state
- `errgroup.Group` for coordinated fan-out

## Djinn Alignment

- GenSec + 2Sec + Executor = concurrent agents
- Signal bus = concurrent event routing
- Mirage = concurrent filesystem isolation

**Reference:** https://en.wikipedia.org/wiki/Concurrent_computing
