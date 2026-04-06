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

## Djinn Alignment

- Troupe Actor = the actor model (Perform receives messages, produces responses)
- GenSec spawns child actors (2Sec, Executor)
- Agents communicate through Substrate (message bus), not shared state
- Each agent processes one intent at a time

## Implementations

- Erlang/OTP (original), Akka (JVM), Orleans (.NET), Troupe (Go/Djinn)

**Reference:** https://en.wikipedia.org/wiki/Actor_model
