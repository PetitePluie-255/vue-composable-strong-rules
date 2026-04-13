---
name: vue-composable-strong-rules
description: Use when implementing, refactoring, or changing Vue feature behavior and deciding placement across views, composables, feature-local helpers, provide/inject, or stores; skip only for purely presentational changes.
---

# Vue Composable Strong Rules

Read the target repository's local rules first, such as `AGENTS.md`, contributor docs, architecture notes, or established directory conventions already used in the codebase.

If explicit docs are absent, infer the local conventions from the existing project structure and nearby feature modules. Prefer consistency with the current repository pattern over introducing a new structure unless the user asks for it or the current structure is clearly causing the issue.

## Architectural Stance

Treat Vue SFC architecture with **strong boundaries by default**.

Vue single-file components are a syntax convenience, not permission to accumulate business logic in the page. In architectural discipline, prefer a style closer to **React hook-style concern splitting** and **Java-style layered responsibility** than to script-heavy module accumulation.

Use this as a boundary heuristic, not a literal framework transplant:

- do **not** copy React APIs or Java folder names mechanically
- do apply the same level of concern separation, host health checks, and feature-local responsibility boundaries
- do treat "the code already lives in this `.vue` file" as weak evidence

## Top Priority Rule

For implementation work in this skill, always choose the **smallest compliant architecture** over the **smallest diff**.

Priority order:

1. explicit user instructions
2. repository-local rules such as `AGENTS.md`
3. system or tooling constraints
4. smallest compliant architecture
5. minimizing edits, preserving current file boundaries, or matching nearby anti-patterns

Do not use "this is only a bugfix", "keep it consistent with nearby code", or "avoid touching more files" as reasons to keep logic in the wrong layer.

## Use This Skill When

- any Vue implementation, refactor, or feature behavior change is being made
- you are adding or changing functionality in an existing Vue page, even if the change is small
- the user asks to implement or refactor Vue code with composable-first constraints
- you need to decide whether logic belongs in a view, composable, `provide/inject`, or store
- a task risks turning into a broad architecture cleanup and you need to hold scope without defaulting to patch-style fixes in the wrong layer

Default trigger rule:

- for Vue implementation, refactor, bugfix, or feature-change work, use this skill by default
- this includes small functionality changes to old pages because those are exactly where boundary drift accumulates
- skip it only for purely presentational changes such as static markup, copy updates, icon swaps, or styling with no meaningful state, workflow, or placement decision

This skill focuses on **code placement and layering decisions**. Pair with `$vue-best-practices` for framework API usage, reactivity patterns, and component design. When rules overlap, explicit local docs and established repository conventions > this skill > `$vue-best-practices`.

## Local Convention Priority

Before recommending file placement or directory changes:

1. Check explicit local docs such as `AGENTS.md`, contributor docs, or architecture notes.
2. If none exist, inspect the current directory structure and nearby feature modules for established patterns.
3. Prefer consistency with the existing repository convention over introducing a new structure.
4. Only propose a new directory pattern when the current pattern is inconsistent or directly contributes to the problem.

## Do Not Use This Skill As The Only Skill When

- the user explicitly wants a different architecture style and local project rules support it
- the task is pure review with no implementation — use `$vue-composable-code-review` instead
- the main challenge is framework setup, testing, or library API usage rather than code placement

---

## Architecture Rules

Rules that govern where to place logic and state in a composable-first Vue project.

### State & Scope

| Rule | Impact | Description |
|------|--------|-------------|
| [utils-no-reactivity](rules/utils-no-reactivity.md) | HIGH | Keep `utils/` free of Vue reactivity, lifecycle, and component APIs |
| [state-placement-by-scope](rules/state-placement-by-scope.md) | HIGH | Place state at the narrowest scope: view → composable → provide/inject → store |
| [store-justification](rules/store-justification.md) | MEDIUM | Global store requires explicit admission criteria (cross-route, distant consumers, persistence) |
| [provide-inject-scope](rules/provide-inject-scope.md) | HIGH | provide/inject is for subtree sharing only — not cross-route or app-wide state |

### Composable Design

