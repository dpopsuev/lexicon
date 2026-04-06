---
id: replace-constructor-with-factory-method
title: "Replace Constructor with Factory Method"
description: "Constructor does more than simple initialization."
labels: [refactoring, simplifying-method-calls]
---

# Replace Constructor with Factory Method

**Problem:** Constructor does more than simple initialization.

**Solution:** Replace with a factory function that returns an interface.

**Mechanics:**
1. Create factory function
2. Move construction logic into it
3. Return interface type, not concrete

**Go example:**
```go
// Before
func NewEmployee(kind string) *Employee {
    e := &Employee{kind: kind}
    // complex setup...
    return e
}

// After
func NewEmployee(kind string) Employee {
    switch kind {
    case "engineer": return &Engineer{}
    case "manager": return &Manager{}
    }
}
```

**Fixes:** Switch Statements

**Reference:** https://refactoring.guru/replace-constructor-with-factory-method
