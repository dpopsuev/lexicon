---
id: extract-class
title: "Extract Class"
description: "A class does the work of two."
labels: [refactoring, moving-features]
---

# Extract Class

**Problem:** A class does the work of two. It has groups of related fields and methods.

**Solution:** Create a new class and move the relevant fields and methods to it.

**Mechanics:**
1. Identify related fields and methods
2. Create new struct for them
3. Create a link from old to new
4. Move fields and methods one by one

**Go example:**
```go
// Before: Person has phone-related fields
type Person struct {
    Name, AreaCode, Number string
}

// After: extracted PhoneNumber
type Person struct {
    Name  string
    Phone PhoneNumber
}
type PhoneNumber struct { AreaCode, Number string }
```

**Fixes:** Large Class, Divergent Change, Data Clumps

**Reference:** https://refactoring.guru/extract-class
