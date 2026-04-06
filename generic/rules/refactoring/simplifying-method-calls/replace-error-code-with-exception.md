---
id: replace-error-code-with-exception
title: "Replace Error Code with Exception"
description: "A method returns a special value (-1, nil, "") to indicate failure."
labels: [refactoring, simplifying-method-calls]
---

# Replace Error Code with Exception

**Problem:** A method returns a special value (-1, nil, "") to indicate failure.

**Solution:** Return an error instead of a magic value.

**Mechanics:**
1. Define an error type or sentinel
2. Return (value, error) tuple
3. Replace magic value checks with error checks

**Go example:**
```go
// Before
func withdraw(amount int) int {
    if amount > balance { return -1 }
    balance -= amount
    return balance
}

// After
var ErrInsufficientFunds = errors.New("insufficient funds")
func withdraw(amount int) (int, error) {
    if amount > balance { return 0, ErrInsufficientFunds }
    balance -= amount
    return balance, nil
}
```

**Fixes:** Primitive Obsession

**Reference:** https://refactoring.guru/replace-error-code-with-exception
