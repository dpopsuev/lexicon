---
id: domain-driven-design
title: "Domain-Driven Design (DDD)"
description: "Model software around the business domain with bounded contexts and ubiquitous language"
labels: [architecture, ddd, design]
---

# Domain-Driven Design (DDD)

**Core idea:** The structure of the software should mirror the structure of the business domain. Domain experts and developers share a common language.

## Strategic Patterns

| Pattern | What |
|---|---|
| **Bounded Context** | Explicit boundary where a model applies. Different contexts can have different models for the same concept. |
| **Ubiquitous Language** | Shared vocabulary between domain experts and developers. Used in code, conversation, and documentation. |
| **Context Map** | Diagram showing relationships between bounded contexts. |
| **Anti-Corruption Layer** | Translation layer protecting your model from external models. |

## Tactical Patterns

| Pattern | What |
|---|---|
| **Entity** | Object defined by identity, not attributes. Has lifecycle. |
| **Value Object** | Immutable object defined by attributes, not identity. |
| **Aggregate** | Cluster of entities with a root entity that maintains consistency. |
| **Repository** | Abstraction for retrieving domain objects from storage. |
| **Domain Event** | Something that happened that domain experts care about. |
| **Domain Service** | Operation that doesn't belong to any entity. |

## Djinn Alignment

- Parchment Artifact = Entity (has ID, lifecycle)
- Parchment Store = Repository
- Parchment Schema = domain rules as code
- Bounded Contexts: Djinn (harness), Scribe (tracking), Lex (rules)

**Reference:** https://en.wikipedia.org/wiki/Domain-driven_design
