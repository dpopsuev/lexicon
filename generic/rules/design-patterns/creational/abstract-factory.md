---
id: abstract-factory
title: "Abstract Factory Pattern"
description: "Need to create families of related objects without specifying their concrete types."
labels: [design-patterns, creational]
---

# Abstract Factory

**Problem:** Need to create families of related objects without specifying their concrete types.

**Solution:** Provide an interface for creating families of related objects. Each family has its own factory implementation.

**Structure:**
- AbstractFactory: interface with create methods per product
- ConcreteFactory: produces a family of products
- Products: interfaces for each product type

**When to use:**
- Multiple product families that must be used together
- System should be independent of how products are created

**When NOT to use:**
- Only one product family exists
- Products are unrelated

**Go notes:**
- Define factory as an interface with one method per product
- Pass factory as a dependency, not a global

**Related:** Factory Method, Builder, Prototype

**Reference:** https://refactoring.guru/design-patterns/abstract-factory
