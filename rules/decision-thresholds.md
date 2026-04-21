---
title: Decision Thresholds
impact: HIGH
impactDescription: determines when the agent must ask, must confirm, or may proceed after the implementation judgment
type: capability
tags: ask, confirm, ambiguity, compatibility, risk
---

# Decision Thresholds

**Impact: HIGH** — determines when the agent must ask, must confirm, or may proceed after the implementation judgment

After producing the implementation judgment, explicitly choose one of three next actions:

- **Proceed**
- **Ask**
- **Confirm**

Do not treat every task as needing a question. Do not treat every task as safe to infer. Use thresholds.

## Ask When

Ask before editing when any of these are true:

- two or more plausible implementations produce different user-visible behavior
- key product intent is missing, so coding now would likely implement the wrong thing
- a user request conflicts with repository rules or this skill's architecture rules
- the required behavior depends on a product choice rather than a technical inference

### Example

```text
Ambiguity:
- "retry failed order sync" could mean immediate retry, queued retry, or retry after confirmation

Required next step:
- Ask which retry behavior is intended before editing
```

## Confirm When

Confirm before editing when any of these are true:

- deleting or modifying key configuration
- changing public API signatures or exported behavior with external consumers
- changing shared types or interfaces with likely downstream impact
- changing store schema, persistence shape, or hydration expectations
- reorganizing directories or renaming exported modules
- changing existing behavior in a way that may break compatibility beyond the current local screen

### Example

```text
High-impact change:
- request requires changing the persisted filter schema used across pages

Required next step:
- confirm before editing because existing persisted state may break
```

## Proceed When

Proceed after the implementation judgment when all of these are true:

- behavior is clear enough to implement safely
- no meaningful product ambiguity remains
- no high-impact change threshold is crossed
- the architecture path is compliant

Proceeding is correct when the task is local, low-risk, and behaviorally clear. Do not create unnecessary round-trips.

## Key Principle

Ask for missing intent. Confirm for high-impact change. Proceed for clear local work.

## Reference

- [implementation-judgment-first](implementation-judgment-first.md)
- [architecture-conflict-protocol](architecture-conflict-protocol.md)
- [scope-guard-refactors](scope-guard-refactors.md)
