---
id: encapsulate-collection
title: "Encapsulate Collection"
description: "A getter returns a mutable collection."
labels: [refactoring, organizing-data]
---

# Encapsulate Collection

**Problem:** A getter returns a mutable collection. Callers modify it directly.

**Solution:** Return a copy or read-only view. Provide add/remove methods.

**Mechanics:**
1. Add add/remove methods for the collection
2. Return a copy from the getter
3. Remove direct collection setter

**Go example:**
```go
// Before
func (c *Course) Students() []*Student { return c.students }

// After
func (c *Course) Students() []*Student {
    out := make([]*Student, len(c.students))
    copy(out, c.students)
    return out
}
func (c *Course) AddStudent(s *Student) { c.students = append(c.students, s) }
```

**Fixes:** Data Class

**Reference:** https://refactoring.guru/encapsulate-collection
