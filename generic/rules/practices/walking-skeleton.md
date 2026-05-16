---
id: walking-skeleton
title: Walking Skeleton
description: Start with a tiny end-to-end implementation linking all architectural components, then grow it incrementally. Validates architecture early and keeps the system permanently shippable.
labels: [architecture, process, incremental, agile]
---

# Walking Skeleton

**Origin:** Alistair Cockburn, *Crystal Clear* (2004) / *Writing Effective Use Cases* (2000).

> "A Walking Skeleton is a tiny implementation of the system that performs a small end-to-end function. It need not use the final architecture, but it should link together the main architectural components."

The skeleton is production code, not a prototype. It exercises every communication path the system will eventually use.

## What It Is vs. What It Isn't

| Walking Skeleton | Not a Walking Skeleton |
|------------------|----------------------|
| Production code with tests | Throwaway prototype |
| End-to-end through all layers | Single-layer spike |
| Deployable from day one | "Works on my machine" |
| Trivial behavior (hello world) | Feature-complete slice |
| Build + test + deploy pipeline included | Code without tooling |

## Why Start Here

1. **Architecture validated early.** Changes to architecture grow harder and more expensive the longer the system exists. Short feedback cycles catch mistakes before they compound.
2. **Permanently shippable.** Deploying on day one guarantees the software remains deployable as it grows. Prevents the failure mode where code exists but cannot be packaged or run elsewhere.
3. **Friction-free adoption.** When test frameworks, linters, and CI exist from the start, developers encounter no obstacles when doing the right thing.
4. **Coordination confidence.** For multi-developer systems, exercising all communication paths early reveals integration issues before they become expensive.

## How to Build One

**Timeline:** 20 minutes to 2 weeks depending on system complexity.

1. **Pick the thinnest end-to-end path.** Client through server through datastore and back. The simplest possible request/response that touches every major boundary.
2. **Implement trivial behavior.** Return a hard-coded value, echo a string, render a blank screen. The behavior is irrelevant; the wiring is what matters.
3. **Build tooling in parallel.** Package manager, test runner, linter, formatter, build system, deployment script. Alternate between application code and tooling.
4. **Deploy immediately.** The skeleton is not done until it runs somewhere other than your machine. CI pipeline, staging environment, container.
5. **Write a README.** How to obtain, build, test, and deploy. Consistent commands: `make build`, `make test`, `make deploy`.

### Skeleton by project type

| Project Type | Skeleton Looks Like |
|-------------|-------------------|
| Library | Exported function returning "hello" + test + CI |
| CLI tool | Parses one flag, prints output, exits |
| Web app | Single route rendering one page through the full stack |
| Full-stack | Client -> server -> database round-trip with sample data |
| Game | Black window with event loop and render pipeline wired |

## Growth Strategy

Once the skeleton walks:

- **Implement incrementally.** Add end-to-end functionality one slice at a time. Each slice exercises the same architectural path with richer behavior.
- **Keep it running.** The system must remain deployable after every increment. Never break the skeleton to add a feature.
- **Evolve architecture and features in parallel.** The skeleton reveals architectural pressure points. Refactor the architecture as features expose its limits.

## Relationship to Other Practices

- **Continuous Integration** depends on the skeleton — CI validates a deployable artifact, which the skeleton guarantees exists from day one.
- **Agentic Flywheel** builds on the skeleton — the forge-before-sword principle assumes a walking skeleton provides the infrastructure foundation.
- **Hexagonal Architecture** shapes the skeleton — ports and adapters define the boundaries the skeleton must wire together.

## Anti-patterns

- Building features before the deployment pipeline exists.
- Implementing a vertical slice without touching the database or external boundary.
- Treating the skeleton as a one-time exercise rather than the permanent backbone.
- Adding architectural components without verifying the skeleton still walks (regression).
- Confusing a walking skeleton with a proof of concept (PoC is throwaway; skeleton is permanent).

## Detection (code health)

A healthy codebase maintains walking-skeleton properties:

- Every public API entry point reaches the internal implementation through a traceable call path.
- No "island" packages exist that are unreachable from both public API surfaces and internal wiring.
- Build, test, and deploy scripts exist and pass from the first commit.
