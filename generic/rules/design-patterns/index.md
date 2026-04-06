---
id: design-patterns
title: Design Patterns
description: Recognize problems, apply proven solutions. Reference -- https://refactoring.guru/design-patterns
labels: [architecture, design, patterns]
---

# Design Patterns

**Apply when the problem matches. Never force a pattern onto simple code.**

Reference: https://refactoring.guru/design-patterns

## Creational

Manage object creation complexity.

| Pattern | Problem Signal | Solution |
|---------|---------------|----------|
| Factory Method | Constructor needs to return different types based on input | Creator function returns an interface; callers don't know the concrete type |
| Abstract Factory | Families of related objects that must be used together | Factory interface with one method per family member |
| Builder | >4 optional parameters, complex construction sequences | Step-by-step construction; functional options or fluent API |
| Prototype | Need copies of complex objects without coupling to their types | Clone method on an interface |
| Singleton | Exactly one instance required, shared globally | Module-level instance with lazy init; prefer DI over global state |

## Structural

Compose objects into larger structures.

| Pattern | Problem Signal | Solution |
|---------|---------------|----------|
| Adapter | Incompatible interface between two modules | Wrapper type implementing the target interface, delegating to the adaptee |
| Bridge | Abstraction and implementation vary independently | Separate interface hierarchies connected by composition |
| Composite | Tree structures with uniform leaf/branch operations | Shared interface with `Children()` method |
| Decorator | Add behavior without modifying the original type | Wrapper implementing the same interface, delegating + augmenting |
| Facade | Complex subsystem needs a simple entry point | Simplified API hiding internal wiring |
| Proxy | Control access, add caching, logging, or lazy loading | Wrapper implementing the same interface with interception logic |

## Behavioral

Manage algorithms and responsibility assignment.

| Pattern | Problem Signal | Solution |
|---------|---------------|----------|
| Strategy | Algorithm varies at runtime or by configuration | Interface field, set via constructor or option |
| Observer | Multiple consumers react to state changes | Event bus with subscribe/emit; callbacks or channels |
| Command | Need to queue, undo, log, or schedule operations | Encapsulate operation as an object or function |
| Template Method | Algorithm skeleton is fixed, but steps vary | Higher-order function accepting step functions; or base type with hook methods |
| Iterator | Sequential access over an aggregate without exposing internals | Standard iteration protocol (range, yield, next) |
| Mediator | N components need coordination without N-squared coupling | Central hub; components reference mediator, not each other |
| State | Behavior changes based on internal state; complex conditionals | Interface per state; object holds current state and delegates |
| Chain of Responsibility | Request handled by one of many possible handlers | Ordered handler list; first match wins |
| Visitor | Operations over a heterogeneous type hierarchy without modifying types | Accept/Visit double dispatch |

## Decision Heuristic

When you see:

1. **A growing switch/conditional** -- consider Strategy or State
2. **Repeated parameter groups** -- consider Builder
3. **Deep call chains (a.B().C().D())** -- consider Facade or Mediator
4. **The same wrapper logic repeated** -- consider Decorator
5. **"Notify all subscribers"** -- consider Observer
6. **Two modules importing each other** -- consider Adapter or extract interface
7. **Complex object construction** -- consider Factory or Builder
8. **Behavior that varies by runtime config** -- consider Strategy
9. **Undo/replay requirements** -- consider Command or Memento
10. **Tree-shaped data with recursive operations** -- consider Composite

## Anti-Patterns

- **Pattern for pattern's sake.** Three lines of straight code beats a Strategy with one implementation. Wait for the second use case.
- **Premature abstraction.** A concrete type is fine until proven otherwise. Extract the interface when the second consumer arrives.
- **Pattern stacking.** Factory that creates a Builder that configures a Strategy that decorates an Observer -- simplify. Each layer of indirection must justify its existence.
- **Cargo cult.** Using a pattern because "it's best practice" without identifying the specific problem it solves in your codebase.
- **Framework coupling.** Patterns should express domain intent, not framework mechanics. If removing the framework would invalidate the pattern, it's framework coupling, not a design pattern.

## Smell-to-Pattern Mapping

Common refactoring paths from smell to pattern:

| Smell | Refactoring | Target Pattern |
|-------|-------------|---------------|
| Switch Statements | Replace Conditional with Polymorphism | Strategy |
| Long Parameter List | Introduce Parameter Object / Builder | Builder |
| Feature Envy | Move Method + Extract Interface | Adapter or Mediator |
| Parallel Hierarchies | Merge via Registry | Factory + Registry |
| Duplicate Code | Extract + Generalize | Template Method |
| Message Chains | Hide Delegate | Facade |
| Divergent Change | Extract Module | (SOLID SRP, not a pattern) |
| Inappropriate Intimacy | Extract Interface to third module | Adapter or Mediator |

## Complements

- `code-smells` -- smells are the diagnosis; patterns are often the treatment.
- `solid-principles` -- patterns are the mechanism for achieving SOLID compliance.
- `hexagonal-architecture` -- Adapter, Facade, and Strategy are hexagonal building blocks.
- `papert-paradigm` -- Builder and Factory create the low-floor/high-ceiling API shape.
