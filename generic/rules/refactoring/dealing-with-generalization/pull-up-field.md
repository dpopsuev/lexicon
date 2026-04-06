---
id: pull-up-field
title: "Pull Up Field"
description: "Two structs have the same field."
labels: [refactoring, dealing-with-generalization]
---

# Pull Up Field

**Problem:** Two structs have the same field.

**Solution:** Move the field to a shared embedded struct.

**Mechanics:**
1. Create or identify shared base struct
2. Move field to base
3. Embed base in both structs

**Go example:**
```go
// Before
type Engineer struct { name string }
type Manager struct { name string }

// After
type Employee struct { name string }
type Engineer struct { Employee }
type Manager struct { Employee }
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/pull-up-field
