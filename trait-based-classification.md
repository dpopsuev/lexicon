# Trait-Based Classification

**Origin:** Entity-Component-System (ECS) architecture adapted for domain modelling. The Taxonomer pattern is the classification engine. Coined for the PTP Clock Centric Test Suite specification (2026).

## Definition

A classification system where entities earn declarative labels (traits) based on their observable components, rather than being assigned a type at creation. Nothing is declared as a type. It *becomes* a type because its components match the rules.

## The Two Halves

### Traits

Traits are string labels stored in a `TraitSet` on the entity. They are the declarative vocabulary: `"T-BC"`, `"holdover"`, `"E810"`, `"GNSS"`. Traits are not stored by the developer; they are derived by the Taxonomer from the entity's components.

Two levels of traits:

**Primitive traits**: derived directly from one component or field. `"OC"` (has only upstream ports), `"holdover"` (any NIC has DPLL), `"E810"` (leader NIC family).

**Compound traits**: derived from combinations of primitive traits. `"T-BC"` = `"BC"` + `"holdover"`. `"T-GM"` = `"GM"` + `"GNSS"` + `"holdover"`. Compound rules are registered, not hardcoded in a switch.

### Taxonomer

The classification engine. An interface that takes an entity's components and produces a `TraitSet`. Runs once at discovery time. Recomputes when components change (e.g., after a mutation + refresh).

```go
type ClockTaxonomer interface {
    Classify(clock *Clock) TraitSet
}
```

The Taxonomer is a Strategy: different environments or future requirements can swap classification rules without changing the entity struct or the query API.

## How It Differs from Enum-Based Classification

| Aspect | Enum-based | Trait-based |
|--------|-----------|-------------|
| Adding a new variant | New constant, new case in switch, new counter field | New trait rule. Existing rules untouched. |
| Compound types | Explicit constant per combination (`ProfileTypeTBC`) | Composed from primitives (`"BC"` + `"holdover"`) |
| Querying | `switch baseType` or `counter.TBC > 0` | `Is("T-BC")` checks `TraitSet.Has("T-BC")` |
| What changes when a variant is added | Every consumer that switches on the enum | Only the Taxonomer rules |
| Multiple axes | One enum per axis, or one mega-enum with all combinations | One TraitSet with all traits from all axes |

## The Connection to Declarative/Imperative

Traits are the declarative side. Components are the imperative side. The Taxonomer is the bridge.

- **Dog** is the trait. **Walks on four, barks, mammal** are the components. The Taxonomer says: these components match the rule for "dog."
- **T-BC** is the trait. **Upstream + downstream ports, holdover-capable NIC, two profiles linked via controllingProfile** are the components. The Taxonomer says: these components match the rule for "T-BC."

Nothing is declared as a T-BC. It *is* a T-BC because it matches.

## Lifecycle

1. **Discovery**: read CRs, parse configs, populate entity components (NICs, Ports, Services, Profiles).
2. **Classification**: Taxonomer reads components, derives primitive traits, then compound traits. Stores in `TraitSet`.
3. **Query**: `Is("T-BC")` checks `TraitSet.Has("T-BC")`. No switch, no enum, no hardcoded mapping.
4. **Mutation**: test changes a component (brings down a port, changes a config).
5. **Refresh**: Snapshotter re-reads CRs, re-populates components. Taxonomer re-derives traits. Traits may have changed.
6. **Re-query**: `Is("T-BC")` may now return false if the mutation removed a required component.

## Smell: Stored Trait

If a trait is stored as a field on the entity (e.g., `BaseType ClockBaseType`) instead of computed by the Taxonomer, it's a smell. The stored value can go stale after a mutation. The Taxonomer-derived trait is always current.

## Sources

- Entity-Component-System architecture (game development, 2000s)
- Hickey, R. "Spec-ulation" (2016) -- Accretion, grow by addition
- PTP Clock Centric Test Suite specification (2026) -- first application to test domain modelling
