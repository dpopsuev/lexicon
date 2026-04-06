---
id: collapse-hierarchy
title: "Collapse Hierarchy"
description: "A subclass is barely different from its parent."
labels: [refactoring, dealing-with-generalization]
---

# Collapse Hierarchy

**Problem:** A subclass is barely different from its parent.

**Solution:** Merge them into one type.

**Mechanics:**
1. Move all fields and methods to one type
2. Remove the empty type
3. Update references

**Go example:**
```go
// Before: Manager barely differs from Employee
// After: merge into Employee with an optional role field
type Employee struct { name string; isManager bool }
```

**Fixes:** Lazy Class, Speculative Generality

**Reference:** https://refactoring.guru/collapse-hierarchy
