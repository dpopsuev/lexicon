---
id: replace-method-with-method-object
title: "Replace Method with Method Object"
description: "A long method uses many local variables making extraction impossible."
labels: [refactoring, composing-methods]
---

# Replace Method with Method Object

**Problem:** A long method uses many local variables making extraction impossible.

**Solution:** Transform the method into a separate struct where locals become fields.

**Mechanics:**
1. Create new struct with fields for each local and parameter
2. Copy method body into the struct's Compute() method
3. Replace original method with struct creation + Compute() call

**Go example:**
```go
// Before
func (o *Order) price() float64 {
    primaryBase := ...
    secondaryBase := ...
    tertiaryBase := ...
    // complex calculation using all three
}

// After
type priceCalculator struct {
    order *Order
    primaryBase, secondaryBase, tertiaryBase float64
}
func (c *priceCalculator) compute() float64 { ... }
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/replace-method-with-method-object
