---
id: introduce-foreign-method
title: "Introduce Foreign Method"
description: "A library class needs an additional method but you can't modify the library."
labels: [refactoring, moving-features]
---

# Introduce Foreign Method

**Problem:** A library class needs an additional method but you can't modify the library.

**Solution:** Create a utility function that takes the library type as its first parameter.

**Mechanics:**
1. Create function in your code
2. Pass library object as first parameter
3. Document that this extends the library type

**Go example:**
```go
// Before: repeated date math
next := date.AddDate(0, 0, 1)

// After: named function
func nextDay(d time.Time) time.Time { return d.AddDate(0, 0, 1) }
```

**Fixes:** Incomplete Library Class

**Reference:** https://refactoring.guru/introduce-foreign-method
