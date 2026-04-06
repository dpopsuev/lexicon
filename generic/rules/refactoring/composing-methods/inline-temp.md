---
id: inline-temp
title: "Inline Temp"
description: "A temp variable is assigned once from a simple expression and used once."
labels: [refactoring, composing-methods]
---

# Inline Temp

**Problem:** A temp variable is assigned once from a simple expression and used once.

**Solution:** Replace the variable reference with the expression itself.

**Mechanics:**
1. Verify the variable is assigned exactly once
2. Replace all references with the expression
3. Remove the variable declaration

**Go example:**
```go
// Before
basePrice := order.BasePrice()
return basePrice > 1000

// After
return order.BasePrice() > 1000
```

**Fixes:** Dead Code

**Reference:** https://refactoring.guru/inline-temp
