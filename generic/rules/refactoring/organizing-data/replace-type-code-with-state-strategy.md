---
id: replace-type-code-with-state-strategy
title: "Replace Type Code with State/Strategy"
description: "Type code affects behavior and changes at runtime."
labels: [refactoring, organizing-data]
---

# Replace Type Code with State/Strategy

**Problem:** Type code affects behavior and changes at runtime.

**Solution:** Use State or Strategy pattern instead of type code.

**Mechanics:**
1. Create State/Strategy interface
2. Implement per type code value
3. Object holds State field instead of type code

**Go example:**
```go
// State pattern
type EmployeeType interface { PayAmount(salary float64) float64 }
type Engineer struct{}
func (e Engineer) PayAmount(salary float64) float64 { return salary }
type Manager struct{}
func (m Manager) PayAmount(salary float64) float64 { return salary * 1.5 }
```

**Fixes:** Switch Statements

**Reference:** https://refactoring.guru/replace-type-code-with-state-strategy
