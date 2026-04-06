---
id: replace-type-code-with-class
title: "Replace Type Code with Class"
description: "A field uses raw int/string for type code with no type safety."
labels: [refactoring, organizing-data]
---

# Replace Type Code with Class

**Problem:** A field uses raw int/string for type code with no type safety.

**Solution:** Replace with a named type or enum.

**Mechanics:**
1. Create named type
2. Define constants for valid values
3. Replace raw values with typed constants

**Go example:**
```go
// Before
const TypeA = 0; TypeB = 1

// After
type BloodType int
const (
    BloodTypeA BloodType = iota
    BloodTypeB
)
```

**Fixes:** Primitive Obsession, Switch Statements

**Reference:** https://refactoring.guru/replace-type-code-with-class
