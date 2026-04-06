---
id: hide-delegate
title: "Hide Delegate"
description: "Client calls a method on an object returned by another object (message chain)."
labels: [refactoring, moving-features]
---

# Hide Delegate

**Problem:** Client calls a method on an object returned by another object (message chain).

**Solution:** Create a delegating method on the server to hide the delegate.

**Mechanics:**
1. Create delegate method on the intermediary
2. Client calls intermediary instead of navigating the chain
3. Repeat for each chain

**Go example:**
```go
// Before
manager := person.Department().Manager()

// After
manager := person.Manager()
func (p *Person) Manager() *Employee { return p.dept.Manager() }
```

**Fixes:** Message Chains

**Reference:** https://refactoring.guru/hide-delegate
