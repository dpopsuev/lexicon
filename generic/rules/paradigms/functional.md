---
id: functional-programming
title: "Functional Programming"
description: "Pure functions, immutable data, composition. No side effects, no shared state."
labels: [paradigm, functional, design]
---

# Functional Programming

**Core idea:** Programs are composed of pure functions. No side effects, no mutable state, no shared state.

## Key Concepts

| Concept | What | Go Equivalent |
|---|---|---|
| **Pure functions** | Same input → same output, no side effects | Functions that don't modify receivers or globals |
| **Immutability** | Data never changes, create new copies | Value types, return new structs instead of mutating |
| **First-class functions** | Functions as values, passed as arguments | `func` types, closures, function fields |
| **Higher-order functions** | Functions that take/return functions | Middleware: `func(next Handler) Handler` |
| **Composition** | Build complex from simple | Pipeline: `f(g(x))`, or method chaining |
| **No side effects** | Function doesn't change the world | Separate I/O from logic |

## Go's Functional Features

- Closures and anonymous functions
- Functions as first-class values
- `slices.Map`, `slices.Filter` (Go 1.21+)
- Functional options pattern: `WithTimeout(d)`

## When to Use in Go

- Data transformation pipelines
- Configuration (functional options)
- Middleware chains (decorators)
- Event handlers (callbacks)

**Reference:** https://en.wikipedia.org/wiki/Functional_programming
