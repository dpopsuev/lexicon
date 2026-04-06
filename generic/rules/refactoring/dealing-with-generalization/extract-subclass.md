---
id: extract-subclass
title: "Extract Subclass"
description: "A class has features used only in some instances."
labels: [refactoring, dealing-with-generalization]
---

# Extract Subclass

**Problem:** A class has features used only in some instances.

**Solution:** Create a subtype for the special cases.

**Mechanics:**
1. Identify features used only sometimes
2. Create new type for those features
3. Move special features to new type

**Go example:**
```go
// Before: Employee has engineerSkills used only by some
// After
type Employee struct { name string }
type Engineer struct { Employee; skills []string }
```

**Fixes:** Large Class

**Reference:** https://refactoring.guru/extract-subclass
