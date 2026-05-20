---
id: bounded-context
title: Bounded Context
description: "An explicit boundary within which a domain model is defined and applicable. Inside: one precise meaning per term. Outside: the same term may mean something different."
labels: [architecture, ddd]
---

# Bounded Context

**Origin:** Domain-Driven Design (Eric Evans, 2003)

## Definition

A Bounded Context is an explicit boundary within which a domain model is defined and applicable. Inside the boundary, every term has a precise, unambiguous meaning. Outside, the same term may mean something different.

## Core Idea

Large systems cannot have a single unified model. Different parts of the system have different views of the same concepts. A "User" in the authentication context means credentials and sessions. A "User" in the billing context means payment methods and invoices. A "User" in the analytics context means events and funnels.

Rather than forcing one definition, Bounded Contexts draw explicit borders. Within each context, the model is internally consistent. Between contexts, translation happens at the boundary.

## Structural Signals

- A package or module where all types share a coherent vocabulary.
- Types that mean different things in different packages.
- Translation code at boundaries: mappers, adapters, anti-corruption layers.
- Teams that own different contexts and evolve them independently.

## In Practice

- Each Bounded Context has its own types, its own rules, its own lifecycle.
- Contexts communicate through well-defined interfaces, not shared mutable state.
- A test suite is its own Bounded Context — it does not reuse the system's internal types directly. It reads external outputs and translates them into its own domain model.

## Relationship to Domain-Driven Design

Bounded Context is one of DDD's *strategic* patterns — it operates at the system level, defining where models begin and end. The *tactical* patterns (Entity, Aggregate, Repository) live inside a context. See `domain-driven-design.md` for the full picture.

## Sources

- Evans, E. *Domain-Driven Design: Tackling Complexity in the Heart of Software* (2003) — Chapters 14-15
- Vernon, V. *Implementing Domain-Driven Design* (2013) — Bounded Context mapping
