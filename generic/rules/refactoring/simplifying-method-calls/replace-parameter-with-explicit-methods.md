---
id: replace-parameter-with-explicit-methods
title: "Replace Parameter with Explicit Methods"
description: "A method uses a parameter to decide which code path to execute."
labels: [refactoring, simplifying-method-calls]
---

# Replace Parameter with Explicit Methods

**Problem:** A method uses a parameter to decide which code path to execute.

**Solution:** Create separate methods for each path.

**Mechanics:**
1. Create a method for each parameter value
2. Replace conditional with the appropriate method call
3. Remove the parameter

**Go example:**
```go
// Before
func setValue(name string, value int) {
    if name == "height" { height = value }
    if name == "width" { width = value }
}

// After
func setHeight(value int) { height = value }
func setWidth(value int) { width = value }
```

**Fixes:** Switch Statements

**Reference:** https://refactoring.guru/replace-parameter-with-explicit-methods
