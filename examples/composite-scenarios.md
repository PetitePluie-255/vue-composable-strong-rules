# Composite Scenarios

Use these examples when multiple rules are active at once and the correct path is not obvious.

Each scenario shows:

- the user request
- the current structure
- the triggered rules
- the wrong path
- the correct decision
- why that decision wins
- what stays out of scope

## Scenario 1: Overloaded Host + Scope Guard

### User Request

Add bulk retry to the failed jobs page.

### Current Structure

- `FailedJobsPage.vue` already owns list loading, filter state, modal state, single-item retry, and toast coordination
- the new feature needs async mutation workflow plus disabled/loading UI state

### Triggered Rules

- [current-host-viability](../rules/current-host-viability.md)
- [scope-guard-refactors](../rules/scope-guard-refactors.md)
- [minimum-compliant-architecture-priority](../rules/minimum-compliant-architecture-priority.md)
- [boundary-first-minimality](../rules/boundary-first-minimality.md)

### Wrong Path

- keep bulk retry request logic in `FailedJobsPage.vue` because it is "just one more action"
- or use the feature as an excuse to rename composables and reorganize the entire feature

### Correct Decision

- keep screen-only click wiring and modal glue in the page
- extract the bulk retry workflow state, mutation lifecycle, and request coordination into a feature-local composable
- rewire the page to use that composable

### Why This Wins

- the current host is already overloaded, so one more workflow would preserve a drifting boundary
- the extraction is still feature-local and directly required to place the new behavior correctly
- the larger cleanup is not required to ship the feature and should remain out of scope

### What Stays Out Of Scope

- renaming neighboring composables
- reorganizing directories
- cleaning up unrelated list-page concerns

## Scenario 2: Host Not Viable + Hollow Wrapper Ban

### User Request

Add tabbed detail loading and export actions to an existing user detail page.

### Current Structure

- `UserDetailPage.vue` is already heavy and mixes page state, async detail loading, export actions, and tab coordination
- a first instinct is to create `useUserDetailPage()` that mostly gathers refs and re-exports nested composables

### Triggered Rules

- [current-host-viability](../rules/current-host-viability.md)
- [hollow-wrapper-ban](../rules/hollow-wrapper-ban.md)
- [orchestration-composable](../rules/orchestration-composable.md)
- [business-component-boundary](../rules/business-component-boundary.md)

### Wrong Path

- leave the new logic in the page because it is already there
- or create a `useUserDetailPage()` composable that just collects `useUserDetail()`, `useUserExport()`, and local refs without real orchestration

### Correct Decision

- extract a real orchestration composable that owns tab-specific loading, export coordination, combined loading/error state, and stale-response protection
- keep page-only wiring in the view

### Why This Wins

- the host is no longer viable for more workflow logic
- a hollow wrapper would only move the mess one file away
- a real orchestration composable creates a coherent workflow boundary instead of fake extraction

### What Stays Out Of Scope

- turning every page concern into a composable
- introducing a global store for page-local tab/export workflow

## Scenario 3: Ask First, Then Conflict Protocol

### User Request

Put this search state in the global store so the page can remember it.

### Current Structure

- the request is ambiguous: "remember it" could mean only within the current page visit, across route changes, or across app reloads
- the current feature only uses the search state in one page and its child components

### Triggered Rules

- [implementation-judgment-first](../rules/implementation-judgment-first.md)
- [decision-thresholds](../rules/decision-thresholds.md)
- [architecture-conflict-protocol](../rules/architecture-conflict-protocol.md)
- [store-justification](../rules/store-justification.md)

### Wrong Path

- immediately move the state into a global store because the user said "store"
- or silently keep it local without explaining the conflict

### Correct Decision

1. Ask what "remember it" means:
   - current page subtree only
   - across route changes
   - across reloads
2. If the answer still implies only page-local usage, state the architecture conflict explicitly
3. Propose the compliant path:
   - keep the search state in a local composable
   - use `provide/inject` only if subtree sharing is needed
   - use a global store only if true cross-route or persistent state is required

### Why This Wins

- the first issue is product ambiguity, so `Ask` comes before implementation
- after ambiguity is resolved, the architecture conflict can be addressed directly instead of guessed around
- the final placement follows actual scope rather than the first storage word in the request

### What Stays Out Of Scope

- adding a store just because it feels "more reusable"
- broad persistence work unless the user explicitly needs reload persistence
