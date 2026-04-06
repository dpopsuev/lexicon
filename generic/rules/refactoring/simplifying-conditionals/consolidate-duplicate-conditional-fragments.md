---
id: consolidate-duplicate-conditional-fragments
title: "Consolidate Duplicate Conditional Fragments"
description: "Same code appears in all branches of a conditional."
labels: [refactoring, simplifying-conditionals]
---

# Consolidate Duplicate Conditional Fragments

**Problem:** Same code appears in all branches of a conditional.

**Solution:** Move the duplicate code outside the conditional.

**Mechanics:**
1. Identify identical code in all branches
2. Move it before or after the conditional
3. Verify behavior is unchanged

**Go example:**
```go
// Before
if isPriority {
    total = price * 0.9
    send(total)
} else {
    total = price
    send(total)
}

// After
total = price
if isPriority { total *= 0.9 }
send(total)
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/consolidate-duplicate-conditional-fragments
