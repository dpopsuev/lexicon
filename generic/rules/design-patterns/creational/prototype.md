---
id: prototype
title: "Prototype Pattern"
description: "Need copies of complex objects without coupling to their concrete classes."
labels: [design-patterns, creational]
---

# Prototype

**Problem:** Need copies of complex objects without coupling to their concrete classes.

**Solution:** Delegate cloning to the objects themselves via a Clone method.

**Structure:**
- Prototype: interface with Clone() method
- ConcretePrototype: implements Clone

**When to use:**
- Object creation is expensive (DB queries, file reads)
- Need variations of a configured object

**When NOT to use:**
- Objects are simple value types
- No deep copy needed

**Go notes:**
- Define `Clone() T` method on the type
- Be careful with pointer fields — deep copy required

**Related:** Factory Method, Memento

**Reference:** https://refactoring.guru/design-patterns/prototype
