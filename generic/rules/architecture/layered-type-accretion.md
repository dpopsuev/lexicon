---
id: layered-type-accretion
title: Layered Type Accretion
description: "Build domain models incrementally across conceptual layers. Types declared once, grown by addition as each layer contributes fields tagged with their provenance."
labels: [architecture, ddd, design]
---

# Layered Type Accretion

**Origin:** Hybrid pattern combining Domain-Driven Design (Bounded Context), Onion Architecture (inward dependencies), and Accretion (grow, never modify).

## Definition

A design method for building domain models incrementally across conceptual layers. Types are declared once but grow as each layer adds fields from its domain. Every field is tagged with the layer that introduced it. The complete type at any layer is the accumulation of all layers below it.

## The Three Ingredients

### From Bounded Context (DDD)

Each layer is a mini bounded context with its own vocabulary. The same concept (e.g., "holdover") means different things at different layers: a protocol state (Layer 1), a hardware capability (Layer 2), a service behaviour (Layer 3), an external config field (Layer 4).

### From Onion Architecture

Dependencies point inward. Layer 1 types have no knowledge of Layer 2 types. The struct grows outward: inner fields are defined first, outer fields are added later. The domain core is stable; the periphery absorbs change.

### From Accretion

Types grow by addition. No field is removed. No field changes meaning. Each layer adds fields alongside existing ones. Old consumers that only use Layer 1 fields continue to work. New consumers use the full type.

## The Rules

1. A type is declared in the layer where its first field appears.
2. When a higher layer adds fields, the type is redeclared with all fields, grouped by layer tag.
3. Standalone declarations (enums, constants) get `// layer N name` above, plus a description comment.
4. Struct fields get `// layer N name` as a section separator inside the struct.
5. Each layer's Complete Picture shows the full accumulated state of all types through that layer.
6. Methods on a type are introduced in the implementation interface layers (3-4), not in the domain type layers (1-2).

## Example

```go
// After Layer 1: core identity
type Entity struct {
    // layer 1 identity
    Traits    TraitSet
}

// After Layer 2: physical composition added
type Entity struct {
    // layer 1 identity
    Traits    TraitSet

    // layer 2 physical
    Components []*Component
    Ports       []*Port
}

// After Layer 3: runtime composition added
type Entity struct {
    // layer 1 identity
    Traits    TraitSet

    // layer 2 physical
    Components []*Component
    Ports       []*Port

    // layer 3 runtime
    Services  []*ServiceInstance
}

// After Layer 4: external context added (final form)
type Entity struct {
    // layer 1 identity
    Traits    TraitSet

    // layer 2 physical
    Components []*Component
    Ports       []*Port

    // layer 3 runtime
    Services  []*ServiceInstance

    // layer 4 external
    Config  *Config
    Node    string
}
```

The reader encounters Entity four times. Each time it has more fields, each tagged with provenance. The final form is the complete domain model.

## Temporal Accretion

An extension of the pattern where the API itself evolves over time (e.g., versioned external systems). Each version adds to the API surface. The domain model absorbs each addition through parser strategies and trait derivation rules, without modifying existing types or breaking existing consumers.

## Smell Detector

| Smell | How to Detect |
|-------|--------------|
| **Layer leak downward** | A domain type layer (1-2) has a method or function. |
| **Layer leak upward** | An implementation layer (3-4) defines a new data field without a layer tag. |
| **Missing provenance** | A struct field has no layer tag comment. |
| **Phantom type** | A type is defined but never appears in any struct accumulation. |
| **Behaviour in data layer** | A switch statement or if-check appears in a Layer 1-2 type definition. |
| **Flat enum growth** | A type gains a new constant for every variant instead of composing traits. |
| **Stored trait** | A derived property is stored as a field instead of computed by the Taxonomer. |
| **Parallel detection** | Two packages derive the same fact from different sources. |

## Relationship to Other Patterns

| Pattern | Shared with LTA | How LTA differs |
|---------|----------------|-----------------|
| Bounded Context | Layered vocabulary, explicit boundaries | LTA layers share types; BCs don't |
| Onion Architecture | Inward dependency direction | LTA focuses on type growth, not module structure |
| Accretion | Grow by addition, never modify | LTA adds provenance tagging and smell detection |
| Entity-Component-System | Entity with composed components, derived properties | LTA's traits are ECS components derived by Taxonomer |

## Sources

- Evans, E. *Domain-Driven Design* (2003) — Bounded Context
- Palermo, J. "The Onion Architecture" (2008)
- Cockburn, A. "Hexagonal Architecture" (2005)
- Hickey, R. "Spec-ulation" (2016) — Accretion
- Martin, R. C. *Clean Architecture* (2017) — Dependency Rule, OCP
