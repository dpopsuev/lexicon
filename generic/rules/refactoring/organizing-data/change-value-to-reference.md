---
id: change-value-to-reference
title: "Change Value to Reference"
description: "Many identical instances exist where one shared instance should."
labels: [refactoring, organizing-data]
---

# Change Value to Reference

**Problem:** Many identical instances exist where one shared instance should.

**Solution:** Replace value copies with references to a single shared object.

**Mechanics:**
1. Create a registry/factory for the object
2. Replace direct construction with registry lookup
3. Ensure identity-based comparison

**Go example:**
```go
// Use registry pattern
var customers = map[string]*Customer{}
func GetCustomer(id string) *Customer { return customers[id] }
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/change-value-to-reference
