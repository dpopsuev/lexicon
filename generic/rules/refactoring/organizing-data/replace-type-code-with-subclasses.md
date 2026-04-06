---
id: replace-type-code-with-subclasses
title: "Replace Type Code with Subclasses"
description: "Type code affects behavior."
labels: [refactoring, organizing-data]
---

# Replace Type Code with Subclasses

**Problem:** Type code affects behavior. Different types need different methods.

**Solution:** Create an interface with implementations per type.

**Mechanics:**
1. Define interface for the varying behavior
2. Create implementation per type code value
3. Replace switch on type code with polymorphism

**Go example:**
```go
// Before: switch on type
switch e.Type { case "engineer": ...; case "manager": ... }

// After: interface
type Employee interface { Bonus() float64 }
type Engineer struct{}
func (e Engineer) Bonus() float64 { return e.salary * 0.1 }
```

**Fixes:** Switch Statements

**Reference:** https://refactoring.guru/replace-type-code-with-subclasses
