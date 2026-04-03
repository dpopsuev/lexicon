---
id: factory-principles
title: Factory Principles
description: Manufacturing wisdom applied to software delivery. Toyota Production System, Lean, Deming, Theory of Constraints. Flow, quality built in, stop on defect, pull not push, eliminate waste.
labels: [architecture, process]
---

# Factory Principles

**The best software factories are designed like the best physical factories. The principles are the same — only the material changes.**

Reference: Toyota Production System (Taiichi Ohno), The Toyota Way (Jeffrey Liker), Lean Manufacturing, W. Edwards Deming, Theory of Constraints (Eliyahu Goldratt), Ford Assembly Line.

## The Eight Convergent Truths

Every great manufacturing system converges on these:

1. **Flow, not batch.** Work moves continuously. Blockage is visible and fixed immediately. Never stockpile unfinished work.
2. **Quality is built in, not inspected in.** Error-proofing (poka-yoke) is better than testing, which is better than inspection, which is better than blame.
3. **Stop on defect (Jidoka).** Never pass a defect downstream. The line stops. Fix the root cause before resuming.
4. **Pull, not push.** Workers pull work when ready. No overproduction. No speculative features.
5. **Eliminate waste (Muda).** Every action that doesn't add value is waste. Seven types -- all must be identified and removed.
6. **Go and see (Genchi Genbutsu).** Don't decide from reports. Observe the actual system. Production data on the code, not dashboards.
7. **Respect the constraint.** The chain is as strong as its weakest link. Find THE bottleneck and fix it. Don't optimize everything.
8. **Continuous improvement (Kaizen).** Small, daily, forever. Not big redesigns -- incremental refinement every cycle.

## The Seven Wastes in Software

| Waste | Manufacturing | Software Equivalent | How to Eliminate |
|-------|-------------|--------------------|--------------------|
| **Transport** | Moving parts between stations | Context switching between repos, tools, environments | Unified spatial view. MCP ports. One IDE. |
| **Inventory** | Stockpiled unfinished goods | Unmerged branches, open PRs, specs without tasks | WIP limits. Pull system. Finish before starting new. |
| **Motion** | Unnecessary worker movement | Agent re-reading files, redundant queries, rebuilding context | Scoped tiers. Each agent sees only what it needs. |
| **Waiting** | Idle machines, idle workers | Blocked on review, dependency, permission, CI queue | Automation. Mechanical gates. Smart permission batching. |
| **Overproduction** | Making more than ordered | Premature abstractions, unused features, speculative code | Pull system. Just-in-Time. Build what's needed now. |
| **Over-processing** | Unnecessary refinement | Gold-plating, optimizing non-bottlenecks, polishing dead code | Theory of Constraints. Optimize the bottleneck only. |
| **Defects** | Rework, scrap, returns | Bugs found late, reverted PRs, failed deploys, incidents | Poka-yoke. Contract tests. Stop on defect. Shift left. |

## Toyota Production System Principles

### Jidoka (Automation with a Human Touch)

The machine stops itself when it detects a defect. The human decides what to do -- the machine ensures defects never pass downstream silently.

- Test gates halt workstreams on failure. Never skip a red test.
- Watchdogs halt on anomaly. Security watchdog bypasses command chain.
- The operator is the human touch -- approves, overrides, decides.

### Just-in-Time

Produce only what's needed, when it's needed, in the amount needed. No inventory.

- Agents activate on demand, not speculatively.
- Tier N doesn't start until Tier N-1's output is validated.
- Don't generate code that isn't needed by the current task.

### Andon (Stop the Line)

Any worker can stop the production line when they see a problem. No fear, no blame -- stopping is the RIGHT response.

- Any watchdog can halt a workstream.
- Permission gates are andon cords -- the operator sees the problem and decides.
- Blameless postmortems. Error budgets. The system catches defects, not the blame chain.

### Poka-yoke (Error-Proofing)

Design the process so mistakes are impossible, not just detectable.

- Type systems over runtime checks.
- Contract tests at port boundaries over manual integration testing.
- Import direction enforcement (static analysis) over code review comments.
- Templates with required sections over "remember to think about security."

### Heijunka (Level the Workload)

Uneven workload causes quality problems. Level it.

- Broker distributes across workstreams evenly.
- Token budgets prevent one agent from consuming all resources.
- Don't overload one tier while another idles.

## Deming's Key Insights

### Build Quality In

"Cease dependence on inspection to achieve quality." Don't rely on review gates. Build quality into the process via poka-yoke, contract tests, type systems, and mechanical enforcement.

### Drive Out Fear

"No one can put in best performance unless feeling secure." Blameless postmortems. Error budgets give permission to fail within bounds. The system catches defects -- individuals aren't blamed for systemic problems.

### The System Produces the Results

"A bad system will beat a good person every time." Individual agent skill matters less than process architecture. A well-designed factory with mediocre agents outperforms a poorly-designed factory with brilliant ones.

### Constancy of Purpose

"Create constancy of purpose toward improvement." The constraint funnel (need -> goal -> spec -> task) IS constancy of purpose. Every artifact traces to the north star.

## Theory of Constraints

The bottleneck determines the throughput of the entire system. The five focusing steps:

1. **Identify** the constraint -- which phase or gate fails most? Which takes longest?
2. **Exploit** it -- make the bottleneck more effective without adding resources (better prompts, narrower scope, cached context).
3. **Subordinate** everything else -- other phases don't outpace the bottleneck. No inventory buildup.
4. **Elevate** if exploitation isn't enough -- invest in the bottleneck (better model, more context, split the work).
5. **Repeat** -- the bottleneck moves. Find the new one. Never stop looking.

## Anti-Patterns

- **Batch and queue.** Accumulating work in large batches then processing all at once. Hides defects, delays feedback, creates inventory waste.
- **Push system.** Assigning work to agents regardless of their capacity. Creates overburden (muri) and unevenness (mura).
- **Optimizing non-bottlenecks.** Making a fast phase faster while the slow phase stays slow. Zero improvement in throughput.
- **Blame culture.** Punishing individuals for systemic failures. Drives fear, hides problems, prevents kaizen.
- **Skipping gates under pressure.** The production line exists to catch defects. Stopping it under deadline pressure to "go faster" creates defects that cost 10x to fix later.

## Complements

- `coordination-as-code` -- codified coordination IS standardized work (Toyota principle 6)
- `defense-in-depth` -- Swiss Cheese model IS jidoka at every station
- `operational-quality` -- SRE error budgets ARE Deming's "drive out fear"
- `testing-methodology` -- ROGYB cycle IS kaizen (continuous improvement per iteration)
- `trust-boundaries` -- poka-yoke at every trust boundary
