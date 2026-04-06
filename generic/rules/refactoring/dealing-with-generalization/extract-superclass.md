---
id: extract-superclass
title: "Extract Superclass"
description: "Two classes have similar fields and methods."
labels: [refactoring, dealing-with-generalization]
---

# Extract Superclass

**Problem:** Two classes have similar fields and methods.

**Solution:** Extract shared parts into a common type.

**Mechanics:**
1. Identify shared fields and methods
2. Create shared type
3. Embed in both

**Go example:**
```go
// Before: Department and Employee share name+budget
// After
type Entity struct { Name string; Budget float64 }
type Department struct { Entity }
type Employee struct { Entity }
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/extract-superclass
