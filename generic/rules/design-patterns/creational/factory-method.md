---
id: factory-method
title: "Factory Method Pattern"
description: "A class can't anticipate which type of object it needs to create."
labels: [design-patterns, creational]
---

# Factory Method

**Problem:** A class can't anticipate which type of object it needs to create. Constructor logic varies by subtype.

**Solution:** Define a method that returns an interface. Subclasses or functions decide the concrete type.

**Structure:**
- Creator: declares the factory method
- Product: interface returned by factory
- ConcreteProduct: actual implementation

**When to use:**
- Multiple types share an interface but differ in construction
- Construction logic changes independently from usage

**When NOT to use:**
- Only one implementation exists and won't change
- Simple struct literal suffices

**Go notes:**
- Use a constructor function returning an interface: `func NewStore(kind string) Store`
- Prefer function factories over class hierarchies

**Related:** Abstract Factory, Template Method, Prototype

**Reference:** https://refactoring.guru/design-patterns/factory-method
