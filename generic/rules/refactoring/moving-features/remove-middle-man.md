---
id: remove-middle-man
title: "Remove Middle Man"
description: "A class has too many delegating methods."
labels: [refactoring, moving-features]
---

# Remove Middle Man

**Problem:** A class has too many delegating methods. It's just a pass-through.

**Solution:** Let the client call the delegate directly.

**Mechanics:**
1. Create accessor for the delegate
2. Replace delegating methods with direct calls
3. Remove the delegating methods

**Go example:**
```go
// Before: all methods just delegate
func (p *Person) Manager() *Employee { return p.dept.Manager() }
func (p *Person) Budget() int { return p.dept.Budget() }

// After: expose the delegate
func (p *Person) Department() *Department { return p.dept }
```

**Fixes:** Middle Man

**Reference:** https://refactoring.guru/remove-middle-man
