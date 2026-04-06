---
id: move-method
title: "Move Method"
description: "A method uses features of another class more than its own."
labels: [refactoring, moving-features]
---

# Move Method

**Problem:** A method uses features of another class more than its own.

**Solution:** Create the method in the class it uses most and move the body there.

**Mechanics:**
1. Examine all features used by the method
2. Check which class has most of those features
3. Create new method in that class
4. Move body, adjust references

**Go example:**
```go
// Before: method in Order uses Customer fields heavily
func (o *Order) discount() float64 {
    return o.customer.loyalty * o.customer.years * 0.01
}

// After: method lives with Customer
func (c *Customer) discount() float64 {
    return c.loyalty * c.years * 0.01
}
```

**Fixes:** Feature Envy, Shotgun Surgery

**Reference:** https://refactoring.guru/move-method
