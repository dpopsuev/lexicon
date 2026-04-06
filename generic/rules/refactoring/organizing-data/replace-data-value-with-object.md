---
id: replace-data-value-with-object
title: "Replace Data Value with Object"
description: "A data field needs additional behavior or data beyond a simple value."
labels: [refactoring, organizing-data]
---

# Replace Data Value with Object

**Problem:** A data field needs additional behavior or data beyond a simple value.

**Solution:** Replace the field with a rich object that encapsulates the behavior.

**Mechanics:**
1. Create a new class for the value
2. Add behavior to the new class
3. Replace the raw field with the new type

**Go example:**
```go
// Before
type Order struct { CustomerName string }

// After
type Order struct { Customer Customer }
type Customer struct { Name string; Loyalty int }
```

**Fixes:** Primitive Obsession, Data Clumps

**Reference:** https://refactoring.guru/replace-data-value-with-object
