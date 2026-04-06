---
id: change-bidirectional-association-to-unidirectional
title: "Change Bidirectional Association to Unidirectional"
description: "Bidirectional association is unnecessarily complex."
labels: [refactoring, organizing-data]
---

# Change Bidirectional Association to Unidirectional

**Problem:** Bidirectional association is unnecessarily complex. One side doesn't need the link.

**Solution:** Remove the unnecessary direction.

**Mechanics:**
1. Determine which side doesn't need the reference
2. Remove the back-reference
3. Pass the object as a parameter where needed

**Go example:**
```go
// Remove unnecessary back-reference
// Before: Customer has orders AND Order has customer
// After: only Order has customer; pass orders list when needed
```

**Fixes:** Inappropriate Intimacy

**Reference:** https://refactoring.guru/change-bidirectional-association-to-unidirectional
