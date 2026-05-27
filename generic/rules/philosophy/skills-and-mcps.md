---
id: skills-and-mcps
title: Skills & MCPs — Knowledge and Tools
description: Skills encode what to do. MCPs provide how to reach things. Compose them deliberately — the skill orchestrates, the MCP executes.
labels: [pragmatic, architecture, agentic, process]
---

# Skills & MCPs — Knowledge and Tools

**Skills are the knowledge of how to build the bookshelf. MCPs are the hammer and saw.**

## The Distinction

| Layer | Role | Answers |
|---|---|---|
| **Skills** | Packaged workflow, domain expertise, procedural knowledge | *What to do, in what order, under what constraints* |
| **MCPs** | External systems, data connections, tool execution | *How to reach things, how to invoke capabilities* |
| **Tools** | Low-level primitives (bash, filesystem, HTTP) | *How to do the atomic action* |

Skills load into the agent's context as instructions. MCPs surface as callable tools. The skill tells the agent which tools to call and when — the MCP makes those calls possible.

## Composition Model

A skill is the orchestrator. MCPs are the executors. Tools are the primitives.

```
Skill (workflow + domain knowledge)
  └─ calls MCP A (external system 1)
  └─ calls MCP B (external system 2)
  └─ calls built-in tools (bash, read, write)
```

The skill defines the sequence, the filters, the invariants, and the output shape. The MCPs supply the data and execute the side effects.

## Progressive Disclosure

Skills protect the context window via three tiers:

1. **Description only** — always in context (~30–50 tokens). Must precisely describe the trigger condition.
2. **Full SKILL.md** — loads when the task matches the description.
3. **Reference files** — loaded on demand during execution.

Write descriptions as trigger specs, not marketing copy. The agent uses the description to decide whether to load the skill at all.

## Rules

1. **Skill = what. MCP = how.** If you find yourself putting API call logic in a skill description, it belongs in an MCP. If you find yourself putting workflow logic in an MCP, it belongs in a skill.
2. **Skills orchestrate; MCPs execute.** A skill should never duplicate what an MCP already does — it should sequence MCP calls in the right order with the right guards.
3. **One skill per repeatable workflow.** If you re-explain the same process to an agent more than once, it belongs in a skill.
4. **MCPs are Day 2 enrichment.** Skills must describe what to do when an MCP is unavailable — graceful degradation, not hard failure. See `day-0-1-2`.
5. **Description is the only always-loaded text.** Every word in the description costs tokens on every session. Every word in the body costs tokens only when triggered. Front-load precision, back-load detail.
6. **Invariants belong in skills, not MCPs.** Business rules, filtering criteria, and domain-specific constraints live in the skill. MCPs are agnostic executors.

## Anti-Patterns

- **Fat descriptions.** A 500-word description that explains the entire workflow. The description triggers; the body instructs.
- **Skill-less MCP use.** Calling MCPs ad-hoc without a skill to govern sequencing — leads to the same workflow being re-invented every session.
- **MCP-less skills.** A skill that re-implements external system access via raw HTTP calls instead of using the MCP that already wraps it.
- **Monolithic skills.** One skill that does everything. Compose multiple focused skills instead.
- **Hardcoded tool calls in descriptions.** The description should describe the domain task, not the implementation.

## Complements

- `day-0-1-2` — MCPs are Day 2; skills must work without them at Day 1
- `coordination-as-code` — skills are codified workflows; they replace tribal knowledge
- `agentic-flywheel` — each skill built compounds into the next skill being easier to build
- `off-the-shelf-modules` — prefer existing MCP servers over rolling custom integrations
