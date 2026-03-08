---
id: hexagonal-architecture
title: Hexagonal Architecture
description: Domain logic belongs in library modules. Binaries are thin shells that wire infrastructure to the library.
labels: [architecture, design]
---

# Hexagonal Architecture

**Every service is a library with a thin binary on top.**

## The rule

1. **All domain logic lives in library modules.** This includes: data types, business functions, trait/interface definitions, state machines, validation, and route constructors.
2. **The binary is a thin shell.** It does only: parse config, construct state, call the library's entry point, bind a port, and serve. No business logic, no non-trivial functions.
3. **The litmus test**: if you can't import a function in a test and call it without starting a server, domain logic has leaked into the binary.

## What violates this rule

- Business logic functions defined only in the binary entry point.
- Types used across module boundaries defined only in `main`.
- A test file that needs to spawn a subprocess to exercise domain logic.
- A `main` file longer than ~30 lines (excluding imports).

## What complies

- Library module that re-exports all submodules.
- Binary that imports from its own library package.
- Test files that import the library directly.

## Anti-patterns

- "I'll extract it later" -- logic that starts in `main` calcifies there.
- Building a complete standalone binary, then trying to make it testable.
- Polishing internal algorithms before the public interface exists.

## Complements

- `integrate-early` -- can't integrate early if logic is locked inside a binary.
- `testing-methodology` -- unit tests require importable functions, not subprocesses.