| Rule | Impact | Description |
|------|--------|-------------|
| [composable-weight-boundary](rules/composable-weight-boundary.md) | HIGH | Allow orchestration, but split independently changing business capabilities into smaller cohesive composables |
| [business-component-boundary](rules/business-component-boundary.md) | HIGH | Keep business components focused on rendering and screen-specific glue instead of reusable workflow logic |
| [hollow-wrapper-ban](rules/hollow-wrapper-ban.md) | HIGH | Ban page-level composables that only rename/re-export without orchestration |
| [orchestration-composable](rules/orchestration-composable.md) | MEDIUM | Allow page-level composables when they coordinate multiple data sources or workflows |
| [shared-business-unit-extraction](rules/shared-business-unit-extraction.md) | HIGH | Extract repeated feature business logic into shared feature-local units before it drifts across components |
| [single-purpose-composables](rules/single-purpose-composables.md) | MEDIUM | Prefer cohesive single-purpose composables; avoid both monoliths and over-fragmentation |

### Lifecycle & Safety

| Rule | Impact | Description |
|------|--------|-------------|
| [async-lifecycle-cleanup](rules/async-lifecycle-cleanup.md) | HIGH | Clean up watchers, timers, listeners, and in-flight requests via `onScopeDispose` |
| [no-leaked-subscriptions](rules/no-leaked-subscriptions.md) | HIGH | Every `start()`/`subscribe()` must have a matching `stop()`/`unsubscribe()` + auto-cleanup |

### Scope Discipline

| Rule | Impact | Description |
|------|--------|-------------|
| [strong-boundary-default](rules/strong-boundary-default.md) | HIGH | Treat Vue SFC work with React-like concern splitting and Java-like responsibility discipline, rather than script-style accumulation |
| [architecture-planning-gate](rules/architecture-planning-gate.md) | HIGH | Require a lightweight feature-local architecture plan before editing, but do not default to writing docs for routine Vue changes |
| [current-host-viability](rules/current-host-viability.md) | HIGH | Treat an already overloaded component or composable as a non-compliant host for new behavior, even when the new diff is small |
| [minimum-compliant-architecture-priority](rules/minimum-compliant-architecture-priority.md) | HIGH | Make the smallest compliant architecture outrank the smallest diff, existing file boundaries, and local convenience heuristics |
| [repository-convention-first](rules/repository-convention-first.md) | HIGH | Prefer explicit local docs, then established repository conventions, before proposing new file placement patterns |
| [boundary-first-minimality](rules/boundary-first-minimality.md) | HIGH | Prefer the smallest compliant architecture over the smallest diff; make the smallest feature-local boundary correction first when needed |
| [scope-guard-refactors](rules/scope-guard-refactors.md) | MEDIUM | Reject unrequested broad refactors, API changes, or directory reorganizations, but allow localized boundary corrections required for compliance |
| [view-glue-growth-guard](rules/view-glue-growth-guard.md) | MEDIUM | Warn when a page is drifting from screen-specific glue toward becoming a workflow container |
| [architecture-conflict-protocol](rules/architecture-conflict-protocol.md) | HIGH | When request conflicts with rules, state conflict explicitly and propose compliant alternative before editing |

---

## Procedure

1. Extract the local project rules or established repository conventions relevant to the current task.
2. Apply [strong-boundary-default](rules/strong-boundary-default.md) so Vue SFC syntax does not weaken the architectural bar.
3. Apply those rules before planning code structure or making edits.
4. Run the lightweight architecture planning gate from [architecture-planning-gate](rules/architecture-planning-gate.md).
5. Evaluate whether the current component or composable is still a valid host by using [current-host-viability](rules/current-host-viability.md).
6. Run the pre-edit decision:
   1. What is the smallest diff?
   2. Is that option boundary-compliant?
   3. If not, what is the smallest compliant architecture?
   4. Implement that instead.
7. Choose the smallest compliant architecture that satisfies the request. See [minimum-compliant-architecture-priority](rules/minimum-compliant-architecture-priority.md) and [boundary-first-minimality](rules/boundary-first-minimality.md).
8. If the current structure cannot host the change without violating placement rules, perform the smallest feature-local refactor necessary before adding new behavior.
9. When a user request conflicts with the architecture rules, state the conflict explicitly and propose the compliant implementation before editing.
10. Keep layering decisions stable throughout the task.

