---
id: replace-subclass-with-fields
title: "Replace Subclass with Fields"
description: "Subclasses differ only in constant-returning methods."
labels: [refactoring, organizing-data]
---

# Replace Subclass with Fields

**Problem:** Subclasses differ only in constant-returning methods.

**Solution:** Replace subclasses with fields in the parent.

**Mechanics:**
1. Add fields to parent for each differing value
2. Set fields in constructor
3. Remove subclasses

**Go example:**
```go
// Before: separate types just for constant values
type Male struct{}
func (m Male) Code() string { return "M" }
type Female struct{}
func (f Female) Code() string { return "F" }

// After: just a field
type Person struct { genderCode string }
func NewMale() Person { return Person{genderCode: "M"} }
```

**Fixes:** Lazy Class, Speculative Generality

**Reference:** https://refactoring.guru/replace-subclass-with-fields
