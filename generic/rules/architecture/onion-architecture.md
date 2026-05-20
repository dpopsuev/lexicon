---
id: onion-architecture
title: Onion Architecture
description: "Dependencies point inward. The domain model has zero dependencies on outer layers. Infrastructure depends on the core; never the reverse."
labels: [architecture, clean]
---

# Onion Architecture

**Origin:** Jeffrey Palermo (2008), building on Hexagonal Architecture (Alistair Cockburn, 2005)

## Definition

An architectural pattern where dependencies point inward. The core (domain model) has zero dependencies on outer layers. Outer layers (infrastructure, UI, frameworks) depend on the core. The result is a domain model that is portable, testable, and independent of delivery mechanisms.

## The Layers (Inside Out)

1. **Domain Model** (innermost): entities, value objects, domain events. Pure data and domain logic. No framework imports, no I/O.
2. **Domain Services**: operations that don't belong to a single entity. Still no infrastructure dependencies.
3. **Application Services**: orchestration, use cases, transaction boundaries. Depends on domain, not on infrastructure.
4. **Infrastructure** (outermost): databases, HTTP, messaging, external clients. Implements interfaces defined by inner layers.

## Core Rule

**Dependencies always point inward.** An inner layer never imports an outer layer. The domain model does not know about databases, HTTP, or external frameworks. It defines interfaces (ports); outer layers implement them (adapters).

## Structural Signals

- Inner packages have zero imports of outer packages.
- Interfaces defined in domain packages, implemented in infrastructure packages.
- The domain model compiles and tests without any infrastructure present.
- Import arrows in the dependency graph all point toward the center.

## Relationship to Other Patterns

Onion Architecture and Hexagonal Architecture (Ports and Adapters) share the same core principle: dependency inversion at the architectural level. Hexagonal emphasises the ports (interfaces) and adapters (implementations). Onion emphasises the concentric layer structure and the inward dependency rule. Clean Architecture (Robert C. Martin) is the same principle applied to enterprise systems with an explicit Use Cases layer.

See also: `ports-and-adapters.md`, `clean-architecture.md`.

## Sources

- Palermo, J. "The Onion Architecture" (2008) — blog series
- Cockburn, A. "Hexagonal Architecture" (2005) — Ports and Adapters
- Martin, R. C. *Clean Architecture* (2017) — The Dependency Rule
