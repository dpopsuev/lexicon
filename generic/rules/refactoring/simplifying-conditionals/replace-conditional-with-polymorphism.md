---
id: replace-conditional-with-polymorphism
title: "Replace Conditional with Polymorphism"
description: "Switch/if-else selects behavior based on type or category."
labels: [refactoring, simplifying-conditionals]
---

# Replace Conditional with Polymorphism

**Problem:** Switch/if-else selects behavior based on type or category.

**Solution:** Create an interface. Each case becomes an implementation.

**Mechanics:**
1. Create interface for the varying behavior
2. Create implementation per case
3. Replace conditional with interface method call

**Go example:**
```go
// Before
switch bird.Type {
case "european": return bird.baseSpeed()
case "african": return bird.baseSpeed() - bird.loadFactor()
}

// After
type Bird interface { Speed() float64 }
type European struct{}
func (e European) Speed() float64 { return e.baseSpeed() }
```

**Fixes:** Switch Statements

**Reference:** https://refactoring.guru/replace-conditional-with-polymorphism
