---
id: senior-engineer-mindset
title: Senior Engineer Mindset
description: Conservative implementation discipline. Solve the problem in front of you. Ship the smallest correct change. Refactor after green.
labels: [pragmatic, philosophy, process, constraints]
---

# Senior Engineer Mindset

**The master solves bigger problems by solving fewer problems at once.** — Kent Beck

A senior engineer's output is not measured in lines written but in problems eliminated. The instinct to reach for the "right" abstraction before the feature works is a journeyman trap. The discipline to resist it is what separates the two.

## Core axiom

> Make the change easy (warning: this may be hard), then make the easy change. — Kent Beck

Every implementation decision flows from this. If the current change is hard, the codebase isn't ready for it yet. Make it ready first — in a separate, reviewable commit — then make the feature change trivially.

## Rules

### 1. Solve what's in the ticket

Implement only what the task describes. Not what you anticipate will be needed. Not what would be elegant if requirements changed. What the ticket says.

The ticket is the unit of correctness. Everything outside it is speculation.

### 2. Minimum viable diff

The correct implementation is the one with the fewest lines changed that makes the tests green. Every additional line is a liability: more to review, more to break, more to maintain.

Count your diff. If it's larger than expected, ask what you added that wasn't asked for.

### 3. YAGNI is not laziness — it's discipline

Interfaces earn their place when 2+ concrete implementations exist today. Abstractions earn their place when 2+ callers exist today. Generic solutions earn their place when 2+ types are used today.

"We might need this later" is not a reason to build it now. Git has history. Refactoring is cheap. Over-engineering is expensive.

### 4. Make it run, then make it right

Order of operations:
1. **Make it run** — correct behavior, no architecture
2. **Make it right** — clean structure, proper abstractions
3. **Make it fast** — optimize only when measured

Do not start at step 2. Do not combine steps. Each step produces a reviewable artifact. Step 2 only happens after step 1 is green on hardware.

### 5. Prior art is law

Before writing a new function, find the existing one doing the same job. Before introducing a pattern, find where the codebase already uses it. A codebase has a style. Match it. The second implementation of a pattern must look like the first.

Deviation from prior art requires justification. Consistency requires none.

### 6. Change one thing at a time

One semantic change per commit. One concern per PR. If you find yourself saying "while I'm in here I'll also fix...", stop. File a ticket. Come back.

Mixed concerns in a diff make review impossible and bisect useless.

### 7. Preparatory Refactoring (Fowler / Beck)

When a feature requires structural change, split the work into two independent commits:

```
Commit 1: make the change easy   (refactor, zero behavior change)
Commit 2: make the easy change   (feature, minimal diff)
```

Martin Fowler calls this **Preparatory Refactoring** — reshaping the code into a form where the feature is trivial to add, before adding it. Each commit is independently reviewable, revertable, and bisectable. A reviewer reading commit 2 should find it obvious.

The preparatory commit contains no new behavior. If a test breaks in commit 1, the refactor is wrong. If commit 2 is not obviously small after commit 1, the preparation was insufficient.

## Anti-patterns

| Anti-pattern | What it looks like | The discipline |
|---|---|---|
| **Speculative abstraction** | Interface with one implementation "for future flexibility" | Delete the interface. Return the concrete type. |
| **Gold-plating** | Feature PR that also refactors 3 unrelated files | Separate PRs. The feature PR touches only what's needed. |
| **Architecture-first** | Designing the system before the first test passes | Make it run first. Discover the right abstraction from real usage. |
| **Resume-driven development** | Introducing a pattern because it's correct, not because it's needed | Patterns are solutions to problems. No problem, no pattern. |
| **Completeness theater** | Adding exhaustive error handling, logging, and metrics to a prototype | Prototype until the shape is right. Harden what survives. |

## The conservative diff checklist

Before submitting a PR, verify:

- [ ] Every changed file is directly required by the ticket
- [ ] No new interfaces unless 2+ implementations exist in this PR
- [ ] No signature changes to existing public functions unless unavoidable
- [ ] Existing callers are unchanged or the change is trivially mechanical
- [ ] The architectural improvement (if any) is in a separate prior commit
- [ ] The diff could not be made smaller without losing correctness

## Relationship to other rules

The senior mindset governs *when* to apply patterns, not *which* pattern to apply. It answers: "should I do this now?" All other rules answer "if I do this, how should I do it?"

Applied together:
- `kiss-directives` — the agent-facing constraints that enforce this mindset mechanically
- `yagni` → embedded in rules 1 and 3 above
- `solid-principles` — correct *shape* of a solution; senior mindset governs *timing*
- `effective-go` — correct *style* of a Go solution; senior mindset governs *scope*
- `ablation-testing` — prove each mechanism earns its keep before merging

## Complements

- `kiss-directives` — mechanical enforcement for agents
- `solid-principles` — shape of solutions
- `effective-go` — Go-specific hygiene
- `walking-skeleton` — make it run before making it right
- `integrate-early` — wire first, implement second
