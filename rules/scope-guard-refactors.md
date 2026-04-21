---
title: Scope Guard Against Unrequested Refactors
impact: MEDIUM
impactDescription: prevents scope creep from broad architectural cleanup
type: capability
tags: scope, refactor, guard, architecture, discipline
---

# Scope Guard Against Unrequested Refactors

**Impact: MEDIUM** — prevents scope creep from broad architectural cleanup

This rule guards against opportunistic cleanup and repo-wide churn. It does **not** prohibit small feature-local refactors that are required to keep new logic in the correct layer.

When implementing a feature or fix, do not undertake broad refactors, directory reorganizations, or public API changes unless the user explicitly requested them.

## Symptoms

- A "small fix" PR touches 20+ files
- Public API signatures changed without migration plan
- Directory structure reorganized as a side effect of a feature task
- Shared contract types modified, breaking downstream consumers
- Review scope becomes unmanageable

## Problem Pattern

```
User request: "Add a date filter to the order list page"

AI response:
- ✅ Added date filter to OrderList
- ❌ Refactored all composables to use a new BaseComposable pattern
- ❌ Renamed 6 composable files to match new naming convention
- ❌ Moved store modules to a new directory structure
- ❌ Changed shared API response types
```

## Fix

**Step 1: Ship the smallest boundary-correct change**
```
User request: "Add a date filter to the order list page"

✅ Correct scope:
- Added useOrderFilters() composable with date range state
- Integrated date filter into OrderList view
- Updated fetchOrders() to pass date params
```

If the current structure cannot host the feature without violating placement rules, include the smallest feature-local refactor required to fix that boundary first.

```text
User request: "Add a date filter to the order list page"

Current structure problem:
- OrderList.vue already owns reusable query state and fetch coordination

✅ Still in scope:
- Extract useOrderFilters() into the feature's local composables directory
- Move the reusable query/watch logic out of OrderList.vue
- Wire the page back to the new composable

Reason:
- This is a local boundary correction required for compliance, not a broad cleanup
```

**Step 2: If you spot architectural issues, report them — don't fix them**
```text
⚠️ Potential improvement noticed:
The existing useOrderSearch() could be combined with the new
useOrderFilters() into a single useOrderQuery() composable.
This is out of scope for this task. Want me to create a follow-up?
```

## High-Impact Changes That MUST Be Confirmed

| Change type | Risk | Action |
|---|---|---|
| Public API signature change | Breaks downstream consumers | ⛔ Confirm before editing |
| Shared type/interface change | Breaks dependent modules | ⛔ Confirm before editing |
| Directory reorganization | Breaks imports project-wide | ⛔ Confirm before editing |
| Global store schema change | Breaks persistence/hydration | ⛔ Confirm before editing |
| Renaming exported functions | Breaks external consumers | ⛔ Confirm before editing |

## Allowed Without Extra Confirmation

These are usually still within scope when they are the smallest way to keep boundaries correct:

- moving reusable workflow logic from a view/component into a feature-local composable
- splitting one overweight feature composable into smaller feature-local composables
- creating a new feature-local composable to avoid keeping async or synchronization logic in a component
- adjusting imports inside the same feature after such a local extraction

## When This Conflicts With Boundary-Correct Path Priority

This rule and [minimum-compliant-architecture-priority](minimum-compliant-architecture-priority.md) can appear to conflict:

- `scope-guard-refactors` says do not widen scope unnecessarily
- `minimum-compliant-architecture-priority` says do not preserve the wrong boundary just to keep the diff small

Use this tie-breaker:

- if the feature can ship in the correct layer without widening scope, do that
- if the current host is not viable, allow the smallest feature-local boundary correction required to make the change correct
- if the "fix" would rename APIs, reorganize directories, or clean up neighboring modules that are not needed for the requested behavior, stop and keep that work out of scope

### Example

```text
User request: "Add bulk retry to the failed jobs page"

Current structure:
- FailedJobsPage.vue already mixes list fetch, retry mutation, filter state, and modal coordination

Correct resolution:
- Extract only the retry workflow into a feature-local composable
- Rewire the page to use that composable
- Do not also rename existing composables or reorganize the feature directory
```

The extraction is in scope because the current host cannot accept more behavior cleanly. The broader cleanup is out of scope because it is not required to ship the requested behavior in the correct layer.

## Reference

- [YAGNI Principle](https://martinfowler.com/bliki/Yagni.html)
- [boundary-first-minimality](boundary-first-minimality.md)
