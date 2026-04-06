---
id: consolidate-conditional-expression
title: "Consolidate Conditional Expression"
description: "Multiple conditionals return the same result."
labels: [refactoring, simplifying-conditionals]
---

# Consolidate Conditional Expression

**Problem:** Multiple conditionals return the same result.

**Solution:** Combine them into a single expression and extract it.

**Mechanics:**
1. Merge conditions with && or ||
2. Extract the combined condition into a named method

**Go example:**
```go
// Before
if age < 18 { return 0 }
if experience < 1 { return 0 }
if !employed { return 0 }

// After
if !isEligible(age, experience, employed) { return 0 }
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/consolidate-conditional-expression
