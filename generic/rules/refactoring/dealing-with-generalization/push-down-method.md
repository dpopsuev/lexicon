---
id: push-down-method
title: "Push Down Method"
description: "A method in the base is used by only one subtype."
labels: [refactoring, dealing-with-generalization]
---

# Push Down Method

**Problem:** A method in the base is used by only one subtype.

**Solution:** Move it to that subtype.

**Mechanics:**
1. Move method to the subtype
2. Remove from base
3. Update references

**Go example:**
```go
// Before: method only relevant to one type
// After: method lives only on the type that needs it
```

**Fixes:** Speculative Generality

**Reference:** https://refactoring.guru/push-down-method
