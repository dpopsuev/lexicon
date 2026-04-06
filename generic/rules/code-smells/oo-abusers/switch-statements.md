---
id: switch-statements
title: "Switch Statements"
description: "Shotgun surgery when adding new types."
labels: [code-smells, oo-abusers]
---

# Switch Statements

**Signs:**
- Same switch/type-switch repeated in multiple places
- Adding a new case requires changes in multiple functions
- Switch on type code to determine behavior

**Why it's bad:** Shotgun surgery when adding new types. Logic scattered across the codebase.

**Treatment:**
- Replace Conditional with Polymorphism — interface + implementations
- Replace Type Code with State/Strategy — behavior behind interface
- Introduce Null Object — eliminate nil-checking switches

**Detection (Go):**
- Same `switch kind {` in 3+ functions
- `gocritic switchStatement` or manual grep for repeated switches

**SOLID violation:** OCP — not open for extension without modification

**Reference:** https://refactoring.guru/smells/switch-statements
