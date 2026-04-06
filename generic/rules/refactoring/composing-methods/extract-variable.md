---
id: extract-variable
title: "Extract Variable"
description: "A complex expression is hard to understand."
labels: [refactoring, composing-methods]
---

# Extract Variable

**Problem:** A complex expression is hard to understand.

**Solution:** Place the result of the expression in a variable that explains its purpose.

**Mechanics:**
1. Identify the complex expression
2. Assign it to a well-named variable
3. Replace the expression with the variable

**Go example:**
```go
// Before
if order.Total() - order.Discount() > 100 && order.IsPriority() { ... }

// After
netAmount := order.Total() - order.Discount()
isLargeOrder := netAmount > 100 && order.IsPriority()
if isLargeOrder { ... }
```

**Fixes:** Comments, Long Method

**Reference:** https://refactoring.guru/extract-variable
