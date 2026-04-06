---
id: rename-method
title: "Rename Method"
description: "A method name doesn't clearly convey its purpose."
labels: [refactoring, simplifying-method-calls]
---

# Rename Method

**Problem:** A method name doesn't clearly convey its purpose.

**Solution:** Rename it to something that does.

**Mechanics:**
1. Choose a better name
2. Update the method declaration
3. Update all references

**Go example:**
```go
// Before
func (a *Account) dp() float64

// After
func (a *Account) DailyProfit() float64
```

**Fixes:** Comments

**Reference:** https://refactoring.guru/rename-method
