---
id: template-method
title: "Template Method Pattern"
description: "Several classes have similar algorithms with minor variations in some steps."
labels: [design-patterns, behavioral]
---

# Template Method

**Problem:** Several classes have similar algorithms with minor variations in some steps.

**Solution:** Define the algorithm skeleton in a base, let subclasses override specific steps.

**Structure:**
- AbstractClass: defines template method + abstract steps
- ConcreteClass: implements specific steps

**When to use:**
- Common algorithm structure with varying steps
- Enforcing a sequence of operations

**When NOT to use:**
- Steps vary significantly between implementations
- No common structure exists

**Go notes:**
- Use function fields instead of inheritance: `type Pipeline struct { Validate func(); Execute func() }`
- Or pass step functions as parameters

**Related:** Factory Method, Strategy

**Reference:** https://refactoring.guru/design-patterns/template-method
