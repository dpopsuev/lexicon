---
id: code-smells
title: Code Smells
description: Detect structural problems in code and apply targeted refactorings. Reference -- https://refactoring.guru/refactoring/smells
labels: [architecture, design, refactoring]
---

# Code Smells

**Smells are symptoms, not diseases. Each smell points to a specific refactoring.**

Reference: https://refactoring.guru/refactoring/smells

## Bloaters

Code that has grown too large to manage effectively.

| Smell | Detection Heuristic | Refactoring |
|-------|---------------------|-------------|
| Long Function | >40 lines or >3 indent levels | Extract Function |
| Large Module | >15 exported types or >2000 LOC | Extract Module/Package |
| Primitive Obsession | Raw strings/ints where a named type adds clarity (IDs, statuses, paths) | Introduce Domain Type |
| Data Clumps | Same 3+ parameters repeated across functions | Extract Parameter Object |
| Long Parameter List | >4 parameters on a function | Introduce Options/Builder |

## Object-Orientation Abusers

Misuse of abstraction mechanisms.

| Smell | Detection Heuristic | Refactoring |
|-------|---------------------|-------------|
| Switch Statements | Conditional that grows with each new variant | Replace Conditional with Polymorphism (Strategy) |
| Refused Bequest | Inherited/embedded behavior that is never used | Replace Inheritance with Composition |
| Alternative Implementations | Two types with identical method signatures but different names | Extract Interface, unify |
| Temporary Field | Field that is only set in some execution paths | Extract into a dedicated type or use Option/Maybe |

## Change Preventers

Structures that make modification expensive.

| Smell | Detection Heuristic | Refactoring |
|-------|---------------------|-------------|
| Divergent Change | One module changes for multiple unrelated reasons | Extract Module (SRP violation) |
| Shotgun Surgery | One feature change touches >3 modules | Move Function, consolidate related logic |
| Parallel Hierarchies | Adding a type in module A always requires a type in module B | Merge hierarchies or use registry pattern |

## Dispensables

Code that adds no value.

| Smell | Detection Heuristic | Refactoring |
|-------|---------------------|-------------|
| Dead Code | Unreachable function, unused import, commented-out block | Delete |
| Speculative Generality | Abstraction with one implementation and no extension point consumers | Inline; extract again when second use case arrives |
| Duplicate Code | Same logic in two places with cosmetic differences | Extract shared function or type |
| Middle Man | Type that only delegates to another with no added behavior | Remove Middle Man |

## Couplers

Excessive coupling between modules.

| Smell | Detection Heuristic | Refactoring |
|-------|---------------------|-------------|
| Feature Envy | Function references another module's data more than its own | Move Function to the module it envies |
| Message Chains | a.B().C().D() -- navigating deep object graphs | Introduce Facade or direct accessor |
| Inappropriate Intimacy | Two modules depend on each other's internals (circular imports) | Extract shared interface to a third module |
| Incomplete Library | Utility code duplicated because the library doesn't expose what you need | Extend library or introduce Adapter |

## Agent Behavior

- **Before completing a task**: scan changed code for these smells. Fix or document.
- **Do NOT refactor preexisting smells** unless they're in the task's scope. Note them for future work.
- **When a smell is found**: name it, cite the refactoring, apply if in scope.
- **Premature abstraction is itself a smell** (Speculative Generality). Three similar lines are better than a premature helper.

## Smell-to-SOLID Mapping

| Smell | SOLID Violation |
|-------|----------------|
| Divergent Change | Single Responsibility (S) |
| Switch Statements | Open/Closed (O) |
| Refused Bequest | Liskov Substitution (L) |
| God Interface | Interface Segregation (I) |
| Feature Envy, Inappropriate Intimacy | Dependency Inversion (D) |

## Complements

- `solid-principles` -- smells frequently signal SOLID violations.
- `design-patterns` -- patterns are often the target of a smell-driven refactoring.
- `testing-methodology` -- smells make code hard to test; refactoring restores testability.
