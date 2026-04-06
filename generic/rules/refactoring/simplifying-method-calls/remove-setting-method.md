---
id: remove-setting-method
title: "Remove Setting Method"
description: "A field should be set at creation and never changed."
labels: [refactoring, simplifying-method-calls]
---

# Remove Setting Method

**Problem:** A field should be set at creation and never changed.

**Solution:** Remove the setter. Set the field only in the constructor.

**Mechanics:**
1. Remove the setter method
2. Make the field unexported
3. Set the value only in the constructor

**Go example:**
```go
// Before
type Account struct { id string }
func (a *Account) SetID(id string) { a.id = id }

// After
type Account struct { id string }
func NewAccount(id string) *Account { return &Account{id: id} }
```

**Fixes:** Temporary Field

**Reference:** https://refactoring.guru/remove-setting-method
