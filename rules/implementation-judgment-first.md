---
title: Implementation Judgment First
impact: HIGH
impactDescription: prevents coding from starting before intended behavior, placement, and major risks are explicit
type: capability
tags: judgment, planning, implementation, behavior, risk
---

# Implementation Judgment First

**Impact: HIGH** — prevents coding from starting before intended behavior, placement, and major risks are explicit

Before editing Vue implementation code, produce an implementation judgment first.

This is not a formal spec. It is the minimum pre-edit judgment needed for a strong-rule implementer to avoid guessing, silent drift, or happy-path-only execution.

## Required Judgment

Answer these before editing:

1. What behavior is changing?
2. What is the recommended implementation path?
3. Where should the logic live and why?
4. What key risks, ambiguities, or rule conflicts exist?
5. Does this task require asking the user something first?
6. Does this task require confirmation before editing?

For routine Vue work, the judgment can be a few sentences. Do not skip it.

## Why This Exists

Without an explicit implementation judgment, agents tend to:

- start coding based on the first plausible interpretation
- discover behavioral ambiguity too late
- silently choose one implementation path when multiple user-visible paths exist
- treat architecture as something to clean up later

That is not strong-rule implementation. The judgment must come first.

## Correct Shape

```text
Understanding:
- Add row-level retry to the failed-job list

Recommended path:
- Keep click wiring in the table view
- Extract retry workflow state and request lifecycle into a feature-local composable

Key risks:
- Retry semantics are ambiguous: immediate retry vs retry with confirmation
- Existing page already mixes fetch and mutation workflow

Next step:
- Ask whether retry should execute immediately or require user confirmation
```

## Review Guidance

- Flag edits that begin coding before intended behavior and placement are explicit.
- Do not require a heavyweight design artifact for routine tasks.
- Prefer "show the implementation judgment first" over "write a full spec."

## Reference

- [decision-thresholds](decision-thresholds.md)
- [architecture-planning-gate](architecture-planning-gate.md)
- [minimum-compliant-architecture-priority](minimum-compliant-architecture-priority.md)
