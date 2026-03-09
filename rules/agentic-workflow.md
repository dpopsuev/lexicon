---
id: agentic-workflow
title: Agentic Workflow
description: AI agent as build-and-verify operator with local circuit, abort protocol, and observability
labels: [process, agentic]
---

# Agentic Workflow

The AI agent is the build-and-verify operator. Every code change passes through a local circuit before being declared complete.

## Local circuit

Run stages in order after every substantive change. Each stage is a quality gate.

1. **Build** -- compile; fix errors immediately.
2. **Lint** -- lint changed files; fix introduced lints, ignore pre-existing.
3. **Unit test** -- run tests; stop and fix before proceeding.
4. **Integration** -- run integration/calibration suite; triage regressions.
5. **Validation** -- run full validation if applicable; monitor output, apply abort protocol.

Never skip stages. Build before test; test before integration.

## Abort protocol

**Abort first, diagnose second, optimize third.**

When a command exceeds its silence budget or expected runtime:

1. **Diagnose** -- read last output; check for deadlocks, infinite loops, missing exit conditions.
2. **Measure** -- note partial progress and elapsed time.
3. **Propose fix** -- concrete optimization or bug fix.
4. **Re-run with instrumentation** -- add verbose/debug flags, timing logs, or profiling.

## Observability

- **Stream, don't stare.** Use verbose/debug flags. No output for 15 seconds means something is wrong.
- **Fail fast, diagnose loud.** Print exact error, file, and failing case. Never retry blindly.
- **Incremental over full.** After a targeted fix, run the affected test first, then the full suite.
- **Commit at green gates.** Offer to commit when build + lint + tests pass. Don't bundle uncommitted changes across features.

## Design for fast abort

When writing code with blocking or long-running operations:

- Add context with timeout -- never block indefinitely.
- Prefer timeout/staleness flags over hardcoded waits.
- Emit progress signals (periodic log lines, heartbeat, progress counter).
- Return actionable errors (`"timed out after 10s waiting for X"` beats a silent hang).

## Anti-patterns

- Editing multiple files without building or testing any of them.
- Running integration before verifying the code compiles.
- Piping output through tail/head and losing error context.
- Sleeping in a poll loop without progress logging.
- Retrying a failed command without diagnosing the failure.
- Declaring "done" without running the circuit.
