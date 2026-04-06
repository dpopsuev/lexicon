---
id: adapter
title: "Adapter Pattern"
description: "Incompatible interfaces need to work together."
labels: [design-patterns, structural]
---

# Adapter

**Problem:** Incompatible interfaces need to work together. Existing code can't be modified.

**Solution:** Wrapper that translates one interface to another.

**Structure:**
- Target: interface the client expects
- Adapter: wraps Adaptee, implements Target
- Adaptee: existing incompatible class

**When to use:**
- Integrating third-party libraries with different interfaces
- Legacy code that can't be modified

**When NOT to use:**
- Interfaces are already compatible
- Adapter would add no value

**Go notes:**
- Implement the target interface on a struct that wraps the adaptee
- Common in port/adapter (hexagonal) architecture

**Related:** Bridge, Decorator, Proxy, Facade

**Reference:** https://refactoring.guru/design-patterns/adapter
