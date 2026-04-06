---
id: inline-class
title: "Inline Class"
description: "A class does too little to justify its existence."
labels: [refactoring, moving-features]
---

# Inline Class

**Problem:** A class does too little to justify its existence.

**Solution:** Move all features into another class and delete the empty class.

**Mechanics:**
1. Move all fields and methods to the absorbing class
2. Update references
3. Delete the empty class

**Go example:**
```go
// Before: PhoneNumber struct with one field
type PhoneNumber struct { Number string }

// After: inlined into Person
type Person struct { Name, Phone string }
```

**Fixes:** Lazy Class, Speculative Generality

**Reference:** https://refactoring.guru/inline-class
