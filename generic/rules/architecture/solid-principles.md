---
id: solid-principles
title: SOLID Principles
description: Five design constraints that prevent coupling, ensure testability, and maintain clean module boundaries.
labels: [pragmatic, architecture, design, constraints]
---

# SOLID Principles

**Every module, interface, and dependency decision must satisfy SOLID.**

## The Principles

### S -- Single Responsibility

Each module has one reason to change. Describe its purpose in one sentence without "and." If a change requires editing two unrelated modules, the boundary is wrong.

Litmus test: can you name the module's single axis of change? "Handles HTTP transport." "Validates user input." "Persists session state." If you need a comma, split.

### O -- Open/Closed

Modules are open for extension, closed for modification. New behavior is added by implementing an interface, registering a plugin, or passing configuration -- not by editing existing code.

Litmus test: can you add a new variant (driver, handler, formatter) without modifying existing source files? If adding a feature requires a new `case` in an existing switch, the module is not closed.

### L -- Liskov Substitution

Any implementation of an interface can replace another without breaking callers. Subtypes must honor the contract: same preconditions, same postconditions, no surprises.

Litmus test: do you have contract tests that run against every implementation of the same interface? If not, substitutability is assumed, not proven.

### I -- Interface Segregation

Consumers depend only on the interfaces they use. Prefer small interfaces (1-3 methods) over large ones. If a consumer imports a package only because the interface is too wide, split it.

Litmus test: does any implementation leave methods as no-ops or panics? The interface is too wide.

### D -- Dependency Inversion

High-level modules must not depend on low-level modules. Both depend on abstractions. Domain logic defines the interfaces (ports). Infrastructure implements them (adapters). The dependency arrow always points inward.

Litmus test: can you test the domain module with a stub adapter and no real infrastructure? If you need a database, network, or filesystem to test business logic, the dependency is inverted.

## What violates this rule

- A domain package importing an infrastructure package (database, HTTP client, filesystem).
- An interface with 5+ methods that forces implementors to stub unused methods.
- Adding a new feature by modifying an existing switch/conditional instead of adding an implementation.
- Two modules that always change together for unrelated reasons (shared axis of change).
- An implementation that silently ignores part of the interface contract (violates Liskov).

## What complies

- Domain defines interfaces. Adapters implement them. Wiring happens at the composition root (main, config, DI container).
- New behavior added via new types implementing existing interfaces.
- Contract tests run against every implementation of a shared interface.
- Each module's purpose described in one sentence.

## Complements

- `hexagonal-architecture` -- domain logic in libraries, binaries are thin shells.
- `abstraction-boundaries` -- generic vs scenario-specific separation.
- `testing-methodology` -- SOLID code is testable code (no hidden dependencies).
- `design-patterns` -- patterns are often the mechanism for achieving SOLID.
- `code-smells` -- smells frequently signal SOLID violations.
