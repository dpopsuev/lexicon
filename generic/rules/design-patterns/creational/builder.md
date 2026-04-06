---
id: builder
title: "Builder Pattern"
description: "Complex object requires many optional parameters or multi-step construction."
labels: [design-patterns, creational]
---

# Builder

**Problem:** Complex object requires many optional parameters or multi-step construction.

**Solution:** Separate construction from representation. Build step by step, return the final product.

**Structure:**
- Builder: interface with build steps
- ConcreteBuilder: implements steps
- Director: orchestrates build order
- Product: the complex object

**When to use:**
- Constructor would need 4+ optional parameters
- Object has multiple valid configurations

**When NOT to use:**
- Object is simple (1-3 fields)
- Use functional options pattern instead for Go

**Go notes:**
- Prefer functional options (`WithTimeout(d)`) for simple cases
- Use Builder struct for complex multi-step construction

**Related:** Factory Method, Prototype

**Reference:** https://refactoring.guru/design-patterns/builder
