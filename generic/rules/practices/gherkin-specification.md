---
id: gherkin-specification
title: Gherkin Specification
description: Every spec change area and task acceptance gate requires at least two Given/When/Then scenarios — one happy path, one edge/error case.
labels: [testing, bdd, specification]
---

# Gherkin Specification

**Acceptance criteria are executable sentences, not paragraphs.** Use Given/When/Then to make behavior unambiguous, testable, and reviewable.

## The rule

1. **Every spec change area requires at least two Gherkin scenarios.** One happy path ("it works"), one edge or error case ("it fails gracefully"). If a change area cannot produce two scenarios, the change area is either trivial (fold it into another) or under-specified.
2. **Every task acceptance section requires at least two Gherkin scenarios.** These are the exit gate — the task is not complete until every scenario passes. Copy them from the spec's change areas, then add implementation-specific scenarios if needed.
3. **Scenarios are first-class test cases.** Each Given/When/Then maps directly to a test function. If you can't write the test from the scenario alone, the scenario is too vague.

## Scenario structure

```gherkin
Given {a specific, reproducible precondition}
When  {a single action or event}
Then  {an observable, verifiable outcome}
```

- **Given** sets up state. Be specific: "Given a pipeline with 3 steps where step 2 depends on step 1" not "Given a pipeline."
- **When** is one action. If you need two When clauses, you have two scenarios.
- **Then** is observable. "Then the response contains field X with value Y" not "Then it works correctly."

## Minimum coverage per change area

| Scenario type | Required | Purpose |
|---------------|----------|---------|
| Happy path    | Yes      | Proves the feature works as designed |
| Edge/error    | Yes      | Proves the feature fails gracefully |
| Boundary      | When applicable | Proves limits are respected (empty input, max size, zero, nil) |
| Concurrency   | When applicable | Proves parallel access is safe |

Two is the floor, not the ceiling. Complex change areas will need more.

## What violates this rule

- A spec change area with prose acceptance ("it should handle errors") instead of Gherkin.
- A task acceptance section with fewer than two Given/When/Then scenarios.
- Scenarios that test implementation details instead of observable behavior.
- A "When" clause with multiple actions chained together.
- A "Then" clause that cannot be verified mechanically ("Then the code is clean").

## What complies

- Spec with three change areas, each having 2-4 Gherkin scenarios.
- Task acceptance copied from spec scenarios, plus one implementation-specific scenario.
- Scenarios specific enough to translate directly into test function names.

## When to use structural invariants instead

Gherkin is for **behavioral** acceptance. Some criteria are **structural** — "all exported functions have doc comments," "no circular imports," "schema migration is reversible." These are valid as bullet points, not Gherkin. A good acceptance section has both: Gherkin for behavior, bullets for structure.

## Complements

- `testing-methodology` — the coverage matrix ensures scenarios span all applicable layers.
- `integrate-early` — contract tests are Gherkin scenarios for cross-boundary behavior.
