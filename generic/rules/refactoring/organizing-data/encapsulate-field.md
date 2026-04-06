---
id: encapsulate-field
title: "Encapsulate Field"
description: "Public fields are accessed directly, preventing future changes."
labels: [refactoring, organizing-data]
---

# Encapsulate Field

**Problem:** Public fields are accessed directly, preventing future changes.

**Solution:** Make the field private. Add getter/setter methods.

**Mechanics:**
1. Create getter and setter
2. Make field unexported
3. Update all access points

**Go example:**
```go
// Before
type Person struct { Name string }

// After
type Person struct { name string }
func (p *Person) Name() string { return p.name }
func (p *Person) SetName(n string) { p.name = n }
```

**Fixes:** Data Class

**Reference:** https://refactoring.guru/encapsulate-field
