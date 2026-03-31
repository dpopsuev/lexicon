---
id: naming
title: The Art of Naming
description: Names are hypotheses about reality that shape how developers think — choose them with the care they deserve
labels: [design, code-hygiene, conventions]
---

# The Art of Naming

Names aren't labels. They're **hypotheses about reality** that shape how thousands of developers think. A function name becomes a mental model. A type name prescribes a philosophy. Naming is consciousness being kind to itself across time.

Based on Kenneth Reitz's "The Art of Naming Things in Code."

## Relationship to other rules

This rule complements:
- `for-humans` — names ARE the for-humans surface. If the name reads like intent, the API is human-first.
- `effective-go` — constants over literals is a naming decision (the constant name IS the documentation).
- `code-smells` — bad names are a smell (Primitive Obsession, unclear intent).
- `human-readable-output` — names for humans, codes for machines.

## Principles

### 1. Names shape thinking

When you name a function `authenticate_user()`, every developer who reads it inherits your mental model. When you name it `proc_usr_auth_req()`, you impose your laziness on their cognition.

```go
// Machine-optimized — reader must decode
func procUsrAuthReq(uid string, pwdHash string) (*SessToken, error)

// Human-optimized — reader understands instantly
func AuthenticateUser(email string, passwordHash string) (*Session, error)
```

The second version lets readers breathe. Names carry mental models — invest in them.

### 2. Name what it IS, not how it works

```go
// Implementation-leaked
type JSONCircuitParser struct {}
type HTTPDispatcherWithRetry struct {}

// Intent-revealed
type CircuitLoader struct {}
type ResilientDispatcher struct {}
```

Names should point toward what something *is* rather than how it's implemented. Implementations change. Intent endures.

### 3. Semantic weight matters

Word choice propagates through design:

| Word | Implies | Natural verbs |
|------|---------|--------------|
| `user` | System consumer | authenticate, authorize, track |
| `person` | Social/legal entity | identify, contact, notify |
| `agent` | Autonomous actor | spawn, dispatch, terminate |
| `worker` | Task executor | assign, schedule, complete |
| `walker` | Graph traverser | enter, exit, traverse |

Choose the word that makes the *next* method name obvious. If you name it `agent`, then `agent.Spawn()` feels natural. If you name it `worker`, `worker.Spawn()` feels wrong — workers are assigned, not spawned.

### 4. Names must age well

```go
// Fragile — tied to current implementation
type XMLHttpRequest struct {}     // handles JSON more than XML now

// Durable — describes the role
type CircuitServer struct {}      // still correct after MCP→A2A migration
type ColorIdentity struct {}      // still correct at 100+ agents
```

Ask: will this name still make sense when the implementation changes? If not, you're naming the implementation, not the concept.

### 5. Metaphors illuminate AND constrain

"Orchestrator" illuminates coordination but constrains you to think in terms of musicians. "Factory" illuminates creation but constrains you to think in terms of assembly lines.

Choose metaphors deliberately. Test them: does this metaphor help or hinder the *next* feature?

Good metaphors in our codebase:
- **Circuit** — nodes, edges, walks. Scales to delegation, loops, shortcuts.
- **Heraldic naming** — "Denim Writer of Indigo Refactor." Scales to 56 agents.
- **Papercup** — lightweight, disposable coordination. Communicates the right weight.

Bad metaphors we avoided:
- "Pipeline" — implies linear flow. Circuits have loops and branches.
- "Container" — overloaded (Docker, DI, etc.). We use "shell" for Djinn.

## Rules

1. **Spend time on names.** A good name saves hours of documentation. A bad name creates hours of confusion. The naming moment is a genesis — future development orbits around it.

2. **Define by what you love, not what you hate.** "HTTP for Humans" beats "HTTP that doesn't suck." Positive framing attracts; negative framing calcifies resentment.

3. **Trust the reader's intelligence.** `profile_url()` over `get_url_for_profile()`. Don't explain what's obvious from context.

4. **Be specific enough to be meaningful, general enough to evolve.** `Walker` is better than `GraphNodeVisitorWithStateTracking`. But `Thing` is worse than both.

5. **Consistency over cleverness.** If the codebase uses `Load` for parsing, don't introduce `Parse` for the same operation. If it uses `New` for constructors, don't use `Create`.

6. **Names are the API.** In a well-named codebase, the README writes itself. If you can't explain the API in one sentence using the type/function names, the names are wrong.

## Anti-patterns

- **Abbreviated names** — `proc_usr_auth_req` saves 20 characters, costs 20 minutes of comprehension per reader.
- **Implementation names** — `JSONParser`, `HTTPClient` — what happens when you switch to YAML or gRPC?
- **Negation names** — `NotEmpty`, `DisableCache`, `NoRetry` — positive names (`HasContent`, `DirectAccess`, `SingleAttempt`) read better.
- **Historical baggage** — keeping a name because "that's what it was called" when the reality has changed. Rename.
- **Placeholder calcification** — `ProcessorThingy` in a PR becomes `ProcessorThingy` in production. Name it right or don't merge.

## Agent behavior

- **Before committing**: read every new name aloud. Does it sound like English? Does it describe intent?
- **When naming a type**: ask "what verbs follow naturally?" If the verbs feel awkward, the noun is wrong.
- **When naming a function**: ask "can I read this as a sentence?" `BuildGraph(def)` reads as "build graph from def." `DoStuff(x)` doesn't read as anything.
- **When renaming**: grep for the old name. Update every reference. Dead names are worse than dead code — they mislead.
