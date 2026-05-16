---
id: kiss-directives
title: KISS Directive Family — Anti-Muda Constraints for AI Agents
description: Mandatory constraints injected into agent context to prevent overengineering, premature abstraction, and code bloat. Based on Toyota Muda (waste elimination) applied to code generation.
labels: [kiss, directives, agent, muda, constraints]
always_apply: true
---

# KISS Directive Family

Seven waste types (Muda) applied to AI code generation. Each has a detection rule and a constraint.

## 1. No Speculative Abstraction

**Waste**: Interface with one implementation. Abstract class never subclassed. Generic solution for a specific problem.

**Constraint**: Do NOT create an interface unless 2+ concrete implementations exist RIGHT NOW. Do NOT add type parameters unless the function is called with 2+ different types. Three similar lines is better than a premature abstraction.

**Detection**: interface with `var _ I = (*Only)(nil)` and no other implementor.

## 2. No Feature Creep

**Waste**: Adding functionality nobody asked for. Pagination, caching, retry logic for a prototype. "While I'm here" additions.

**Constraint**: Implement ONLY what the task describes. If the task says "add a function", add ONE function. Do not add tests unless asked. Do not add error handling for impossible scenarios. Do not add logging unless the task mentions observability.

**Detection**: Files changed that aren't mentioned in the task. Functions added that don't serve the stated goal.

## 3. No Unnecessary Layers

**Waste**: Service layer wrapping a repository that wraps a database call. Middleware decorating a handler that does one thing. Adapter pattern with one adapter.

**Constraint**: Call the thing directly. If you need to call a database, call the database. Add a layer ONLY when there's a proven need (2+ callers with different concerns, testability requirement stated in task, or existing codebase convention).

**Detection**: Function A calls function B calls function C, where B does nothing but forward.

## 4. No Library Addiction

**Waste**: Importing a dependency for functionality achievable in <10 lines of stdlib.

**Constraint**: Use stdlib first. Import a dependency ONLY when stdlib is genuinely insufficient AND the dependency is well-maintained (>1K stars, updated within 6 months). State why stdlib doesn't work.

**Detection**: New `go get` or `import` for trivial operations.

## 5. No Comment Bloat

**Waste**: Multi-paragraph docstrings. Comments explaining WHAT (the code already says what). Comments referencing the current task ("added for issue #123").

**Constraint**: Write ZERO comments by default. Add one ONLY when the WHY is non-obvious: a hidden constraint, a workaround, behavior that would surprise a reader. One line maximum.

**Detection**: Comment-to-code ratio > 0.2. Comments containing "this", "the following", "note that".

## 6. No Error Theater

**Waste**: Error handling around operations that cannot fail. Wrapping string concatenation in try/catch. Validating internal function parameters that the caller already validated.

**Constraint**: Trust internal code. Only validate at system boundaries (user input, external APIs, file I/O). If the function is called from one place and the caller guarantees the precondition, don't re-check.

**Detection**: `if err != nil` after an operation that provably cannot error. Error wrapping with no additional context.

## 7. No Backward Compatibility Theater

**Waste**: Renaming unused variables to `_`. Re-exporting removed types. Adding `// removed` comments. Keeping dead code "in case someone needs it".

**Constraint**: Delete it. If it's unused, it's gone. Git has history. Don't design for hypothetical external consumers that don't exist.

**Detection**: Unexported symbols with zero references. `_` prefixed variables. `// deprecated` without a removal date.

## Application

These constraints are injected by the Assembler into every agent prompt. They are non-negotiable. The agent must follow them unless the task EXPLICITLY requests otherwise (e.g., "add comprehensive error handling" overrides rule 6).

The Reflex Store reinforces KISS: simple solutions that work in few turns score higher OAE, get consolidated into Pipes, and become the default behavior. Over-engineered solutions take more turns, score lower, and never become Reflexes. The system naturally evolves toward minimal complexity.

## Complements

- `speculative-generality` — YAGNI code smell (dispensable)
- `shallow-vs-deep-modules` — Ousterhout's module depth principle
- `effective-go` — Go-specific KISS conventions
- `ablation-testing` — prove each mechanism earns its keep
