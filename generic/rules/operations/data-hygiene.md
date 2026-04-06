---
id: data-hygiene
title: Data Hygiene
description: Scrub internal hostnames, secrets, and PII before committing
labels: [security, hygiene]
---

# Data Hygiene

Internal infrastructure identifiers must not appear in committed files. Use placeholder values.

## Agent behavior

- **Before committing:** verify no internal hostname patterns appear in staged files.
- **When writing examples, docs, or config:** use placeholder values (e.g., `your-service.example.com`).
- **When fetching data from external APIs at runtime:** store raw responses in gitignored directories only.
- **If a real hostname is needed in code:** parameterize via environment variable, CLI flag, or gitignored config.
- Raw secrets, PII, and internal hostnames in committed files are treated as bugs.
