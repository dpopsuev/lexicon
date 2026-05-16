---
id: ports-and-adapters
title: "Ports and Adapters (Hexagonal Architecture)"
description: "Alistair Cockburn's pattern: application equally driven by users, tests, or programs"
labels: [pragmatic, architecture, hexagonal, ports-adapters]
---

# Ports and Adapters (Hexagonal Architecture)

**Core idea:** Allow an application to equally be driven by users, programs, automated tests, or batch scripts, and to be developed and tested in isolation from its eventual run-time devices and databases. — Alistair Cockburn

## Why a Hexagon?

Not because six is special. Cockburn wanted to break the reflex of drawing rectangles with "user on top, database on bottom." The hexagon gives room to draw ports and adapters without implying layers.

## Key Concepts

### Ports
A port identifies a purposeful conversation. Most apps have two: the driving side (user, API) and the driven side (database, external service). Cockburn names them: `ForPlacingOrders`, `ForStoringUsers`.

### Adapters
Multiple adapters per port. A port might have: a human UI adapter, a REST API adapter, a test harness adapter, a CLI adapter. All satisfy the same port interface.

### Primary (Driving) vs Secondary (Driven)
- **Primary/Driving** — actors that drive the application (user, test, API client)
- **Secondary/Driven** — actors the application drives (database, email, external API)

## The Symmetry Insight

The key insight is symmetry: the application doesn't care if it's driven by a human or a test. The functional specification is made against the hexagon's interface, not against any external technology.

## Applied to Event-Driven Agent Systems

In an AI agent built on an event bus, hexagonal architecture maps cleanly:

```
[Human / stdin]           [LLM provider / API]
      │                           │
 Primary Adapter           Application Core         Secondary Adapters
 (translates input   →  [LLMOrgan: reasoning] → (translate commands to
  into primary port)     subscribes primary port   secondary port calls)
                         publishes secondary ports
                                  │
               ┌──────────────────┼──────────────────┐
               ↓                  ↓                   ↓
         motor/fs.*          motor/shell.*        motor/web.*
              │                   │                   │
         [FsOrgan]          [ShellOrgan]          [WebOrgan]
        (secondary          (secondary             (secondary
         adapter)            adapter)               adapter)
              │                   │                   │
        [filesystem]          [shell]              [internet]
```

**Port cardinality rules:**
- **Primary reasoning port** (`sense/dialog.message`) — required, exactly one adapter (the reasoning organ)
- **Secondary ports** (`motor/fs.*`, `motor/shell.*`) — optional, at most one adapter each
- Multiple adapters on a secondary port = undefined behaviour (PortRegistry warns)

**PortRegistry** — validates at boot that all required ports have exactly one adapter plugged in. If the reasoning port has zero adapters, the agent cannot respond. If it has two, they race.

This is the hexagonal contract: the application core depends on ports (abstractions), never on concrete adapters (organs).
