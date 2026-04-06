---
id: integrate-early
title: Integrate Early
description: Define interfaces first, implement internals last. Wire components with stub data to crystallize contracts.
labels: [pragmatic, architecture, process]
---

# Integrate Early

**Define interfaces first, implement internals last.** Wire components together with stub data to crystallize the contracts before investing in real logic.

## The approach

1. **Shared types first.** Define the data structures that cross boundaries (protocols, schemas, API types). These live in shared packages.
2. **Contract tests second.** Write tests that assert "producer output decodes correctly on the consumer side." Use fixtures. Both sides must pass before any real logic exists.
3. **Stub implementations third.** Return hardcoded/random data that satisfies the type signatures. Prove the wiring works end-to-end.
4. **Integration tests fourth.** Full pipeline with stubs -- trigger to output. Catches wiring bugs early.
5. **Real internals last.** Replace stubs with real logic one component at a time. Each replacement must keep all existing contract and integration tests green.

## Why

- Interfaces discovered late are expensive to change -- code, tests, and mental models have calcified.
- Integration bugs found late are the hardest to diagnose -- is it the producer, the consumer, or the wire?
- Stub data that satisfies contracts proves the architecture is sound before investing in algorithms.
- Top-to-bottom finds "this interface is wrong" in hours, not weeks.

## Anti-patterns

- Building a complete standalone service, then trying to integrate it. The interface will be wrong.
- Writing unit tests for internals before the integration contract exists. You'll test the wrong thing.
- Polishing one component while others don't exist yet. Polish is wasted if the interface changes.
- "I'll add the contract test later." No. The contract test IS the specification.
