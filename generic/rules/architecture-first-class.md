---
id: architecture-first-class
title: Architecture as a First-Class Pillar
description: Code structure is an equal pillar alongside functionality and performance — not an afterthought
labels: [architecture, design]
---

# Architecture as a First-Class Pillar

Software quality rests on three equal pillars:

1. **Functionality** -- does it do the right thing?
2. **Performance** -- does it do it fast enough?
3. **Architecture** -- is the code structure sustainable, navigable, and change-ready?

Architecture is not a side-effect of implementation. It is a deliberate, reviewable, testable artifact that deserves the same rigor as functional correctness and performance benchmarks.

## Principles

- **Structure is a deliverable.** Component boundaries, dependency direction, and layer separation are design decisions — not emergent properties of "clean code." Review them as explicitly as you review behavior.
- **Diagrams are assertions.** A diagram of current vs. desired architecture is a testable claim. If the code diverges from the diagram, one of them is wrong.
- **Coupling is a metric.** Fan-in, fan-out, and dependency depth are measurable. Track them alongside test coverage and latency percentiles.
- **Refactoring is not optional.** When architecture degrades (god components, circular deps, leaky abstractions), it compounds into velocity loss. Schedule structural work like you schedule bug fixes.

## In practice

- Design documents that change component boundaries MUST include current and desired architecture diagrams.
- Use `flowchart` for component relationships, `sequenceDiagram` for protocols, `classDiagram` for type hierarchies.
- On completion, verify the implementation matches the desired architecture. Update canonical docs.
- When concurrent work modifies the same boundary, flag the conflict early.

## Anti-patterns

- Treating architecture as "we'll clean it up later."
- Reviewing only functional behavior in PRs while ignoring structural changes.
- Adding dependencies without considering fan-in/fan-out impact.
- Letting module boundaries drift without updating documentation.
