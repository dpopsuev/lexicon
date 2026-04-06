---
id: replace-array-with-object
title: "Replace Array with Object"
description: "An array/slice contains elements of different meanings by position."
labels: [refactoring, organizing-data]
---

# Replace Array with Object

**Problem:** An array/slice contains elements of different meanings by position.

**Solution:** Replace with a struct where each element has a named field.

**Mechanics:**
1. Create struct with named fields
2. Replace index-based access with field access
3. Remove the array

**Go example:**
```go
// Before
row := []string{"Liverpool", "15", "6"}  // name, wins, losses

// After
type TeamRecord struct { Name string; Wins, Losses int }
```

**Fixes:** Primitive Obsession

**Reference:** https://refactoring.guru/replace-array-with-object
