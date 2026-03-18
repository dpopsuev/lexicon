---
id: solid-principles
title: SOLID Principles
description: Five design constraints that prevent coupling, ensure testability, and maintain clean module boundaries.
labels: [architecture, design, constraints]
---

# SOLID Principles

**Every module, interface, and dependency decision must satisfy SOLID.**

## The Principles

### S — Single Responsibility
Each module/package has one reason to change. A schematic handles domain logic. A connector handles transport. A framework package handles orchestration. If a change requires editing both, the boundary is wrong.

### O — Open/Closed
Modules are open for extension, closed for modification. New schematics don't require framework changes. New connectors don't require schematic changes. Extension happens via sockets, registries, and component.yaml — not by editing existing code.

### L — Liskov Substitution
Any implementation of an interface can replace another without breaking callers. `toolkit.SourceReader` works whether the driver is git, docs, or a test stub. `Dispatcher` works whether the backend is CLI, MCP, HTTP, or stdin.

### I — Interface Segregation
Consumers depend only on the interfaces they use. A calibration harness doesn't import RCA types. A connector doesn't import the circuit walker. If a consumer needs to import a package it doesn't directly use, the interface is too wide.

### D — Dependency Inversion
High-level modules must not depend on low-level modules. Both depend on abstractions.

**Origami-specific constraint**: **A schematic must never import another schematic directly.** Cross-schematic dependencies are declared as sockets in `component.yaml` and wired by the consumer via `origami.yaml` bindings. The framework resolves the wiring at fold time.

## What violates this rule

- A schematic importing another schematic's Go package (e.g., `rh-rca` importing `rh-dsr` directly instead of via a socket).
- A framework package importing a schematic package (e.g., `cmd/origami` importing `schematics/rca`).
- A connector hardcoding domain types instead of implementing a socket interface.
- A circuit YAML referencing another circuit by module path instead of by name (the registry resolves names → definitions).

## What complies

- Schematics declare sockets: `requires: sockets: [{name: dsr, type: Reader}]`
- Consumers bind sockets: `origami.yaml: schematics.rca.bindings.dsr: harvester`
- The framework wires it at fold time: `mcpconfig.WithDSRReader(rhdsrInstance)`
- Sub-circuits are registered in `GraphRegistries.Circuits` by the consumer, not hardcoded by the schematic.
- Framework utilities like `LoadSubCircuitsFromFS` accept resolvers as parameters — they don't know which schematics exist.

## Complements

- `hexagonal-architecture` — domain logic in libraries, binaries are thin shells.
- `abstraction-boundaries` — interfaces define module boundaries.
- `testing-methodology` — SOLID code is testable code (no hidden dependencies).
