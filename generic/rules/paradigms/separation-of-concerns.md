---
id: separation-of-concerns
title: "Separation of Concerns"
description: "Each module addresses a distinct concern. Minimize overlap. Maximize cohesion."
labels: [paradigm, design, architecture, solid]
---

# Separation of Concerns

**Core idea:** Each section of a program should address a separate concern — a distinct aspect of functionality. Minimize overlap between sections. Maximize cohesion within each.

## Manifestations

| Level | Separation |
|---|---|
| **Function** | Each function does one thing (SRP) |
| **Package** | Each package owns one domain (CCP) |
| **Layer** | Presentation / domain / infrastructure (hexagonal) |
| **Service** | Each service owns one bounded context (DDD) |
| **Repo** | Each repo is one deployable unit |

## Related Principles

- **SRP** (Single Responsibility) — one reason to change
- **ISP** (Interface Segregation) — clients depend only on what they use
- **CCP** (Common Closure) — things that change together live together
- **CRP** (Common Reuse) — things that are used together live together
