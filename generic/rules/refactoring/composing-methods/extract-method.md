---
id: extract-method
title: "Extract Method"
description: "A code fragment can be grouped together and has a clear purpose."
labels: [refactoring, composing-methods]
---

# Extract Method

**Problem:** A code fragment can be grouped together and has a clear purpose.

**Solution:** Turn the fragment into a method whose name explains the purpose.

**Mechanics:**
1. Create new method with descriptive name
2. Copy fragment to new method
3. Replace fragment with method call
4. Pass needed variables as parameters

**Go example:**
```go
// Before
func process(order Order) {
    // validate
    if order.Amount <= 0 { return }
    if order.Customer == "" { return }
    // ... 20 more lines
}

// After
func process(order Order) {
    if !isValid(order) { return }
    // ...
}
func isValid(o Order) bool {
    return o.Amount > 0 && o.Customer != ""
}
```

**Fixes:** Long Method, Duplicate Code, Comments

**Reference:** https://refactoring.guru/extract-method
