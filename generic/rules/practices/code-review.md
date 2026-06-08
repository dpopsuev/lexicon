---
id: code-review
title: Code Review
description: What to look for in a review, how to give feedback that lands, and what reviews are not for.
labels: [practices, git, quality]
---

# Code Review

**Review for correctness and design. Use tools for style, formatting, and lint.**

Anything a linter, formatter, or type checker can catch should not appear in a code review comment. Reserve review for judgment calls.

## What to look for

**Correctness** — does the code do what the task requires? Does it handle the error paths? Are there race conditions or unbounded allocations?

**Design** — is the change in the right place? Does it introduce unnecessary coupling? Does it respect the existing abstraction boundaries? Would a simpler approach work?

**Test coverage** — do the tests actually fail before the fix and pass after? Do they cover the important edge cases? Are they testing the right thing (see `testing-methodology`)?

**Scope** — does the diff contain only what the task requires? Changes outside the task scope belong in a separate PR.

<example>
Good review comment: "This parser allocates a new slice on every call — the caller is in a hot loop. Can we pass a reusable buffer?"
Good review comment: "This change touches the auth middleware to fix a display bug. The auth change belongs in a separate PR."
Bad review comment: "Use 2-space indentation." (let the formatter handle it)
Bad review comment: "I would have named this differently." (style preference, not correctness)
</example>

## How to give feedback

State what you observed, why it matters, and what you suggest. Do not just note the problem.

```
Observed: the retry loop has no backoff.
Why: without backoff, a thundering herd on service restart degrades the target.
Suggestion: add exponential backoff with jitter, capped at 30s.
```

Distinguish blocking issues from suggestions. Use explicit markers if your team does not have a convention: `[blocking]` vs `[nit]` vs `[question]`.

## What reviews are not for

- **Enforcing conventions** — that is what rules, linters, and formatters are for. If a convention is important enough to enforce in review, it is important enough to automate.
- **Architecture decisions** — if the design is wrong, the conversation belongs in a spec before the code is written, not in a PR review after.
- **Learning opportunities** — if you want to teach, file a separate doc or note. Do not block a PR to explain a concept.

## Reviewer checklist

- [ ] Does the diff match what the task describes?
- [ ] Do the tests fail before and pass after? (or are there new tests for the new behavior?)
- [ ] Are there new lint, type, or build errors?
- [ ] Does the change respect existing abstraction boundaries?
- [ ] Is the error path handled at every boundary?
- [ ] Is there code outside the task scope that belongs in a separate PR?

## Complements

- `testing-methodology` — tests are not optional; the review checks they exist
- `commit-conventions` — the commit message tells the reviewer what to expect
- `kiss-directives` — reviewers should reject code that violates the seven constraints
- `writing-rules` — apply the same specificity standard to review comments as to rules
