---
id: separate-query-from-modifier
title: "Separate Query from Modifier"
description: "A method both returns a value and changes state."
labels: [refactoring, simplifying-method-calls]
---

# Separate Query from Modifier

**Problem:** A method both returns a value and changes state.

**Solution:** Split into two methods: one that queries, one that modifies.

**Mechanics:**
1. Create a query method that returns the value
2. Create a command method that modifies state
3. Replace original with appropriate calls

**Go example:**
```go
// Before
func (s *Security) NextAlertAndRemove() string {
    alert := s.alerts[0]
    s.alerts = s.alerts[1:]
    return alert
}

// After
func (s *Security) NextAlert() string { return s.alerts[0] }
func (s *Security) RemoveAlert() { s.alerts = s.alerts[1:] }
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/separate-query-from-modifier
