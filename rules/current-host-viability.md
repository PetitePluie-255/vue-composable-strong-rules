---
title: Current Host Viability
impact: HIGH
impactDescription: prevents agents from treating an already overloaded component or composable as a compliant host for one more change
type: capability
tags: host, boundary, overload, drift, architecture, placement
---

# Current Host Viability

**Impact: HIGH** — prevents agents from treating an already overloaded component or composable as a compliant host for one more change

Before choosing the smallest compliant architecture, determine whether the current host is still a valid place for more logic.

If the current component or composable is already overloaded, mixing independently changing concerns, or showing obvious boundary drift, it is **not** a compliant host for additional behavior even when the new diff is small.

## Required Questions

Answer these before deciding to keep logic where it already lives:

1. Is the current host already carrying multiple independently changing concerns?
2. Does the new change introduce another concern such as async workflow, mutation lifecycle, derived state, validation, or orchestration?
3. Is "keep it here" being justified mainly by convenience, smaller diff, or existing local precedent?
4. Would one more nearby requirement immediately force the same logic to be extracted?

If the answer to the first question is yes, and either the second, third, or fourth is also yes, the current host is not viable.

## Non-Compliant Host Signals

- a component already mixes rendering, request logic, mutation handlers, modal coordination, and screen workflow
- a composable already owns multiple business capabilities and is still absorbing new actions
- each small feature adds "just one more handler" or "just one more loading flag" to the same unit
- the host is only considered acceptable because the change is small
- the architecture would still look wrong even if the new code were perfectly written

## Decision Rule

Do not ask only "can this new code fit here?"

Also ask "is this host still healthy enough to accept more code at all?"

If the host is not healthy, the smallest compliant architecture must include the smallest feature-local extraction or split needed to restore a valid boundary first.

## Example

### Wrong

```text
This export button is small, so I can keep its loading state, request handling, and success/error coordination in the existing list component.
```

Problem:

- the reasoning checks only the size of the new diff
- it ignores whether the list component is already overloaded
- it treats "small addition" as proof of compliance

### Right

```text
The export action is small, but the current list host is already carrying several unrelated behaviors.
Keeping one more async action here would preserve a drifting boundary.
I will do the smallest feature-local extraction needed before adding export.
```

## Review Guidance

- Flag cases where an agent argues from diff size without checking host health.
- Treat "one more action in this component/composable" as a warning sign, not neutral evidence.
- Prefer review language like "the current host is no longer a viable boundary" over vague suggestions to clean up later.

## Reference

- [composable-weight-boundary](composable-weight-boundary.md)
- [minimum-compliant-architecture-priority](minimum-compliant-architecture-priority.md)
- [boundary-first-minimality](boundary-first-minimality.md)
