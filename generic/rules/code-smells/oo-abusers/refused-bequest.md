---
id: refused-bequest
title: "Refused Bequest"
description: "Inheritance hierarchy is misleading."
labels: [code-smells, oo-abusers]
---

# Refused Bequest

**Signs:**
- Subclass overrides parent methods to no-op or panic
- Embedded struct's methods are never called
- Child uses only a fraction of parent's interface

**Why it's bad:** Inheritance hierarchy is misleading. The child doesn't truly "is-a" the parent.

**Treatment:**
- Replace Inheritance with Delegation — compose instead of embed
- Extract Subclass — create proper hierarchy
- Extract Interface — depend on what's actually used

**Detection (Go):**
- Embedded struct with most methods unused
- `panic("not implemented")` in overrides

**SOLID violation:** LSP — subclass violates parent's contract

**Reference:** https://refactoring.guru/smells/refused-bequest
