---
id: introduce-null-object
title: "Introduce Null Object"
description: "Repeated nil checks clutter the code."
labels: [refactoring, simplifying-conditionals]
---

# Introduce Null Object

**Problem:** Repeated nil checks clutter the code.

**Solution:** Replace nil with a special null object that implements the interface with default behavior.

**Mechanics:**
1. Create a null implementation of the interface
2. Return null object instead of nil
3. Remove nil checks from callers

**Go example:**
```go
// Before
if customer != nil { plan = customer.Plan() } else { plan = defaultPlan }

// After
type NullCustomer struct{}
func (n NullCustomer) Plan() Plan { return defaultPlan }
// Now: plan = customer.Plan() — always works
```

**Fixes:** Long Method, Switch Statements

**Reference:** https://refactoring.guru/introduce-null-object
