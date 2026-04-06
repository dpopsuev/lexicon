---
id: introduce-assertion
title: "Introduce Assertion"
description: "Code assumes a condition is true but doesn't verify it."
labels: [refactoring, simplifying-conditionals]
---

# Introduce Assertion

**Problem:** Code assumes a condition is true but doesn't verify it.

**Solution:** Add an explicit assertion to document and enforce the assumption.

**Mechanics:**
1. Identify the assumption
2. Add assertion that panics or returns error if violated
3. Document why the assertion exists

**Go example:**
```go
// Before: assumes discount is non-negative
result := price * (1 - discount)

// After
if discount < 0 || discount > 1 {
    panic(fmt.Sprintf("invalid discount: %f", discount))
}
result := price * (1 - discount)
```

**Fixes:** Comments

**Reference:** https://refactoring.guru/introduce-assertion
