---
id: event-driven-programming
title: "Event-Driven Programming"
description: "Flow determined by events: user actions, sensor outputs, messages. Decouple producers from consumers."
labels: [paradigm, event-driven, architecture]
---

# Event-Driven Programming

**Core idea:** Program flow is determined by events — things that happen. Producers emit events, consumers react. Neither knows about the other.

## Key Concepts

- **Event** — something that happened (signal, message, state change)
- **Producer** — emits events (doesn't know who's listening)
- **Consumer** — reacts to events (doesn't know who emitted)
- **Event bus** — transport between producers and consumers
- **Event loop** — processes events sequentially

## Patterns

| Pattern | What |
|---|---|
| **Publish/Subscribe** | Many consumers per event type |
| **Event Sourcing** | Store events, derive state |
| **CQRS** | Separate read and write models |
| **Observer** | Object notifies dependents of state change |

## Djinn Alignment

- `event/` package — EventSink/EventSource interfaces
- `telemetry/` signals — event-driven health monitoring
- `terminal.ViewEvent` — TUI subscribes to agent events
- Reconciliation Circuit — event-driven control loop

**Reference:** https://en.wikipedia.org/wiki/Event-driven_programming
