---
id: split-temporary-variable
title: "Split Temporary Variable"
description: "A temporary variable is assigned more than once for different purposes."
labels: [refactoring, composing-methods]
---

# Split Temporary Variable

**Problem:** A temporary variable is assigned more than once for different purposes.

**Solution:** Create a separate variable for each assignment.

**Mechanics:**
1. Rename the first assignment to reflect its purpose
2. Create new variables for subsequent assignments
3. Each variable is assigned exactly once

**Go example:**
```go
// Before
temp := 2 * (height + width)
fmt.Println(temp)
temp = height * width
fmt.Println(temp)

// After
perimeter := 2 * (height + width)
fmt.Println(perimeter)
area := height * width
fmt.Println(area)
```

**Fixes:** Long Method

**Reference:** https://refactoring.guru/split-temporary-variable
