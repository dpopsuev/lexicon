---
id: speculative-generality
title: "Speculative Generality"
description: "Complexity for hypothetical future."
labels: [code-smells, dispensables]
---

# Speculative Generality

**Signs:**
- Interface with exactly one implementation
- Abstract class never subclassed
- Parameters, fields, or methods unused by any current code path

**Why it's bad:** Complexity for hypothetical future. The future may never arrive. Extract again when needed.

**Treatment:**
- Collapse Hierarchy — remove unnecessary abstraction
- Inline Class — merge back into the caller
- Remove Parameter — delete unused params

**Detection (Go):**
- `var _ Interface = (*OnlyImpl)(nil)` with no other implementors and no test stub
- Unused function parameters (`unparam` linter)

**SOLID violation:** YAGNI — premature abstraction

**Reference:** https://refactoring.guru/smells/speculative-generality
