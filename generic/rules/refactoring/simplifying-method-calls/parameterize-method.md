---
id: parameterize-method
title: "Parameterize Method"
description: "Multiple methods do similar things with different hardcoded values."
labels: [refactoring, simplifying-method-calls]
---

# Parameterize Method

**Problem:** Multiple methods do similar things with different hardcoded values.

**Solution:** Create one method with a parameter for the varying value.

**Mechanics:**
1. Identify the varying value
2. Create parameterized method
3. Replace specialized methods with calls to the parameterized one

**Go example:**
```go
// Before
func tenPercentRaise() { ... }
func fivePercentRaise() { ... }

// After
func raise(percent float64) { ... }
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/parameterize-method
