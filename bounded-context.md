# Bounded Context

**Origin:** Domain-Driven Design (Eric Evans, 2003)

## Definition

A Bounded Context is an explicit boundary within which a domain model is defined and applicable. Inside the boundary, every term has a precise, unambiguous meaning. Outside, the same term may mean something different.

## Core Idea

Large systems cannot have a single unified model. Different parts of the system have different views of the same concepts. A "Clock" in the PTP protocol layer means a base type (OC, BC, GM). A "Clock" in the OCP operator layer means a set of PtpConfig profiles on a node. A "Clock" in the test suite means an entity with traits, services, ports, and a contract.

Rather than forcing one definition, Bounded Contexts draw explicit borders. Within each context, the model is internally consistent. Between contexts, translation happens at the boundary.

## Structural Signals

- A package or module where all types share a coherent vocabulary.
- Types that mean different things in different packages (e.g., `Profile` in the operator vs `ProfileInfo` in the test suite).
- Translation code at boundaries: mappers, adapters, anti-corruption layers.
- Teams that own different contexts and evolve them independently.

## In Practice

- Each Bounded Context has its own types, its own rules, its own lifecycle.
- Contexts communicate through well-defined interfaces, not shared mutable state.
- The PTP test suite is its own Bounded Context. It does not reuse the daemon's internal types directly. It reads CRs and translates them into its own domain model.

## Sources

- Evans, E. *Domain-Driven Design: Tackling Complexity in the Heart of Software* (2003) -- Chapters 14-15
- Vernon, V. *Implementing Domain-Driven Design* (2013) -- Bounded Context mapping
