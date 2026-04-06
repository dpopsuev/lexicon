---
id: extract-interface
title: "Extract Interface"
description: "Two classes share a subset of methods."
labels: [refactoring, dealing-with-generalization]
---

# Extract Interface

**Problem:** Two classes share a subset of methods. Callers only need that subset.

**Solution:** Define an interface for the shared methods.

**Mechanics:**
1. Identify common methods
2. Define interface at the consumer
3. Use interface in function signatures

**Go example:**
```go
// Consumer defines what it needs
type Reader interface {
    Read(p []byte) (n int, err error)
}
// Both *File and *Buffer satisfy Reader
```

**Fixes:** Alternative Classes with Different Interfaces, Large Class

**Reference:** https://refactoring.guru/extract-interface
