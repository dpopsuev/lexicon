---
id: push-down-field
title: "Push Down Field"
description: "A field in the base is used by only one subtype."
labels: [refactoring, dealing-with-generalization]
---

# Push Down Field

**Problem:** A field in the base is used by only one subtype.

**Solution:** Move it to that subtype.

**Mechanics:**
1. Move field to the subtype that uses it
2. Remove from base
3. Update references

**Go example:**
```go
// Before: quota only used by Salesperson
type Employee struct { quota int }

// After
type Employee struct{}
type Salesperson struct { Employee; quota int }
```

**Fixes:** Speculative Generality

**Reference:** https://refactoring.guru/push-down-field
