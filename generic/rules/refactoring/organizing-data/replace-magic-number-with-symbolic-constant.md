---
id: replace-magic-number-with-symbolic-constant
title: "Replace Magic Number with Symbolic Constant"
description: "Code uses literal numbers whose meaning isn't obvious."
labels: [refactoring, organizing-data]
---

# Replace Magic Number with Symbolic Constant

**Problem:** Code uses literal numbers whose meaning isn't obvious.

**Solution:** Replace with a named constant.

**Mechanics:**
1. Declare a constant with a descriptive name
2. Replace all occurrences of the literal
3. Consider using typed constants

**Go example:**
```go
// Before
if speed > 120 { ... }

// After
const maxHighwaySpeed = 120
if speed > maxHighwaySpeed { ... }
```

**Fixes:** Primitive Obsession

**Reference:** https://refactoring.guru/replace-magic-number-with-symbolic-constant
