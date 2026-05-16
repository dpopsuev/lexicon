# Onion Architecture

**Origin:** Jeffrey Palermo (2008), building on Hexagonal Architecture (Alistair Cockburn, 2005)

## Definition

An architectural pattern where dependencies point inward. The core (domain model) has zero dependencies on outer layers. Outer layers (infrastructure, UI, frameworks) depend on the core. The result is a domain model that is portable, testable, and independent of delivery mechanisms.

## The Layers (Inside Out)

1. **Domain Model** (innermost): entities, value objects, domain events. Pure data and domain logic. No framework imports, no I/O.
2. **Domain Services**: operations that don't belong to a single entity. Still no infrastructure dependencies.
3. **Application Services**: orchestration, use cases, transaction boundaries. Depends on domain, not on infrastructure.
4. **Infrastructure** (outermost): databases, HTTP, messaging, Kubernetes clients. Implements interfaces defined by inner layers.

## Core Rule

**Dependencies always point inward.** An inner layer never imports an outer layer. The domain model does not know about Kubernetes, Prometheus, or pod exec. It defines interfaces (ports); outer layers implement them (adapters).

## Structural Signals

- Inner packages have zero imports of outer packages.
- Interfaces defined in domain packages, implemented in infrastructure packages.
- The domain model compiles and tests without any infrastructure present.
- Import arrows in the dependency graph all point toward the center.

## Relationship to Hexagonal Architecture

Onion Architecture and Hexagonal Architecture (Ports and Adapters) share the same core principle: dependency inversion at the architectural level. Hexagonal emphasizes the ports (interfaces) and adapters (implementations). Onion emphasizes the concentric layer structure and the inward dependency rule.

## In Practice

- The PTP domain model (Clock, NICInfo, ServiceInstance, TraitSet) has no dependency on Kubernetes clients, Prometheus, or pod exec.
- The OCP layer (Layer 4) is the outermost ring: it reads CRs, calls the Taxonomer, populates the domain types.
- The test suite consumes the domain model through the Clock entity. It does not import the operator's internal types.

## Sources

- Palermo, J. "The Onion Architecture" (2008) -- blog series
- Cockburn, A. "Hexagonal Architecture" (2005) -- Ports and Adapters
- Martin, R. C. *Clean Architecture* (2017) -- The Dependency Rule
