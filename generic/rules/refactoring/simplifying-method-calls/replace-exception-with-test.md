---
id: replace-exception-with-test
title: "Replace Exception with Test"
description: "An exception is used for flow control instead of a condition check."
labels: [refactoring, simplifying-method-calls]
---

# Replace Exception with Test

**Problem:** An exception is used for flow control instead of a condition check.

**Solution:** Replace with a conditional test before the operation.

**Mechanics:**
1. Move the condition check before the operation
2. Remove the try/catch or error check
3. Handle the condition explicitly

**Go example:**
```go
// Before
func value(a []int, i int) int {
    val, err := safeGet(a, i)
    if err != nil { return 0 }
    return val
}

// After
func value(a []int, i int) int {
    if i < 0 || i >= len(a) { return 0 }
    return a[i]
}
```

**Fixes:** N/A

**Reference:** https://refactoring.guru/replace-exception-with-test
