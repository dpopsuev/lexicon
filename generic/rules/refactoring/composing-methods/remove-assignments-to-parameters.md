---
id: remove-assignments-to-parameters
title: "Remove Assignments to Parameters"
description: "A function parameter is reassigned inside the function body."
labels: [refactoring, composing-methods]
---

# Remove Assignments to Parameters

**Problem:** A function parameter is reassigned inside the function body.

**Solution:** Use a local variable instead of reassigning the parameter.

**Mechanics:**
1. Create a local variable
2. Replace parameter references after the assignment with the local
3. Assign the local variable instead of the parameter

**Go example:**
```go
// Before
func discount(input int) int {
    if input > 50 { input -= 2 }
    return input
}

// After
func discount(input int) int {
    result := input
    if result > 50 { result -= 2 }
    return result
}
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/remove-assignments-to-parameters
