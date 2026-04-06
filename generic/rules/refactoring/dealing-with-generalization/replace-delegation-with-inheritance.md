---
id: replace-delegation-with-inheritance
title: "Replace Delegation with Inheritance"
description: "A class delegates everything to another and adds nothing."
labels: [refactoring, dealing-with-generalization]
---

# Replace Delegation with Inheritance

**Problem:** A class delegates everything to another and adds nothing.

**Solution:** Use embedding instead of delegation.

**Mechanics:**
1. Make the delegating type embed the delegate
2. Remove delegation methods
3. Clients access delegate methods directly

**Go example:**
```go
// Before: all methods delegate
type Engine struct { inner *Motor }
func (e *Engine) Start() { e.inner.Start() }

// After: embed
type Engine struct { *Motor }
```

**Fixes:** Middle Man, Lazy Class

**Reference:** https://refactoring.guru/replace-delegation-with-inheritance
