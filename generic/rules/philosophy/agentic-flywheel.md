---
id: agentic-flywheel
title: Agentic Flywheel
description: AI agent as build-and-verify operator. Micro circuit (per-change quality gates) and macro flywheel (compound DX investment where each tool makes the next tool faster).
labels: [pragmatic, process, agentic]
---

# Agentic Flywheel

The AI agent is the build-and-verify operator at two scales:
- **Micro circuit** — every code change passes through quality gates before completion
- **Macro flywheel** — every DX tool built makes the next tool faster to build, test, and deploy

## Micro: Per-Change Circuit

Run stages in order after every substantive change. Each stage is a quality gate.

1. **Build** -- compile; fix errors immediately.
2. **Lint** -- lint changed files; fix introduced lints, ignore pre-existing.
3. **Unit test** -- run tests; stop and fix before proceeding.
4. **Integration** -- run integration/calibration suite; triage regressions.
5. **Validation** -- run full validation if applicable; monitor output, apply abort protocol.

Never skip stages. Build before test; test before integration.

## Macro: Per-Project Flywheel

Each DX investment compounds — the output of one cycle feeds the next:

```
Testkit → enables Observability
  → enables Live Experimentation
    → enables Confident Deployment
      → enables Faster Iteration
        → enables better Testkit
          → spin again, faster
```

### Flywheel Principles

1. **Build the forge before the sword.** DX infrastructure (testing, tracing, config) ships before gameplay features. The forge makes every sword faster to forge.

2. **Every tool uses the architecture it builds.** The testkit uses the same domain model as production. The tracer uses the same debug hooks as the testkit. No separate "test infrastructure" — one architecture, many consumers.

3. **Compound returns over linear output.** A test framework that takes 2 hours to build saves 10 minutes per feature × 50 features = 8 hours saved. Front-load the investment.

4. **Config over recompilation.** Externalize decisions to data files (YAML, TOML). Changing behavior should require editing a config, not rebuilding the binary. This is the difference between a 5-second experiment and a 5-minute experiment.

5. **Stubs at every boundary.** Every service, every system, every adapter has a stub. Stubs enable isolation testing, fast CI, and incremental development. Build the stub BEFORE the real implementation.

6. **Observable by default.** Every system step has debug hooks. Every service boundary has trace propagation. Every test failure produces a diagnostic timeline. Observability is not a feature — it's the default.

### Flywheel DX Tiers (priority order)

| Tier | What | ROI |
|------|------|-----|
| 1 | Error recovery (Result types, no panics) | Every future error is recoverable |
| 2 | Test diagnostics (recorder, timeline dump) | Every future test failure is self-diagnosing |
| 3 | Live experimentation (hot-swap config/impls) | Every experiment takes seconds not minutes |
| 4 | Tracing (per-system spans, round-trip) | Every perf issue is visible without manual instrumentation |
| 5 | E2E skeleton (stubs, full-stack validation) | Every future feature is validated across the entire stack |
| 6 | Dashboards (Prometheus, Grafana) | Every deployment has one-click health visibility |

Build Tier 1 before Tier 2. Each tier is MORE valuable because the previous tier exists.

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

### Micro anti-patterns
- Editing multiple files without building or testing any of them.
- Running integration before verifying the code compiles.
- Piping output through tail/head and losing error context.
- Sleeping in a poll loop without progress logging.
- Retrying a failed command without diagnosing the failure.
- Declaring "done" without running the circuit.

### Macro anti-patterns
- Building gameplay features before the testkit exists.
- Adding observability after the system is built (instrument as you build).
- Hardcoding values that should be configurable.
- Building a real service before its stub exists.
- Skipping stubs because "we'll add them later" (you won't).
- Treating DX as optional polish instead of foundational infrastructure.
