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

## Applied to Event-Driven Systems

In a system built on an event bus, hexagonal architecture maps cleanly:

```
[Primary Driver]            Application Core           [Secondary Driven]
 (user, test,     →  (depends only on port    →  (database, API, shell,
  API client)          interfaces, no adapters)    filesystem, LLM)
```

**Port cardinality rules:**
- **Primary driving port** — required; exactly one adapter per port (driving actor)
- **Secondary driven ports** — optional; at most one adapter each (driven actor)
- Multiple adapters on the same port = race condition or undefined routing

**Boot validation** — a registry validates at startup that all required ports have exactly one adapter plugged in. Zero adapters = the application cannot respond. Two adapters = they race.

This is the hexagonal contract: the application core depends on ports (abstractions), never on concrete adapters (infrastructure).

## Complements

- `domain-driven-design` — DDD provides the content of the center: aggregates, value objects, domain services, repositories as port interfaces. Hexagonal provides the structure around it.
- `domain-centric-design` — the synthesis of hexagonal, DDD, and BDD as a coherent system
- `gherkin-specification` — BDD scenarios are specifications for port behavior; hexagonal enables testing them without infrastructure
