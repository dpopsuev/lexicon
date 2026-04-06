---
id: service-oriented
title: "Service-Oriented Architecture (SOA)"
description: "Organize software as discoverable, reusable services communicating over well-defined interfaces."
labels: [paradigm, soa, architecture, distributed]
---

# Service-Oriented Architecture

**Core idea:** Break applications into services that communicate over well-defined interfaces. Services are discoverable, reusable, and independently deployable.

## Key Principles

- **Loose coupling** — services minimize dependencies
- **Abstraction** — hide implementation details
- **Autonomy** — services control their own logic and data
- **Discoverability** — services can be found and consumed dynamically
- **Composability** — services combine into higher-level processes

## Microservices = SOA + DevOps

Microservices are SOA refined with DevOps practices:
- One service per bounded context (DDD)
- Independent deployment per service
- Decentralized data management
- Smart endpoints, dumb pipes

## Djinn Alignment

- MCP = service interface protocol
- Scribe, Lex, Locus = services (discovered via MCP)
- Parchment, Ordo, Oculus = libraries (imported directly, not services)
- Day 1: libraries. Day 2: services enhance.

**Reference:** https://en.wikipedia.org/wiki/Service-oriented_architecture
