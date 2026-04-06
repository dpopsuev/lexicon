---
id: comments
title: "Comments"
description: "Comments rot faster than code."
labels: [code-smells, dispensables]
---

# Comments

**Signs:**
- Comments explain WHAT the code does (not WHY)
- Commented-out code left in place
- Comments compensate for bad naming

**Why it's bad:** Comments rot faster than code. Misleading comments are worse than none.

**Treatment:**
- Extract Method — name the method after what the comment says
- Rename Method — make the name self-explanatory
- Introduce Assertion — replace defensive comments with runtime checks

**Detection (Go):**
- Comments that restate the code: `// increment i` before `i++`
- Commented-out code blocks

**SOLID violation:** N/A — but often masks SRP violations

**Reference:** https://refactoring.guru/smells/comments
