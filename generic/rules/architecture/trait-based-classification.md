---
id: trait-based-classification
title: Trait-Based Classification
description: "Entities earn labels (traits) based on observable components, not declared types. Nothing is declared as a type — it becomes one because its components match the rules."
labels: [architecture, design, patterns]
---

# Trait-Based Classification

**Origin:** Entity-Component-System (ECS) architecture adapted for domain modelling.

## Definition

A classification system where entities earn declarative labels (traits) based on their observable components, rather than being assigned a type at creation. Nothing is declared as a type. It *becomes* a type because its components match the rules.

## The Two Halves

### Traits

Traits are string labels stored in a `TraitSet` on the entity. They are the declarative vocabulary. Traits are not stored by the developer; they are derived by the Taxonomer from the entity's components.

Two levels of traits:

**Primitive traits**: derived directly from one component or field.

**Compound traits**: derived from combinations of primitive traits. Compound rules are registered, not hardcoded in a switch.

### Taxonomer

The classification engine. An interface that takes an entity's components and produces a `TraitSet`. Runs once at discovery time. Recomputes when components change.

```go
type Taxonomer interface {
    Classify(entity *Entity) TraitSet
}
```

The Taxonomer is a Strategy: different environments or future requirements can swap classification rules without changing the entity struct or the query API.

## How It Differs from Enum-Based Classification

| Aspect | Enum-based | Trait-based |
|--------|-----------|-------------|
| Adding a new variant | New constant, new case in switch, new counter field | New trait rule. Existing rules untouched. |
| Compound types | Explicit constant per combination | Composed from primitives |
| Querying | `switch type` or counter check | `Is("T-X")` checks `TraitSet.Has("T-X")` |
| What changes when a variant is added | Every consumer that switches on the enum | Only the Taxonomer rules |
| Multiple axes | One enum per axis, or one mega-enum | One TraitSet with all traits from all axes |

## The Connection to Declarative/Imperative

Traits are the declarative side. Components are the imperative side. The Taxonomer is the bridge.

- **Dog** is the trait. **Walks on four, barks, mammal** are the components. The Taxonomer says: these components match the rule for "dog."

Nothing is declared as a Dog. It *is* a Dog because it matches.

## Lifecycle

1. **Discovery**: populate entity components from raw data sources.
2. **Classification**: Taxonomer derives primitive traits, then compound traits. Stores in `TraitSet`.
3. **Query**: `Is("X")` checks `TraitSet.Has("X")`. No switch, no enum.
4. **Mutation**: test or system changes a component.
5. **Refresh**: re-populate components. Taxonomer re-derives traits.
6. **Re-query**: traits may now differ if mutation removed a required component.

## Smell: Stored Trait

If a trait is stored as a field on the entity instead of computed by the Taxonomer, it's a smell. The stored value can go stale after a mutation. The Taxonomer-derived trait is always current.

## Sources

- Entity-Component-System architecture (game development, 2000s)
- Hickey, R. "Spec-ulation" (2016) — Accretion, grow by addition
