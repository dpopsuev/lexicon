---
id: facade
title: "Facade Pattern"
description: "Complex subsystem with many classes."
labels: [design-patterns, structural]
---

# Facade

**Problem:** Complex subsystem with many classes. Clients need a simpler interface.

**Solution:** Single struct that wraps subsystem complexity and exposes a simple API.

**Structure:**
- Facade: simple interface to subsystem
- Subsystem classes: complex internal components

**When to use:**
- Simplifying a complex library or subsystem
- Reducing coupling between client and internals

**When NOT to use:**
- Client needs fine-grained control over subsystem
- Facade becomes a god object

**Go notes:**
- Djinn's `terminal.Terminal` is a Facade (Controller + Viewer + Lifecycle)
- Keep facade thin — delegate, don't implement

**Related:** Adapter, Mediator, Singleton

**Reference:** https://refactoring.guru/design-patterns/facade
