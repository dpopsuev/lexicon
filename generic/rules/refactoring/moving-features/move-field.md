---
id: move-field
title: "Move Field"
description: "A field is used more by another class than by its own class."
labels: [refactoring, moving-features]
---

# Move Field

**Problem:** A field is used more by another class than by its own class.

**Solution:** Move the field to the class that uses it most.

**Mechanics:**
1. Create field in target class
2. Update all references to use the new location
3. Remove old field

**Go example:**
```go
// Before: discount rate stored in Order, used by Customer
type Order struct { discountRate float64 }

// After: discount rate lives with Customer
type Customer struct { discountRate float64 }
```

**Fixes:** Feature Envy, Data Clumps

**Reference:** https://refactoring.guru/move-field
