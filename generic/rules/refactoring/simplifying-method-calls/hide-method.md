---
id: hide-method
title: "Hide Method"
description: "A method is not used by any other class."
labels: [refactoring, simplifying-method-calls]
---

# Hide Method

**Problem:** A method is not used by any other class.

**Solution:** Make it private/unexported.

**Mechanics:**
1. Verify no external callers exist
2. Make the method unexported (lowercase in Go)
3. Consider if it should be inlined

**Go example:**
```go
// Before
func (o *Order) CalculateDiscount() float64 { ... } // only used internally

// After
func (o *Order) calculateDiscount() float64 { ... }
```

**Fixes:** Dead Code, Speculative Generality

**Reference:** https://refactoring.guru/hide-method
