---
id: remove-control-flag
title: "Remove Control Flag"
description: "A boolean variable controls loop flow instead of break/return."
labels: [refactoring, simplifying-conditionals]
---

# Remove Control Flag

**Problem:** A boolean variable controls loop flow instead of break/return.

**Solution:** Use break, continue, or return instead of the flag.

**Mechanics:**
1. Find the control flag assignments
2. Replace with break/continue/return
3. Remove the flag variable

**Go example:**
```go
// Before
found := false
for _, v := range items {
    if v == target { found = true }
}

// After
for _, v := range items {
    if v == target { return v }
}
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/remove-control-flag
