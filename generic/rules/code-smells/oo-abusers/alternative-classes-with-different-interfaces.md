---
id: alternative-classes-with-different-interfaces
title: "Alternative Classes with Different Interfaces"
description: "Can't use polymorphism."
labels: [code-smells, oo-abusers]
---

# Alternative Classes with Different Interfaces

**Signs:**
- Two classes do similar things but have different method names
- Can't substitute one for the other despite similar behavior
- Parallel implementations that evolved independently

**Why it's bad:** Can't use polymorphism. Callers need conditional logic to pick the right class.

**Treatment:**
- Rename Method — align method names
- Extract Interface — define shared contract
- Move Method — consolidate duplicate logic

**Detection (Go):**
- Two types with methods like `Process()` vs `Handle()` vs `Execute()` doing the same thing

**SOLID violation:** LSP — types can't be substituted for each other

**Reference:** https://refactoring.guru/smells/alternative-classes-with-different-interfaces
