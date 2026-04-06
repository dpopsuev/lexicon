---
id: remove-parameter
title: "Remove Parameter"
description: "A parameter is no longer used in the method body."
labels: [refactoring, simplifying-method-calls]
---

# Remove Parameter

**Problem:** A parameter is no longer used in the method body.

**Solution:** Remove the unused parameter.

**Mechanics:**
1. Verify no caller depends on the parameter position
2. Remove from signature
3. Remove from all call sites

**Go example:**
```go
// Before
func greet(name string, unused int) string { return "Hi " + name }

// After
func greet(name string) string { return "Hi " + name }
```

**Fixes:** Dead Code, unparam linter

**Reference:** https://refactoring.guru/remove-parameter
