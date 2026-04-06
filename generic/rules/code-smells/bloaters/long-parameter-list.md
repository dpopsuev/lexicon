---
id: long-parameter-list
title: "Long Parameter List"
description: "Hard to call correctly."
labels: [code-smells, bloaters]
---

# Long Parameter List

**Signs:**
- Function takes >4 parameters
- Multiple parameters of the same type
- Boolean parameters (flag arguments)

**Why it's bad:** Hard to call correctly. Easy to swap arguments. Impossible to extend.

**Treatment:**
- Introduce Parameter Object — group related params into a struct
- Preserve Whole Object — pass the struct instead of its fields
- Replace Parameter with Method Call — derive values internally

**Detection (Go):**
- >4 parameters on any function
- `gocritic hugeParam`: struct parameter >256 bytes

**SOLID violation:** ISP — interface is too wide for most callers

**Reference:** https://refactoring.guru/smells/long-parameter-list
