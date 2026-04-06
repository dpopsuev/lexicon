---
id: bridge
title: "Bridge Pattern"
description: "Abstraction and implementation are tightly coupled."
labels: [design-patterns, structural]
---

# Bridge

**Problem:** Abstraction and implementation are tightly coupled. Changes to one force changes to the other.

**Solution:** Separate abstraction from implementation so they can vary independently.

**Structure:**
- Abstraction: high-level interface
- Implementation: low-level interface
- RefinedAbstraction: extends Abstraction
- ConcreteImplementation: implements Implementation

**When to use:**
- Multiple dimensions of variation (e.g., shape × renderer)
- Want to switch implementations at runtime

**When NOT to use:**
- Only one dimension of variation exists
- Abstraction and implementation change together

**Go notes:**
- Abstraction holds an interface field (the implementation)
- Compose via constructor injection

**Related:** Adapter, Strategy, State

**Reference:** https://refactoring.guru/design-patterns/bridge
