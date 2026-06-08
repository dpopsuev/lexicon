---
id: kiss-directives
title: KISS Directive Family — Anti-Muda Constraints for AI Agents
description: Mandatory constraints injected into agent context to prevent overengineering, premature abstraction, and code bloat. Based on Toyota Muda (waste elimination) applied to code generation.
labels: [kiss, directives, agent, muda, constraints]
always_apply: true
---

# KISS Directive Family

Seven waste types (Muda) applied to AI code generation. Each has a detection rule and a constraint.

## 1. Abstraction needs two callers

Create an interface, abstract class, or type parameter when two or more concrete implementations exist in this change. Three similar lines of code are cheaper than a premature abstraction, because an abstraction that turns out to be wrong costs every future reader.

<example>
Wrong: `type Store interface { Save(x) }` with only `*PostgresStore` implementing it.
Right: return `*PostgresStore` directly until a second store type is required.
</example>

**Detect:** interface with exactly one implementor and no second caller in sight.

## 2. Implement what the task describes, nothing more

Change only the files and functions the task names. If the task says "add a function", add one function. Logging, metrics, retry logic, and pagination are separate tasks — file them separately if you notice the gap.

<example>
Task: "add a health-check endpoint."
Wrong: add the endpoint, plus structured logging, plus a circuit-breaker wrapper.
Right: add the endpoint. File a follow-up for observability if it's missing.
</example>

**Detect:** files changed that are not mentioned in the task; functions added that do not directly serve the stated goal.

## 3. Call the thing directly

Add a layer only when two or more callers exist with different concerns, or when the existing codebase already uses the layer. Function A → B → C where B only forwards is waste.

<example>
Wrong: `UserService.GetUser(id)` → `UserRepository.FindByID(id)` → `db.Query(...)` where UserRepository adds nothing.
Right: call `db.Query(...)` from UserService directly until a second caller of the repository exists.
</example>

**Detect:** a function whose entire body is a single forwarding call.

## 4. Use stdlib first

Reach for a dependency only when stdlib is genuinely insufficient. State in a comment why stdlib does not work. A dependency is a liability: it must be updated, audited, and understood by every future engineer.

<example>
Wrong: import `github.com/pkg/errors` to wrap an error with a message.
Right: `fmt.Errorf("context: %w", err)` — stdlib since Go 1.13.
</example>

**Detect:** a new import for an operation achievable in under ten lines of stdlib.

## 5. Write zero comments by default

Add a comment only when the WHY is non-obvious to a reader who knows the codebase: a hidden constraint, a workaround, an external invariant the code cannot enforce. One line maximum. Never explain what the code does — the code says that.

<example>
Wrong: `// GetUser fetches a user by ID from the database`
Right: `// gaxios throws "request to URL failed" for OAuth failures — these are retryable`
</example>

**Detect:** comment-to-code ratio above 0.2; comments containing "this function", "the following", "note that".

## 6. Validate at boundaries, trust internals

Validate user input, external API responses, and file I/O at the point they enter the system. Trust internal function calls — if the caller guarantees the precondition, do not re-check it inside the callee. Duplicate validation is maintenance cost with no safety gain.

<example>
Wrong: `func processItem(item Item) { if item == nil { return } ... }` when processItem is only called after a nil check.
Right: assert the invariant in a test; remove the runtime check.
</example>

**Detect:** nil/bounds/type checks in internal functions whose sole caller already checks.

## 7. Delete unused code immediately

Remove unused functions, types, variables, and imports when you find them. Git preserves history. A `// deprecated` comment without a removal date is not a plan — it is indefinite deferral.

<example>
Wrong: keep `OldHandler` with a `// deprecated, remove in v2` comment.
Right: delete `OldHandler`. The removal is in git history with a commit message.
</example>

**Detect:** unexported symbols with zero references; imports that nothing in the file calls.

## Override

A task can explicitly request something that contradicts a rule above — "add comprehensive error handling" overrides rule 6; "add retry logic" overrides rule 2. The explicit task description wins. When in doubt, implement the minimum and note the gap.

## Complements

- `code-is-a-liability` — the philosophy behind these constraints
- `shallow-vs-deep-modules` — Ousterhout's module depth principle
- `ablation-testing` — prove each mechanism earns its keep before merging
