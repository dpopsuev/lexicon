---
id: ports-and-adapters
title: "Ports and Adapters (Hexagonal Architecture)"
description: "Alistair Cockburn's pattern: application equally driven by users, tests, or programs"
labels: [architecture, hexagonal, ports-adapters]
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

## Djinn Alignment

- Mirage is an adapter (overlay, Kata, K8s sandbox behind same Space interface)
- Troupe is an adapter (ACP, HTTP, mock behind same Driver interface)
- Parchment is an adapter (SQLite, Memory behind same Store interface)

**Reference:** https://alistair.cockburn.us/hexagonal-architecture/
**Book:** Alistair Cockburn & Juan Manuel Garrido de Paz (2024)
