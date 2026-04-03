---
id: package-principles
title: Package Principles (Martin's 6 Laws)
description: When to merge packages, when to split, how to organize dependencies. REP/CCP/CRP for cohesion, ADP/SDP/SAP for coupling.
labels: [architecture, design, packages, SOLID]
---

# Package Principles

Robert C. Martin's six principles for organizing packages/modules. Three for cohesion (what goes together), three for coupling (how they relate).

## Cohesion — What Goes Together

### CCP (Common Closure Principle)
**Things that change together belong together.**

If every change to workspace features touches scope/, workspace/, and vfs/ — they're one domain split across three directories. Merge them.

Violation signal: git log shows files from 3+ packages changing in the same commit repeatedly.

### CRP (Common Reuse Principle)
**Things imported together belong together.**

If consumers import package X for 1 of 10 symbols, the other 9 are forced dependencies. Either the package is too broad (split) or the 1 symbol belongs elsewhere (merge into consumer).

Violation signal: fan-in is high but only 1-2 symbols are actually used by most consumers.

### REP (Reuse-Release Equivalence Principle)
**The unit of reuse is the unit of release.**

If you can't version and release a package independently, it's not a real package — it's a file that got its own directory. A 56 LOC package with 1 consumer is not independently releasable.

Violation signal: package has < 100 LOC, fan-in ≤ 2, no independent version.

## Coupling — How Packages Relate

### ADP (Acyclic Dependencies Principle)
**No cycles in the dependency graph.**

If A imports B and B imports A, merge them or extract a shared interface.

### SDP (Stable Dependencies Principle)
**Depend in the direction of stability.**

Stability = incoming deps / (incoming + outgoing). Packages with many dependents (stable) should not depend on packages with few dependents (unstable).

### SAP (Stable Abstractions Principle)
**Stable packages should be abstract.**

High fan-in packages should export interfaces, not concrete implementations. If driver/ has fan-in=15, it should be an interface package. Concrete drivers are sub-packages (unstable, free to change).

## Rules

1. **< 100 LOC with ≤ 2 consumers** → not a package, merge into consumer (REP violation)
2. **Always changes with another package** → merge (CCP violation)
3. **Fan-in high but only 1-2 symbols used** → extract those symbols to consumer (CRP violation)
4. **Exports interfaces AND implementations** → split interface from implementation (SAP violation)
5. **Depends on unstable package** → introduce interface or invert dependency (SDP violation)

## Anti-Patterns

- **Spec-driven package creation**: each spec gets its own directory regardless of size or cohesion
- **Shallow modules**: many small packages with thin APIs and high overhead (Ousterhout)
- **Flat forest**: everything at root level, no layering, no hierarchy
- **Package per type**: one struct per package instead of one domain per package
