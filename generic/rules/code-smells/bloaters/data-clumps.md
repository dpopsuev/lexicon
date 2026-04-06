---
id: data-clumps
title: "Data Clumps"
description: "Duplication."
labels: [code-smells, bloaters]
---

# Data Clumps

**Signs:**
- Same 3+ fields appear together in multiple structs
- Same parameters passed together to multiple functions
- Deleting one field would make the others meaningless

**Why it's bad:** Duplication. Changes to the group require updates in multiple places.

**Treatment:**
- Extract Class — create a struct for the clump
- Introduce Parameter Object — pass the struct
- Preserve Whole Object — use the source struct directly

**Detection (Go):**
- grep for repeated field groups across structs
- Same 3+ params in multiple function signatures

**SOLID violation:** DRY — same concept defined in multiple places

**Reference:** https://refactoring.guru/smells/data-clumps
