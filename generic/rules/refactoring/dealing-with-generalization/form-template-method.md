---
id: form-template-method
title: "Form Template Method"
description: "Two methods in different types follow the same steps but with different details."
labels: [refactoring, dealing-with-generalization]
---

# Form Template Method

**Problem:** Two methods in different types follow the same steps but with different details.

**Solution:** Create a template with abstract steps.

**Mechanics:**
1. Identify the shared algorithm skeleton
2. Define step functions (or function fields)
3. Each type provides its own step implementations

**Go example:**
```go
// Template with pluggable steps
type Report struct {
    Header func() string
    Body   func() string
    Footer func() string
}
func (r Report) Generate() string {
    return r.Header() + r.Body() + r.Footer()
}
```

**Fixes:** Duplicate Code

**Reference:** https://refactoring.guru/form-template-method
