---
title: Repository Convention First
impact: HIGH
impactDescription: keeps code placement aligned with the target repository instead of inventing a new structure
type: capability
tags: repository, convention, directory, placement, architecture, discipline
---

# Repository Convention First

**Impact: HIGH** — keeps code placement aligned with the target repository instead of inventing a new structure

Before recommending file placement or directory changes, follow the repository's existing conventions. Prefer explicit local docs first; if they are absent, infer the convention from the current directory structure and nearby feature modules.

## Symptoms

- Review feedback assumes a directory rule that the repository never defined
- A new composable is moved into `src/composables` even though the codebase consistently keeps feature-local composables beside the feature
- Similar modules in the same feature use one placement pattern, but the new change introduces another
- A proposed refactor adds a new top-level directory without showing that the current structure is inconsistent or causing the problem

## Problem Pattern

```text
Repository reality:
- No AGENTS.md or architecture doc
- Existing feature modules keep local composables under `src/features/order/composables`

AI review:
- "Move this to src/composables because composables belong there"

Problem:
- The recommendation invents a new repository convention instead of following the existing one.
```

## Fix

**Step 1: Check for explicit local rules**
- `AGENTS.md`
- contributor docs
- architecture notes
- feature-level README or module docs

**Step 2: If docs are absent, inspect the codebase**
- nearby feature modules
- established directory names
- placement patterns for similar composables or services

**Step 3: Recommend the smallest consistent placement**
```text
Repository reality:
- Similar API workflow composables live under src/features/api/composables

✅ Correct recommendation:
- Keep the new workflow composable under the same feature-local composables directory
- Only propose a new directory pattern if the current feature structure is inconsistent or directly causing the issue
```

## Decision Checklist

1. Is there an explicit local document that defines placement? → Follow it.
2. If not, do nearby modules show a stable pattern? → Follow that pattern.
3. Would your recommendation introduce a new directory convention? → Avoid it unless necessary.
4. Is the current structure itself inconsistent or causing the bug/maintenance issue? → If yes, call that out explicitly before proposing a new pattern.

## Reference

- [A Philosophy of Software Design: Deep Modules](https://web.stanford.edu/~ouster/cgi-bin/book.php)
