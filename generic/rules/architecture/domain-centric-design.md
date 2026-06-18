---
id: domain-centric-design
title: Domain-Centric Design
description: DDD, Hexagonal Architecture, and BDD are not independent methodologies — they form a system. Each attacks the same root problem from a different angle.
labels: [architecture, ddd, hexagonal, bdd, design]
---

# Domain-Centric Design

**DDD, Hexagonal Architecture, and BDD each attack the same root problem from a different angle — used together they form a coherent system.**

## The Root Problem

Technical concerns bleeding into business logic: infrastructure coupling, anemic domain models, tests written in framework language rather than business language. All three methodologies are reactions to this failure mode.

## What Each Contributes

| | Answers | Attack angle |
|---|---|---|
| **DDD** | *What* to model | Language and boundaries — ubiquitous language, bounded contexts, aggregates |
| **Hexagonal** | *How* to structure it | Architecture — isolate the domain behind ports, push infrastructure to adapters |
| **BDD** | *How* to verify it | Specification — describe behavior from the outside, in domain language |

## The System

```
  BDD scenarios
  (specify port behavior
   in domain language)
         ↓
  ┌─────────────────────────────────┐
  │          Hexagonal              │
  │  Port ← Domain Model → Port    │  ← DDD provides this center:
  │  (use cases, application logic) │    aggregates, value objects,
  │          Adapters               │    domain services, repositories
  └─────────────────────────────────┘
              ↑
       Infrastructure
    (DB, HTTP, queues, LLM)
```

BDD specifies what the ports must do. DDD defines what lives inside. Hexagonal defines the boundary that keeps them separate.

## Pairwise Relationships

### DDD ↔ Hexagonal

The tightest coupling. DDD provides the content; hexagonal provides the structure.

- DDD's **Repository** is a hexagonal **Port** — the domain defines the interface, infrastructure implements it as an adapter
- DDD's **Anti-Corruption Layer** is a hexagonal **adapter** — it translates a foreign model into your domain language
- DDD's **Bounded Context** often maps to a separate hexagonal application
- Without hexagonal, infrastructure leaks into the domain model. Hexagonal is how you implement DDD correctly.

### BDD ↔ DDD

Both start from the business perspective and share a vocabulary.

- DDD's **Ubiquitous Language** and BDD's **scenario language** are the same thing — the business's own words, made executable
- BDD scenarios written with domain experts drive DDD model discovery — the scenarios reveal what concepts the domain must express
- `Given / When / Then` maps directly to DDD aggregates: Given (precondition / state), When (command), Then (domain event / outcome)

### BDD ↔ Hexagonal

Hexagonal is what makes BDD architecturally meaningful.

- A BDD scenario describes behavior from the outside — which is exactly how hexagonal thinks (a primary actor driving the application through a port)
- Hexagonal enables testing the domain through its ports using test doubles for all adapters — no database, no HTTP, no framework in the test loop
- BDD tests belong at the **port boundary**, not the HTTP layer: fast, isolated, in domain language

## Where They Are Independent

They do not require each other. You can use any one without the others. But common failure modes when used in isolation:

| Used alone | Common failure |
|---|---|
| DDD without hexagonal | Rich domain model, but coupled to infrastructure — the anemic model creeps back in |
| Hexagonal without DDD | Clean boundaries, but a thin transaction script at the center — ports without a real domain |
| BDD without hexagonal | Scenarios pass through HTTP/DB — slow, brittle, not testing the domain in isolation |

## The Lineage

Both **Clean Architecture** (Martin, 2017) and **Onion Architecture** (Palermo, 2008) are explicit syntheses of these three ideas:

- Domain at the center (DDD)
- Concentric layers with inward-only dependencies (hexagonal)
- Use cases as the application layer — directly mappable to BDD scenarios

## Complements

- `ports-and-adapters` — hexagonal structure in detail
- `domain-driven-design` — DDD strategic and tactical patterns
- `gherkin-specification` — BDD scenario format and coverage rules
- `solid-principles` — hexagonal's dependency rule is an application of the Dependency Inversion Principle
- `onion-architecture` — alternative framing of the same synthesis
