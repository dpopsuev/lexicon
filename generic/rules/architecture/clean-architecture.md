---
id: clean-architecture
title: "Clean Architecture"
description: "Robert C. Martin's dependency rule: source code dependencies point inward"
labels: [architecture, clean, solid]
---

# Clean Architecture

**Core idea:** Separate software into concentric layers. The Dependency Rule: source code dependencies must point inward. Inner layers know nothing about outer layers.

## Layers (inside → out)

1. **Entities** — enterprise business rules (domain objects)
2. **Use Cases** — application business rules (orchestration)
3. **Interface Adapters** — convert data between use cases and external formats
4. **Frameworks & Drivers** — database, web, UI, external tools

## The Dependency Rule

Nothing in an inner circle can know about anything in an outer circle. The name of something in an outer circle must not be mentioned in an inner circle. This includes functions, classes, variables, or any named entity.

## Relationship to Hexagonal

Clean Architecture and Hexagonal Architecture solve the same problem. Hexagonal uses ports/adapters vocabulary. Clean Architecture uses layers/dependency-rule vocabulary. Both arrive at: domain at center, infrastructure at edges, dependencies point inward.

## Djinn Alignment

- Parchment (domain) has zero transport imports — Clean Architecture compliant
- Djinn imports libraries inward (Parchment, Ordo, Oculus, Troupe, Mirage)
- depguard lint rule enforces dependency direction

**Reference:** Robert C. Martin, "Clean Architecture" (2017)
