---
id: actor-model
title: "Actor Model"
description: "Concurrent computation where actors communicate via messages. No shared state. Each actor processes one message at a time."
labels: [paradigm, concurrent, actor, distributed]
---

# Actor Model

**Core idea:** Everything is an actor. Actors communicate by sending messages. No shared state. Each actor has a mailbox and processes one message at a time.

## An Actor Can

1. **Send messages** to other actors
2. **Create new actors** (spawn children)
3. **Change its own behavior** for the next message

## Key Properties

- No shared memory — all communication via messages
- Location transparency — actors don't know where others are
- Fault tolerance — supervisor hierarchies (Erlang/OTP)
- Concurrency — millions of lightweight actors

## Implementations

- Erlang/OTP (original), Akka (JVM), Orleans (.NET), Pykka (Python)

**Reference:** https://en.wikipedia.org/wiki/Actor_model
