---
id: architecture-erosion
title: Architecture Erosion — Detect and Fix Structural Decay
description: Techniques for detecting and fixing divergence between intended and actual architecture. DSM, reflexion models, independence scoring, consolidation analysis.
labels: [architecture, erosion, refactoring, maintenance]
---

# Architecture Erosion

**Architecture erodes when the code grows without enforcing the intended structure.**

Not the same as drift (measured against a desired state). Erosion is organic decay where the intended structure was never enforced — the gap between "what we designed" and "what we built" widens silently.

## Symptoms

- **Package sprawl**: too many small packages at the same level (flat forest, no layers)
- **Default classification**: architecture tools can't distinguish layers because everything is at the same level
- **Spec-driven creation**: each specification gets its own directory regardless of cohesion
- **Fan-in zero**: packages that exist but nothing imports them
- **Duplicate facades**: two packages doing the same thing because the second didn't know about the first

## Detection Techniques

### Dependency Structure Matrix (DSM)
Square matrix of package × package dependencies. Reorder to reveal clusters:
- **Partitioning**: minimize off-diagonal entries → reveals natural layers
- **Tearing**: identify edges to cut for clean modularization
- **Clustering**: group tightly-coupled packages into domains

### Reflexion Model
Compare INTENDED architecture against ACTUAL import graph:
- **Convergence**: actual matches intended ✓
- **Divergence**: dependency exists that shouldn't (violation)
- **Absence**: intended dependency doesn't exist (unimplemented)

### Independence Score
`fan-in × exports / LOC` per package. Low score = package isn't earning its directory.
A 56 LOC package with 1 consumer isn't independently viable — merge into consumer.

### Common Closure Analysis
From git history: packages that always change together in the same commit are one domain split artificially. Merge them.

## Fix Techniques

### Strangler Fig
Gradual consolidation. Create target package, move one consumer at a time, keep re-exports in old package for backward compat, delete old package when empty. Never Big Bang.

### Modular Decomposition
Fowler's "refactoring toward deeper modules." Fewer packages with richer internal structure. Reduce surface area, increase depth.

### Layer Enforcement
Set the intended layer structure (domain/port/adapter/infra/app/test). Validate every import against the intended direction. Reject new violations.

## Rules

1. **Count your top-level directories.** If > 3× the number of engineers, investigate.
2. **Fan-in 0 = dead.** If nothing imports a package, it's either unused or misplaced. Delete or merge.
3. **< 100 LOC packages** should be files in another package, not standalone directories.
4. **Packages that always change together** are one domain — merge them.
5. **Run reflexion analysis** after every consolidation to verify convergence.

## Complements

- `package-principles` — Martin's 6 laws for cohesion and coupling
- `solid-principles` — SRP at the package level
- `hexagonal-architecture` — the layer structure erosion degrades
