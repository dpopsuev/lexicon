---
id: introduce-parameter-object
title: "Introduce Parameter Object"
description: "Same group of parameters appears in multiple methods."
labels: [refactoring, simplifying-method-calls]
---

# Introduce Parameter Object

**Problem:** Same group of parameters appears in multiple methods.

**Solution:** Replace them with an object that groups them.

**Mechanics:**
1. Create struct for the parameter group
2. Update method signatures
3. Move behavior into the new struct if appropriate

**Go example:**
```go
// Before
func amountInRange(start, end time.Time, min, max float64) float64

// After
type DateRange struct { Start, End time.Time }
type AmountRange struct { Min, Max float64 }
func amountInRange(dates DateRange, amounts AmountRange) float64
```

**Fixes:** Long Parameter List, Data Clumps

**Reference:** https://refactoring.guru/introduce-parameter-object
