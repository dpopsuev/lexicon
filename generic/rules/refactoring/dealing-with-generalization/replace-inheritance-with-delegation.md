---
id: replace-inheritance-with-delegation
title: "Replace Inheritance with Delegation"
description: "A subclass uses only a fraction of the parent's interface."
labels: [refactoring, dealing-with-generalization]
---

# Replace Inheritance with Delegation

**Problem:** A subclass uses only a fraction of the parent's interface.

**Solution:** Remove embedding. Hold a reference and delegate specific methods.

**Mechanics:**
1. Create field for the former parent
2. Delegate needed methods
3. Remove embedding

**Go example:**
```go
// Before: Stack embeds all of List
type Stack struct { list.List }

// After: Stack delegates to List
type Stack struct { data *list.List }
func (s *Stack) Push(v any) { s.data.PushBack(v) }
func (s *Stack) Pop() any { ... }
```

**Fixes:** Refused Bequest

**Reference:** https://refactoring.guru/replace-inheritance-with-delegation
