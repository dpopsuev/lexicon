---
id: inappropriate-intimacy
title: "Inappropriate Intimacy"
description: "Circular dependency."
labels: [code-smells, couplers]
---

# Inappropriate Intimacy

**Signs:**
- Two packages depend on each other's internals (circular imports)
- One package accesses unexported fields via unsafe or reflection
- Tight coupling — changing one always requires changing the other

**Why it's bad:** Circular dependency. Can't understand, test, or deploy one without the other.

**Treatment:**
- Move Method / Move Field — untangle shared logic
- Extract Class — create a shared dependency both import
- Replace Inheritance with Delegation — break embedding coupling

**Detection (Go):**
- Locus `analysis cycles` detects circular imports
- `depguard` enforces import direction

**SOLID violation:** DIP — both depend on each other instead of abstractions

**Reference:** https://refactoring.guru/smells/inappropriate-intimacy
