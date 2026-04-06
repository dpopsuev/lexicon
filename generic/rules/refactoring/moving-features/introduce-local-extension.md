---
id: introduce-local-extension
title: "Introduce Local Extension"
description: "A library class needs several additional methods."
labels: [refactoring, moving-features]
---

# Introduce Local Extension

**Problem:** A library class needs several additional methods.

**Solution:** Create a wrapper type or embed the library type, adding your methods.

**Mechanics:**
1. Create new type wrapping or embedding the library type
2. Add the needed methods
3. Replace library type usage with your extension

**Go example:**
```go
// Before: many helper functions for time.Time

// After: local extension
type DateTime struct { time.Time }
func (d DateTime) NextDay() DateTime { return DateTime{d.AddDate(0, 0, 1)} }
func (d DateTime) IsWeekend() bool { ... }
```

**Fixes:** Incomplete Library Class

**Reference:** https://refactoring.guru/introduce-local-extension
