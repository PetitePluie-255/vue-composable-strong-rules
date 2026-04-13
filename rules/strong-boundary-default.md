---
title: Strong Boundary Default
impact: HIGH
impactDescription: prevents Vue SFC syntax from being mistaken for permission to keep growing page-level logic
type: capability
tags: vue, boundary, sfc, react, java, layering, discipline
---

# Strong Boundary Default

**Impact: HIGH** — prevents Vue SFC syntax from being mistaken for permission to keep growing page-level logic

Treat Vue architecture with strong boundaries by default.

Vue single-file components are a syntax convenience. They do **not** justify keeping business logic, async workflow, or feature orchestration in the page once that logic should live in a feature-local composable or helper.

## Heuristic

For architectural discipline, reason about Vue more like:

- **React hook-style concern splitting**
- **Java-style responsibility layering**

and less like:

- script-heavy modules that keep absorbing one more feature because the code is already there

This is a heuristic for **boundary discipline**, not an instruction to copy React APIs or Java directory names literally.

## What This Means in Practice

- do not give `.vue` files special permission to absorb state, requests, and workflow just because the file already owns the UI
- do not treat existing local precedent as proof of a healthy boundary
- do expect reusable behavior, async workflow, mutation lifecycle, and derived state to move into feature-local composables when the boundary calls for it
- do keep screen-only glue in the view

## Wrong Reasoning

```text
This is Vue, so keeping one more request and one more loading state in the page is still fine.
```

Problem:

- it lowers the architectural bar because the file is an SFC
- it assumes Vue should tolerate weaker boundaries than React or layered server code
- it turns syntax choice into architecture policy

## Correct Reasoning

```text
This file is a Vue SFC, but that does not change the responsibility boundary.
If this concern would be split in a React hook or a layered Java service flow, I should check whether the same split is needed here.
```

## Review Guidance

- Flag reasoning that treats Vue as inherently more tolerant of mixed concerns.
- Do not recommend React or Java structure literally; recommend their level of boundary discipline.
- Prefer review language like "Vue syntax does not lower the boundary bar" over framework-style debates.

## Reference

- [current-host-viability](current-host-viability.md)
- [minimum-compliant-architecture-priority](minimum-compliant-architecture-priority.md)
- [business-component-boundary](business-component-boundary.md)
