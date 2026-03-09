---
id: architecture-snapshots
title: Architecture Snapshots
description: Current/desired architecture diagrams as first-class artifacts in contracts
labels: [architecture, design]
---

# Architecture Snapshots

Contracts that change component boundaries, interfaces, data flow, or dependency graphs MUST include:

- `## Current Architecture` -- the system at contract creation (testable assertion)
- `## Desired Architecture` -- the system after completion (acceptance target)

Both sections MUST contain at least one mermaid diagram. Prose alone is insufficient.

## Diagram types

| Type | Use for |
|---|---|
| `flowchart` / `graph` | Component relationships, data flow, dependencies |
| `sequenceDiagram` | Multi-step protocols, request/response flows |
| `classDiagram` | Interface hierarchies, type relationships |

## Integrity checks

- On contract creation: current architecture matches canonical `docs/architecture.md`.
- On contract completion: implementation matches desired architecture; update `docs/architecture.md`.
- On concurrent contracts modifying the same boundary: flag conflict.

## Transfer from plans

When Plan Mode produces diagrams, carry them into the contract's architecture sections. Do not reduce to prose.
