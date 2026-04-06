---
id: replace-nested-conditional-with-guard-clauses
title: "Replace Nested Conditional with Guard Clauses"
description: "Deep nesting makes the normal path hard to find."
labels: [refactoring, simplifying-conditionals]
---

# Replace Nested Conditional with Guard Clauses

**Problem:** Deep nesting makes the normal path hard to find.

**Solution:** Use early returns for special cases. Keep the happy path un-nested.

**Mechanics:**
1. Identify special cases that can return early
2. Replace nested else with guard clause (early return)
3. Main logic stays at top indent level

**Go example:**
```go
// Before
func pay(employee Employee) float64 {
    if employee.IsSeparated {
        result = separatedPay(employee)
    } else {
        if employee.IsRetired {
            result = retiredPay(employee)
        } else {
            result = normalPay(employee)
        }
    }
    return result
}

// After
func pay(e Employee) float64 {
    if e.IsSeparated { return separatedPay(e) }
    if e.IsRetired { return retiredPay(e) }
    return normalPay(e)
}
```

**Fixes:** Long Method, nestif linter

**Reference:** https://refactoring.guru/replace-nested-conditional-with-guard-clauses
