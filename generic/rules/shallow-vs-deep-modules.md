---
id: shallow-vs-deep-modules
title: Shallow vs Deep Modules (Ousterhout)
description: Fewer deep modules with rich internals beat many shallow modules with thin APIs. Depth reduces cognitive load, surface area, and import complexity.
labels: [architecture, design, modules, complexity]
---

# Shallow vs Deep Modules

**Complexity comes from having too many shallow modules, not too few deep ones.**

From John Ousterhout's *A Philosophy of Software Design* — the most underappreciated insight in software architecture.

## The Problem with Shallow

A shallow module has a thin API (few methods, few exports) but little depth (small implementation, low LOC). It feels clean — small, focused, "does one thing." But each module has overhead:

- Directory structure (package boundary)
- Documentation (doc.go, README)
- Import declarations (every consumer adds an import line)
- Mental model (developer must remember it exists)
- Test infrastructure (test file, setup, fixtures)

A project with 50 shallow modules has 50× this overhead. The API surface area is enormous. Consumers import 20 packages for a single operation.

## The Value of Deep

A deep module has a simple API but a rich implementation. The interface is small. The internals are complex. Consumers see a clean surface and don't need to understand the depth.

```
Shallow:              Deep:
┌───────────┐         ┌───────────┐
│   API     │         │   API     │
├───────────┤         ├───────────┤
│  impl     │         │           │
└───────────┘         │  impl     │
(high ratio:           │           │
 API/impl ≈ 1)        │           │
                      │           │
                      └───────────┘
                      (low ratio:
                       API/impl ≈ 0.1)
```

## When to Merge (Shallow → Deep)

- Package has < 100 LOC and ≤ 2 consumers → file in consumer package
- Package has < 200 LOC and 1 consumer → merge into that consumer
- Two packages always change together (Common Closure) → one domain
- Package exports 2-3 symbols that all consumers use → symbols belong in consumer

## When to Split (Deep → Shallow)

- Package has > 3000 LOC and touches > 5 domains → SRP violation, split by domain
- Package exports types used by DIFFERENT consumer sets → CRP says split
- Package has internal sub-concerns with zero coupling → extract sub-package

## The Depth Ratio

`depth = LOC / exported_symbols`

- Depth < 5: dangerously shallow (50 LOC, 10 exports → every line is API)
- Depth 10-30: healthy (1000 LOC, 50 exports → rich internals)
- Depth > 100: possibly too deep (3000 LOC, 20 exports → might be hiding too much)

## Rules

1. **Resist the urge to create a new package.** Ask: "Is this a file in an existing package?" first.
2. **Count your packages per engineer.** If > 10 packages per active contributor, investigate.
3. **Measure depth ratio.** Packages with depth < 5 are candidates for merge.
4. **Merge before split.** When in doubt, keep things together. Extract when the seam is proven by usage, not predicted by design.

## Anti-Patterns

- **Package per type**: one struct gets its own directory because "it's a different concern"
- **Spec-driven packages**: each design spec creates a new directory
- **Premature decomposition**: splitting before knowing what changes together
- **Fear of large packages**: a 2000 LOC package with clear internal organization is better than 10 × 200 LOC packages with 45 cross-imports

## Complements

- `package-principles` — Martin's CCP/CRP/REP guide merge decisions
- `architecture-erosion` — shallow modules are a symptom of erosion
- `solid-principles` — SRP applies at package level, not file level
