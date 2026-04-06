---
id: visitor
title: "Visitor Pattern"
description: "Need to add operations to a class hierarchy without modifying the classes."
labels: [design-patterns, behavioral]
---

# Visitor

**Problem:** Need to add operations to a class hierarchy without modifying the classes.

**Solution:** Separate operations into visitor objects. Classes accept visitors.

**Structure:**
- Visitor: interface with Visit method per element type
- Element: interface with Accept(Visitor)
- ConcreteVisitor: implements operations

**When to use:**
- Adding operations to complex structures (AST, DOM, graph)
- Operations change more often than the structure

**When NOT to use:**
- Structure changes frequently (new element types break all visitors)
- Few operations needed

**Go notes:**
- Use type switches instead of Accept/Visit for simple hierarchies
- For complex ASTs, full Visitor pattern is warranted

**Related:** Composite, Iterator, Strategy

**Reference:** https://refactoring.guru/design-patterns/visitor
