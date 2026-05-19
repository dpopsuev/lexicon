---
id: comments
title: "Comments"
description: "Comments rot faster than code."
labels: [code-smells, dispensables]
---

# Comments

**Signs:**
- Comments explain WHAT the code does instead of WHY
- Commented-out code left in place
- Comments compensate for bad naming
- Comments record decisions that belong in commit messages or ADRs
- Comments describe what the code used to do or will do ("TODO: replace once X ships")
- Comments compare to alternatives inline ("unlike Y, which...")
- Comments explain the reasoning chain that led to a design choice

**Why it's bad:** Comments rot faster than code. A comment that was true when written becomes a lie the moment the code changes. Misleading comments are worse than none.

## The one legitimate comment

**Non-obvious WHY** — something the reader cannot deduce from the code and the surrounding context.

```ts
// gaxios throws "request to URL failed" for OAuth token fetch failures —
// these are retryable network errors, not permanent auth failures.
return /request to.*failed/.test(err);
```

That is legitimate. The regex pattern alone doesn't explain the domain reason.

```ts
// waitForReady() polls getState() until !isStreaming
await this.rpcClient.waitForReady();
```

That is not. The method name already says it.

## AI-specific pitfalls

AI agents over-comment by default. Specific patterns to reject on sight:

| Pattern | Example | Correct action |
|---------|---------|---------------|
| **Decision lore** | `// Using RPC over json mode because json blocks on piped stdin` | Delete. Put in commit message or ADR. |
| **Upstream ticket reference** | `// Replace with X once pi-mono#4744 merges` | Delete. The ticket tracks it. |
| **Alternative comparison** | `// Unlike waitForIdle(), this doesn't need a prompt` | Delete. Rename the method if the distinction matters. |
| **Future state** | `// This is a workaround until the async factory lands` | Delete. The code either works or it doesn't. |
| **What-comment** | `// Resolve agent definition` above `resolveInstanceConfig(...)` | Delete. The call site is self-explanatory. |
| **Version tag** | `// Added in v0.2` | Delete. git blame exists. |
| **Praise/explanation of the approach** | `// This is the correct readiness check for fresh sessions` | Delete. Correctness is implicit. |

## Treatment

- **Extract Method** — name the method after what the comment says
- **Rename** — make the name self-explanatory so the comment is redundant
- **Commit message** — decision rationale belongs there, not in the source
- **ADR / AGENTS.md** — architectural reasoning belongs in docs, not inline

## What belongs where

| Content | Where |
|---------|-------|
| Why this algorithm, not that one | Commit message |
| Why this library was chosen | ADR or AGENTS.md |
| What a non-obvious regex matches | Inline comment ✓ |
| Known edge case the code handles | Inline comment ✓ |
| External constraint (OS quirk, API behavior) | Inline comment ✓ |
| What the next line of code does | Nowhere |
| Comparison to a previous implementation | Nowhere |

**Reference:** https://refactoring.guru/smells/comments
