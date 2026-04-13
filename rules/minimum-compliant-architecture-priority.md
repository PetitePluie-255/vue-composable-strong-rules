---
title: Minimum Compliant Architecture Priority
impact: HIGH
impactDescription: makes boundary compliance outrank the smallest diff or preserving existing file boundaries
type: capability
tags: priority, architecture, boundary, minimality, diff, refactor
---

# Minimum Compliant Architecture Priority

**Impact: HIGH** — makes boundary compliance outrank the smallest diff or preserving existing file boundaries

In this skill, "minimal" means the smallest feature-local architecture that is still compliant. It does **not** mean the fewest changed lines or the fewest touched files.

## Priority Order

For implementation work, use this order:

1. explicit user instructions
2. repository-local rules such as `AGENTS.md`
3. system or tooling constraints
4. smallest compliant architecture
5. minimizing edits, preserving current file boundaries, or matching nearby anti-patterns

If items 4 and 5 conflict, item 4 wins every time.

## Required Pre-Edit Decision

Before editing, answer:

1. What is the smallest diff?
2. Is that option boundary-compliant?
3. If not, what is the smallest compliant architecture?
4. Implement that instead.

Do not skip this decision just because the task is a bug fix or a small follow-up.

## Common Rationalizations to Reject

- "This is only a bugfix."
- "The code already lives here."
- "I should avoid touching more files."
- "I can keep it here for now and extract later."
- "Nearby code already uses this boundary."

Those reasons may justify avoiding a broad refactor. They do **not** justify preserving the wrong layer when a small feature-local boundary correction would fix it.

## Allowed Corrections

These still count as minimal when they are required for compliance:

- creating one new feature-local composable
- splitting an overloaded composable into a couple of cohesive feature-local units
- extracting tree helpers, workflow coordination, or mutation lifecycle into local units
- updating same-feature imports after that extraction

These are not required by this rule:

- repo-wide renames
- new global patterns without local need
- unrelated cleanup in neighboring features
- public API changes not required by the request

## Review Guidance

- Flag changes that keep logic in the wrong layer only to preserve the smallest diff.
- Treat "follow-up cleanup later" as a warning sign, not a valid compliance strategy.
- Prefer review language like "do the smallest feature-local split first" over vague comments about cleanliness.

## Reference

- [boundary-first-minimality](boundary-first-minimality.md)
- [composable-weight-boundary](composable-weight-boundary.md)
- [single-purpose-composables](single-purpose-composables.md)
