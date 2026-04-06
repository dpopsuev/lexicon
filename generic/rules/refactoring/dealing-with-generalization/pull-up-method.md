---
id: pull-up-method
title: "Pull Up Method"
description: "Two types have identical methods."
labels: [refactoring, dealing-with-generalization]
---

# Pull Up Method

**Problem:** Two types have identical methods.

**Solution:** Move the method to a shared type or extract to a function.

**Mechanics:**
1. Verify methods are identical
2. Move to shared embedded type or standalone function
3. Remove duplicates

**Go example:**
```go
// Before: same method on both types
// After: standalone function or method on embedded type
func fullName(first, last string) string { return first + " " + last }
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/pull-up-method
