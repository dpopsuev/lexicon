---
id: parallel-inheritance-hierarchies
title: "Parallel Inheritance Hierarchies"
description: "Coupling between hierarchies."
labels: [code-smells, change-preventers]
---

# Parallel Inheritance Hierarchies

**Signs:**
- Adding a type in package A always requires adding a type in package B
- Mirror hierarchies that must stay in sync
- Every new variant needs changes in two places

**Why it's bad:** Coupling between hierarchies. Forgetting one side causes subtle bugs.

**Treatment:**
- Move Method — consolidate into one hierarchy
- Move Field — merge parallel structures
- Replace Inheritance with Delegation — break the mirroring

**Detection (Go):**
- Creating a new `FooHandler` always requires a new `FooConfig`
- Two packages with matching type names

**SOLID violation:** DRY — same concept split across two hierarchies

**Reference:** https://refactoring.guru/smells/parallel-inheritance-hierarchies
