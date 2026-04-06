---
id: component-based
title: "Component-Based Development"
description: "Build systems from reusable, self-contained components with well-defined interfaces."
labels: [paradigm, components, architecture]
---

# Component-Based Development

**Core idea:** Build systems by composing reusable, self-contained components. Each component has a well-defined interface and can be developed, tested, and deployed independently.

## Key Properties

- **Encapsulation** — internal implementation hidden
- **Interface** — well-defined contract with consumers
- **Composability** — components combine to form larger systems
- **Replaceability** — swap implementations without changing consumers
- **Reusability** — same component in multiple contexts

## Djinn Alignment

- Parchment, Ordo, Oculus, Troupe, Mirage = reusable components
- Each has a published interface (Go module)
- Each is independently testable
- Each is replaceable (Mirage: overlay ↔ Kata ↔ K8s sandbox)

**Reference:** https://en.wikipedia.org/wiki/Component-based_software_engineering
