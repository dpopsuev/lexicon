---
id: object-oriented-programming
title: "Object-Oriented Programming (OOP)"
description: "Organize code around objects that combine data and behavior. Encapsulation, inheritance, polymorphism, abstraction."
labels: [paradigm, oop, design]
---

# Object-Oriented Programming

**Core idea:** Model software as interacting objects that encapsulate state and behavior.

## Four Pillars

- **Encapsulation** — hide internals, expose interface. In Go: unexported fields + exported methods.
- **Abstraction** — model only what matters. In Go: interfaces define behavior, not structure.
- **Inheritance** — share behavior through hierarchy. In Go: embedding (composition, not inheritance).
- **Polymorphism** — same interface, different implementations. In Go: implicit interface satisfaction.

## Go's Take

Go is not classically OOP. No classes, no inheritance, no constructors. Instead:
- Structs + methods = objects
- Interfaces = polymorphism (implicit, consumer-defined)
- Embedding = composition (not inheritance)
- Packages = encapsulation boundary

## Anti-Patterns

- God objects (Large Class smell)
- Deep inheritance hierarchies (use composition)
- Getter/setter obsession (Data Class smell)

**Reference:** https://en.wikipedia.org/wiki/Object-oriented_programming
