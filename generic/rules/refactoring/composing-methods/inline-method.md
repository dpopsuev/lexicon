---
id: inline-method
title: "Inline Method"
description: "A method body is as clear as its name."
labels: [refactoring, composing-methods]
---

# Inline Method

**Problem:** A method body is as clear as its name. The indirection adds no value.

**Solution:** Replace method calls with the method body and remove the method.

**Mechanics:**
1. Verify method is not overridden/polymorphic
2. Replace all calls with method body
3. Delete the method

**Go example:**
```go
// Before
func isEligible(age int) bool { return age >= 18 }
if isEligible(user.Age) { ... }

// After
if user.Age >= 18 { ... }
```

**Fixes:** Speculative Generality, Middle Man

**Reference:** https://refactoring.guru/inline-method
