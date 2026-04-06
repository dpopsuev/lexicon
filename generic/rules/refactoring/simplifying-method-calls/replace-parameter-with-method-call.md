---
id: replace-parameter-with-method-call
title: "Replace Parameter with Method Call"
description: "A caller computes a value just to pass it as a parameter."
labels: [refactoring, simplifying-method-calls]
---

# Replace Parameter with Method Call

**Problem:** A caller computes a value just to pass it as a parameter.

**Solution:** Let the method compute the value itself.

**Mechanics:**
1. Move the computation inside the method
2. Remove the parameter
3. Update all callers

**Go example:**
```go
// Before
basePrice := qty * itemPrice
seasonDiscount := season.Discount()
finalPrice := discountedPrice(basePrice, seasonDiscount)

// After
finalPrice := discountedPrice(qty, itemPrice, season)
```

**Fixes:** Long Parameter List

**Reference:** https://refactoring.guru/replace-parameter-with-method-call
