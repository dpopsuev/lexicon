---
id: limes-workflow
title: Limes CI Workflow
description: How AI agents use Limes MCP for CI health monitoring, test execution, and regression detection
labels: [process, agentic, testing]
---

# Limes CI Workflow

Limes is a CI health monitor available as an MCP sidecar. Use it to run tests, track regressions, detect flakes, and monitor project health.

## Session Start

1. Call `status` with `metric=health` to get a per-component health table with test types and trends.
2. Call `status` with `metric=regressions` to see newly failing tests since last green run.
3. Call `status` with `metric=flakes` to identify flaky tests that may cause false failures.

## Running Tests

- Use `run` with `action=command` to execute test commands and ingest results via adapters.
- Supported adapters: `go-test`, `go-vet`, `go-build`, `golangci-lint`.
- Use `run` with `action=limesfile` to execute a limesfile YAML DAG for multi-step CI pipelines.
- Use `run` with `action=ingest` to parse raw tool output from a previous command.

## After Making Changes

1. Run affected tests via `run action=command` with the appropriate adapter.
2. Check `status metric=regressions` to verify no new failures introduced.
3. Check `status metric=health` to confirm overall project health.

## Investigating Failures

- Use `status metric=rca` with a `target` test name for root cause analysis.
- Use `results` to query stored test data for specific components or time ranges.
- Use `status metric=trends` to see pass rate trends over recent runs.
- Use `bugs` to view staged bugs and mark them as reported.

## Limesfile DAG

Projects with a `limesfile.yaml` can define multi-step CI pipelines:

```bash
limes run --limesfile limesfile.yaml        # run all tasks
limes run --limesfile limesfile.yaml --task test  # run specific task
```