## Placement Matrix (Quick Reference)

- **Keep in the view**: template wiring, event handlers tightly bound to one screen, page-only modal/message/navigation glue
- **Extract to a local composable**: reusable reactive state, async workflows, validation, derived UI state, or side effects needed by more than one component or too heavy for the view
- **Use `provide/inject`**: subtree-scoped coordination such as form sections, wizard steps, or feature-local services shared across nested components
- **Use global store**: app-wide session state, cross-route persistence, shared cache, or coordination across distant consumers

## Implementation Pattern

1. Restate the smallest compliant solution, not the smallest diff.
2. Briefly plan the feature-local boundary before editing: what behavior changes, what state or async workflow is involved, and where each concern belongs.
3. Treat the current `.vue` file or composable as a normal host, not a privileged host; SFC syntax does not lower the bar for keeping logic there.
4. Check whether the current host is still healthy enough to receive more logic; if not, treat "leave it where it is" as non-compliant.
5. Choose placement with the matrix above.
6. Check whether a smaller screen-specific solution is enough before creating a reusable abstraction.
7. If the smaller diff only works by keeping reusable workflow logic in the wrong layer, or by adding more logic to an already overloaded host, reject it and follow [strong-boundary-default](rules/strong-boundary-default.md), [current-host-viability](rules/current-host-viability.md), and [minimum-compliant-architecture-priority](rules/minimum-compliant-architecture-priority.md).
8. If architecture and request conflict, say so before editing and propose the smallest compliant path.
9. After editing, call out the rule that drove the placement decision and any validation gap.

## Planning Output

- default to lightweight inline or internal planning for routine Vue implementation work
- do **not** write a plan file to `docs` by default for normal feature changes, bugfixes, or refactors
- write a plan document only when the user explicitly asks for one, or when the task is large enough that `superpowers:writing-plans` is the correct companion skill

## Mandatory Split Triggers

Perform the smallest feature-local split first when any of these are true:

- one composable contains both data-loading workflow and editing or mutation workflow
- one composable contains both tree/query state and lifecycle or mutation state
- a bug fix would add new state or workflow to an already multi-responsibility composable
- two or more independently changing business capabilities live in the same composable
- the current component or composable is already overloaded, so adding one more concern would preserve a drifting boundary
- keeping the current boundary is justified only by "smaller diff" or "the code is already here"

## Review Output Guidance

When this skill is used during review, keep findings limited to placement, layering, and composable-boundary issues.

### Finding Attribution

Classify each finding as one of:

- **skill-native rule**: the issue violates a rule defined in this skill
- **repo-local rule**: the issue violates `AGENTS.md`, contributor docs, architecture docs, or established repository conventions
- **risk note**: the current code is not clearly wrong yet, but it is trending toward a layering or boundary problem

Do not attribute a finding to this skill alone if the constraint comes only from local project rules or repository conventions.

### Severity Guidance

- **P1**: clear architectural boundary violation that materially increases coupling, mixes reusable workflow logic into the wrong layer, or blocks safe feature growth
- **P2**: repeated business logic, under-split composables, weak boundary definition, or repository-convention drift with real maintenance cost
- **P3**: growth risk, boundary warning, style drift, or early signal that does not yet create a hard architectural break

### Review Fallback

If this skill is used in review instead of a review-specific skill:

- focus on placement, layering, and business-boundary findings only
- separate skill-native findings from repo-local findings
- keep risk notes distinct from hard violations
- avoid claiming directory, naming, or API-shape violations unless they come from local project docs or established repository conventions

## Conflict Template

```text
Architecture conflict: the request pushes reusable reactive workflow logic into the view, but the project rules prefer that logic in a composable. I will keep screen-only glue in the component and extract only the reusable workflow state into a local composable.
```

## Output

- If there is an architecture conflict, say so directly before editing.
- After implementation, mention the key architecture rules that affected the solution and any important tradeoff or validation gap.
- Explicitly state when `minimum-compliant-architecture-priority` overrode a smaller diff.
- During review, identify whether each finding is skill-native, repo-local, or a risk note.
