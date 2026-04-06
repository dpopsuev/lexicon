---
id: mediator
title: "Mediator Pattern"
description: "Many objects communicate directly, creating a tangled web of dependencies."
labels: [design-patterns, behavioral]
---

# Mediator

**Problem:** Many objects communicate directly, creating a tangled web of dependencies.

**Solution:** Central mediator object handles all communication. Components only know the mediator.

**Structure:**
- Mediator: interface for communication
- ConcreteMediator: coordinates components
- Components: talk to mediator, not each other

**When to use:**
- Many-to-many communication between components
- Components should be reusable independently

**When NOT to use:**
- Few components with simple interactions
- Mediator becomes a god object

**Go notes:**
- Event bus pattern: components emit events, mediator routes
- Djinn's signal bus is a mediator

**Related:** Chain of Responsibility, Command, Facade, Observer

**Reference:** https://refactoring.guru/design-patterns/mediator
