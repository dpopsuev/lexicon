---
id: change-reference-to-value
title: "Change Reference to Value"
description: "Reference object is small, immutable, and hard to manage lifecycle."
labels: [refactoring, organizing-data]
---

# Change Reference to Value

**Problem:** Reference object is small, immutable, and hard to manage lifecycle.

**Solution:** Make it a value object. Compare by value, not identity.

**Mechanics:**
1. Make the object immutable
2. Remove registry/factory
3. Use value comparison

**Go example:**
```go
// Value object — immutable, compared by value
type Money struct { Amount int; Currency string }
```

**Fixes:** N/A

**Reference:** https://refactoring.guru/change-reference-to-value
