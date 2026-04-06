---
id: decompose-conditional
title: "Decompose Conditional"
description: "A complex conditional with long branches obscures the logic."
labels: [refactoring, simplifying-conditionals]
---

# Decompose Conditional

**Problem:** A complex conditional with long branches obscures the logic.

**Solution:** Extract the condition and each branch into separate methods.

**Mechanics:**
1. Extract condition into a named method
2. Extract then-branch into a method
3. Extract else-branch into a method

**Go example:**
```go
// Before
if date.Before(summerStart) || date.After(summerEnd) {
    charge = qty * winterRate + winterServiceCharge
} else {
    charge = qty * summerRate
}

// After
if isSummer(date) {
    charge = summerCharge(qty)
} else {
    charge = winterCharge(qty)
}
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/decompose-conditional
