---
id: dead-code
title: "Dead Code"
description: "Noise."
labels: [code-smells, dispensables]
---

# Dead Code

**Signs:**
- Unreachable functions or methods
- Unused imports, variables, or parameters
- Code behind always-false conditions

**Why it's bad:** Noise. Increases cognitive load. May mislead readers into thinking it's used.

**Treatment:**
- Delete — remove the dead code entirely
- No refactoring needed — just delete

**Detection (Go):**
- `unused` linter catches unused functions and variables
- `staticcheck` SA9003: empty branch
- `unparam`: unused function parameters

**SOLID violation:** N/A

**Reference:** https://refactoring.guru/smells/dead-code
