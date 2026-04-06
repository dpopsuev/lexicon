---
id: long-method
title: "Long Method"
description: "Long methods are hard to read, test, and reuse."
labels: [code-smells, bloaters]
---

# Long Method

**Signs:**
- Method exceeds 40 lines
- Multiple indent levels (>3 deep)
- Comments explaining sections of code within the method

**Why it's bad:** Long methods are hard to read, test, and reuse. Each section is a hidden responsibility.

**Treatment:**
- Extract Method — move sections into named functions
- Replace Temp with Query — eliminate explanatory variables
- Decompose Conditional — extract condition branches

**Detection (Go):**
- `funlen` linter: >120 lines or >70 statements
- `gocyclo`: cyclomatic complexity >20

**SOLID violation:** SRP — method does more than one thing

**Reference:** https://refactoring.guru/smells/long-method
