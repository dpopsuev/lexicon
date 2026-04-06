---
id: incomplete-library-class
title: "Incomplete Library Class"
description: "Duplication from library gaps."
labels: [code-smells, couplers]
---

# Incomplete Library Class

**Signs:**
- Utility code duplicated because the library doesn't expose what you need
- Wrapper functions that add missing behavior to library types
- Working around library limitations in multiple places

**Why it's bad:** Duplication from library gaps. Changes if the library adds the feature later.

**Treatment:**
- Introduce Foreign Method — add a standalone function that takes the library type
- Introduce Local Extension — wrap or embed the library type with extensions

**Detection (Go):**
- Same helper function for a library type in 2+ packages
- TODO comments about library limitations

**SOLID violation:** N/A

**Reference:** https://refactoring.guru/smells/incomplete-library-class
