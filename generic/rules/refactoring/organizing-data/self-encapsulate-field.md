---
id: self-encapsulate-field
title: "Self Encapsulate Field"
description: "Direct field access makes it hard to add validation or computed values."
labels: [refactoring, organizing-data]
---

# Self Encapsulate Field

**Problem:** Direct field access makes it hard to add validation or computed values.

**Solution:** Access fields through getter/setter methods, even within the class.

**Mechanics:**
1. Create getter and setter methods
2. Replace direct access with method calls
3. Add validation or computation in methods

**Go example:**
```go
// Before: direct access
func (o *Order) total() float64 { return o.quantity * o.price }

// After: self-encapsulated
func (o *Order) Quantity() int { return o.quantity }
func (o *Order) total() float64 { return float64(o.Quantity()) * o.Price() }
```

**Fixes:** N/A

**Reference:** https://refactoring.guru/self-encapsulate-field
