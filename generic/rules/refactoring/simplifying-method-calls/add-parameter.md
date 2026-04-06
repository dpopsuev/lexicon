---
id: add-parameter
title: "Add Parameter"
description: "A method needs additional data to do its job."
labels: [refactoring, simplifying-method-calls]
---

# Add Parameter

**Problem:** A method needs additional data to do its job.

**Solution:** Add a parameter for the needed data.

**Mechanics:**
1. Add parameter to signature
2. Update all callers
3. Use the new parameter in the body

**Go example:**
```go
// Before
func (c *Contact) Phone() string

// After
func (c *Contact) Phone(kind string) string
```

**Fixes:** N/A

**Reference:** https://refactoring.guru/add-parameter
