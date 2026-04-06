---
id: replace-temp-with-query
title: "Replace Temp with Query"
description: "A temporary variable holds the result of an expression used in multiple places."
labels: [refactoring, composing-methods]
---

# Replace Temp with Query

**Problem:** A temporary variable holds the result of an expression used in multiple places.

**Solution:** Extract the expression into a method. Replace the temp with the method call.

**Mechanics:**
1. Extract the expression into its own method
2. Replace all references to the temp with the method call
3. Remove the temp variable

**Go example:**
```go
// Before
basePrice := qty * itemPrice
if basePrice > 1000 { return basePrice * 0.95 }

// After
func (o *Order) basePrice() float64 { return o.Qty * o.ItemPrice }
if o.basePrice() > 1000 { return o.basePrice() * 0.95 }
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/replace-temp-with-query
