---
id: reactive-programming
title: "Reactive Programming"
description: "Data streams and propagation of change. Values automatically update when dependencies change."
labels: [paradigm, reactive, dataflow]
---

# Reactive Programming

**Core idea:** Declare relationships between values. When a source changes, dependents update automatically. Streams of data, not snapshots.

## Key Concepts

- **Observable** — source of data over time
- **Observer** — reacts to data from observable
- **Operators** — transform, filter, combine streams
- **Backpressure** — handle fast producers with slow consumers

## Reactive Manifesto (2014)

Reactive systems are:
1. **Responsive** — respond in a timely manner
2. **Resilient** — stay responsive in the face of failure
3. **Elastic** — stay responsive under varying workload
4. **Message Driven** — rely on asynchronous message passing

## Djinn Alignment

- Signal bus = reactive stream of health signals
- Reconciliation Circuit = reactive loop (observe → react → observe)
- Roadmap Tree = reactive view of plan DAG changes

**Reference:** https://en.wikipedia.org/wiki/Reactive_programming
