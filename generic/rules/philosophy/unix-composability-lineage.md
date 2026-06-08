---
id: unix-composability-lineage
title: The Unix Composability Lineage
description: Doug McIlroy's 1964 memo at Bell Labs proposed coupling programs like garden hose segments. Nine years later, Unix pipes were born. Sixty years later, AI agents rediscovered the same principle. The lineage is direct.
labels: [architecture, philosophy, history, unix, composability, agentic]
see_also: [agent-accessible-interfaces, bezos-mandate]
---

# The Unix Composability Lineage

## The Memo (1964)

Doug McIlroy wrote an internal memo at Bell Labs in 1964:

> "We should have some ways of coupling programs like garden hose — screw in another
> segment when it becomes necessary to massage data in another way."

The memo went nowhere for nine years. Then Ken Thompson implemented pipes in Unix. The
`|` character became the most productive punctuation mark in computing history.

The insight was not technical — it was compositional. Small programs that do one thing
well, connected by a universal medium (text), produce combinations their authors never
anticipated. `grep | sort | uniq | wc` was not designed as a system. It emerged from
parts that were designed to be connected.

## The Unix Philosophy (1978)

Doug McIlroy, Brian Kernighan, and Rob Pike articulated the principles in the Unix
Programmer's Manual:

1. Write programs that do one thing and do it well.
2. Write programs to work together.
3. Write programs to handle text streams, because that is a universal interface.

Rule 3 is the load-bearing one. Text as the universal interface means any program can
connect to any other program without negotiation. The pipe does not care what the programs
are — it just moves bytes. The complexity budget of the connector is zero, which is why
the complexity budget of the programs can be minimal. Small, composable, text in, text out.

## The Gap (1980s–2020s)

The Unix philosophy held in the Unix world. It did not hold at scale in the enterprise
world. GUIs, object models, RPC frameworks, XML, JSON APIs, microservices — each
generation added expressiveness at the cost of composability. The interface became richer.
The connector became heavier. The system became harder to compose.

The enterprise learned to couple programs tightly because tight coupling was faster to
ship. The Bezos mandate in 2002 was an attempt to undo this — to force service interfaces
back toward composability by decree. It worked inside Amazon because Rick Dalzell enforced
it. It did not generalize.

## The Return (2024–)

AI agents rediscovered composability from the other direction.

An agent is a consumer that reads tool descriptions at runtime, decides what to invoke,
observes the result, and decides what to do next. It does not have a build-time dependency
on the tools. It discovers them, composes them, and discards them. This is the pipe model,
elevated one level of abstraction.

**MCP as `/usr/bin`:** Anthropic's Model Context Protocol is the standard pipe fitting
for agents — a universal way to connect AI to tools and data sources. An MCP server is a
tool in `/usr/bin`. The agent discovers it, reads its description, and decides whether to
use it. The protocol is the universal medium, as text was in Unix.

**Fabric as the terminal:** Daniel Miessler's Fabric framework treats AI prompts like Unix
tools — small, composable "patterns" chained from the command line. Extract wisdom from a
podcast, summarize it, convert to action items. All piped together. The terminal is not
dead. It evolved.

**The command line returns:** The most capable coding agents operate primarily through shell and CLI — `git`, `curl`, `jq`, `rg`, `sqlite3`. The command line's design properties — text in, text out, exit codes, stdin/stdout/stderr — are exactly the right interface for an agent that observes and reacts.

## The Difference

Unix pipes are deterministic. Same input, same output, every time. A broken pipe throws
an error. An agent is probabilistic — same input may produce different outputs, and a
broken agent can confidently return garbage without signaling failure.

McIlroy waited nine years for the implementation to catch up with the idea. The
composability principle came first; the reliability came later, through convention,
testing, and accumulated tooling.

The gap between the principle (composable agent tools) and the reliability (deterministic,
auditable behavior) is where the field is now. The principle is established. The plumbing
is still getting tighter.

## The Lineage

```
1964  McIlroy memo — couple programs like garden hose
1973  Ken Thompson implements Unix pipes
1978  Unix philosophy articulated — one thing well, text as universal interface
2002  Bezos mandate — service interfaces, no back-doors (same principle, enterprise scale)
2011  Yegge rant — platform thinking requires the same discipline
2024  MCP — pipe fitting for agents, /usr/bin for the agentic era
2026  CLI renaissance — agents fluent in shell, git, curl; the terminal evolves
```

The principle is sixty years old. The implementation keeps catching up.
