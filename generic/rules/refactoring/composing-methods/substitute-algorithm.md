---
id: substitute-algorithm
title: "Substitute Algorithm"
description: "An algorithm can be replaced with a clearer or more efficient one."
labels: [refactoring, composing-methods]
---

# Substitute Algorithm

**Problem:** An algorithm can be replaced with a clearer or more efficient one.

**Solution:** Replace the body of the method with the new algorithm.

**Mechanics:**
1. Verify tests cover the current behavior
2. Replace the algorithm implementation
3. Run tests to verify identical behavior

**Go example:**
```go
// Before
func foundPerson(people []string) string {
    for _, p := range people {
        if p == "Don" { return "Don" }
        if p == "John" { return "John" }
    }
    return ""
}

// After
func foundPerson(people []string) string {
    candidates := map[string]bool{"Don": true, "John": true}
    for _, p := range people {
        if candidates[p] { return p }
    }
    return ""
}
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/substitute-algorithm
