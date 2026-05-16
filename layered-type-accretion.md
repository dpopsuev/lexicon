# Layered Type Accretion

**Origin:** Hybrid pattern. Combines Domain-Driven Design (Bounded Context), Onion Architecture (inward dependencies), and Accretion (grow, never modify). Coined for the PTP Clock Centric Test Suite specification (2026).

## Definition

A design method for building domain models incrementally across conceptual layers. Types are declared once but grow as each layer adds fields from its domain. Every field is tagged with the layer that introduced it. The complete type at any layer is the accumulation of all layers below it.

## The Three Ingredients

### From Bounded Context (DDD)

Each layer is a mini bounded context with its own vocabulary. Layer 1 (Protocol) speaks in clock types and clock classes. Layer 2 (Hardware) speaks in NIC families and sync methods. Layer 3 (Linux) speaks in services and processes. Layer 4 (OCP) speaks in CRs and profiles. The same concept (e.g., "holdover") means different things at different layers: a protocol state (Layer 1), a hardware capability (Layer 2), a service behaviour (Layer 3), a CR field (Layer 4).

### From Onion Architecture

Dependencies point inward. Layer 1 types have no knowledge of Layer 2 types. Layer 2 types reference Layer 1 types but not Layer 3. The struct grows outward: inner fields are defined first, outer fields are added later. The domain core (protocol types, trait constants) is stable; the periphery (CR parsing, environment) absorbs change.

### From Accretion

Types grow by addition. No field is removed. No field changes meaning. Each layer adds fields alongside existing ones. The complete type at Layer 4 contains every field from Layers 1-4. Old consumers that only use Layer 1 fields continue to work. New consumers use the full type.

## The Rules

1. A type is declared in the layer where its first field appears.
2. When a higher layer adds fields, the type is redeclared with all fields, grouped by layer tag.
3. Standalone declarations (enums, constants) get `// layer N name` above, plus a description comment.
4. Struct fields get `// layer N name` as a section separator inside the struct.
5. Each layer's Complete Picture shows the full accumulated state of all types through that layer.
6. Methods on a type are introduced in the implementation interface layers (3-4), not in the domain type layers (1-2). Data-derived stubs are acceptable.

## Example

```go
// After Layer 1: just the trait set
type Clock struct {
    // layer 1 protocol
    Traits    TraitSet
}

// After Layer 2: hardware fields added
type Clock struct {
    // layer 1 protocol
    Traits    TraitSet

    // layer 2 hardware
    NICs      []*NICInfo
    Ports     []*InterfaceInfo
}

// After Layer 3: service composition added
type Clock struct {
    // layer 1 protocol
    Traits    TraitSet

    // layer 2 hardware
    NICs      []*NICInfo
    Ports     []*InterfaceInfo

    // layer 3 linux
    Services  []*ServiceInstance
}

// After Layer 4: OCP context added (final form)
type Clock struct {
    // layer 1 protocol
    Traits    TraitSet

    // layer 2 hardware
    NICs      []*NICInfo
    Ports     []*InterfaceInfo

    // layer 3 linux
    Services  []*ServiceInstance

    // layer 4 ocp
    Profiles  []*ProfileInfo
    Node      string
}
```

The reader encounters Clock four times. Each time it has more fields, each tagged with provenance. The final form is the complete domain model.

## Temporal Accretion

An extension of the pattern where the API itself evolves over time (e.g., OCP versions 4.20, 4.21, 4.22). Each version adds to the API surface. The domain model absorbs each addition through parser strategies and trait derivation rules, without modifying existing types or breaking existing consumers.

## Smell Detector

Violations of Layered Type Accretion:

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

| Pattern | What it shares with LTA | How LTA differs |
|---------|------------------------|--------------  |
| Bounded Context | Layered vocabulary, explicit boundaries | LTA layers share types; Bounded Contexts don't |
| Onion Architecture | Inward dependency direction | LTA focuses on type growth, not module structure |
| Accretion | Grow by addition, never modify | LTA adds provenance tagging and smell detection |
| Entity-Component-System | Entity with composed components, derived properties | LTA's "traits" are ECS components derived by the Taxonomer |
| Progressive Disclosure | Reader learns incrementally | LTA applies this to type definitions, not UI |

## Sources

- Evans, E. *Domain-Driven Design* (2003) -- Bounded Context
- Palermo, J. "The Onion Architecture" (2008)
- Cockburn, A. "Hexagonal Architecture" (2005)
- Hickey, R. "Spec-ulation" (2016) -- Accretion
- Martin, R. C. *Clean Architecture* (2017) -- Dependency Rule, OCP
- PTP Clock Centric Test Suite specification (2026) -- first application
