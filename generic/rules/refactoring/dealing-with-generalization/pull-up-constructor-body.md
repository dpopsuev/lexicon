---
id: pull-up-constructor-body
title: "Pull Up Constructor Body"
description: "Constructors in subtypes contain mostly identical code."
labels: [refactoring, dealing-with-generalization]
---

# Pull Up Constructor Body

**Problem:** Constructors in subtypes contain mostly identical code.

**Solution:** Move shared initialization to the base constructor.

**Mechanics:**
1. Identify common initialization
2. Create base constructor
3. Call base from subtypes

**Go example:**
```go
// Before: repeated init in both
// After
func newEmployee(name string, id int) Employee {
    return Employee{name: name, id: id}
}
func NewEngineer(name string, id int) *Engineer {
    return &Engineer{Employee: newEmployee(name, id)}
}
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/pull-up-constructor-body
