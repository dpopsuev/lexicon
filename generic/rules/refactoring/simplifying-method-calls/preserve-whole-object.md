---
id: preserve-whole-object
title: "Preserve Whole Object"
description: "Multiple values are extracted from an object just to pass them as parameters."
labels: [refactoring, simplifying-method-calls]
---

# Preserve Whole Object

**Problem:** Multiple values are extracted from an object just to pass them as parameters.

**Solution:** Pass the whole object instead.

**Mechanics:**
1. Replace individual field parameters with the object
2. Update method to access fields from the object
3. Remove the extracted variables

**Go example:**
```go
// Before
low := room.TempRange().Low
high := room.TempRange().High
plan.WithinRange(low, high)

// After
plan.WithinRange(room.TempRange())
```

**Fixes:** Long Parameter List, Data Clumps, Feature Envy

**Reference:** https://refactoring.guru/preserve-whole-object
