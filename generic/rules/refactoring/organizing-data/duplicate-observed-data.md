---
id: duplicate-observed-data
title: "Duplicate Observed Data"
description: "Domain data stored only in UI/presentation layer."
labels: [refactoring, organizing-data]
---

# Duplicate Observed Data

**Problem:** Domain data stored only in UI/presentation layer.

**Solution:** Copy the data to a domain object. Set up sync between them.

**Mechanics:**
1. Create domain object for the data
2. Use observer to sync UI and domain
3. Redirect logic to use domain object

**Go example:**
```go
// Separate domain from presentation
type TemperatureModel struct { Celsius float64 }
func (t *TemperatureModel) Fahrenheit() float64 { return t.Celsius*1.8 + 32 }
```

**Fixes:** Large Class

**Reference:** https://refactoring.guru/duplicate-observed-data
