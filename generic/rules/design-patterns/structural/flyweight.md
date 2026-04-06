---
id: flyweight
title: "Flyweight Pattern"
description: "Large number of similar objects consume too much memory."
labels: [design-patterns, structural]
---

# Flyweight

**Problem:** Large number of similar objects consume too much memory.

**Solution:** Share common state between objects instead of storing it in each.

**Structure:**
- Flyweight: shared immutable state
- Context: unique state per instance
- Factory: manages flyweight pool

**When to use:**
- Thousands of similar objects (game entities, text characters)
- Most state is shared/intrinsic

**When NOT to use:**
- Few objects exist
- Objects have mostly unique state

**Go notes:**
- Separate intrinsic (shared) from extrinsic (per-use) state
- Use a map as the flyweight pool

**Related:** Composite, Facade, Singleton

**Reference:** https://refactoring.guru/design-patterns/flyweight
