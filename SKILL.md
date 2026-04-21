---
name: vue-composable-strong-rules
description: Use when implementing, refactoring, or changing Vue feature behavior where placement, boundary discipline, or behavior-risk tradeoffs matter; skip only for purely presentational changes.
---

# Vue Composable Strong Rules

Read the target repository's local rules first, such as `AGENTS.md`, contributor docs, architecture notes, or established directory conventions already used in the codebase.

If explicit docs are absent, infer the local conventions from the existing project structure and nearby feature modules. Prefer consistency with the current repository pattern over introducing a new structure unless the user asks for it or the current structure is clearly causing the issue.

## Skip Checklist

Do **not** run the full judgment-first implementation flow when all of these are true:

- the change is purely presentational: static markup, copy, icon, or styling only
- no meaningful behavior, state, async logic, side effect, or placement decision is changing
- no repository rule, compatibility risk, or architecture conflict is being crossed

If any item is false, use the normal skill flow.

## Identity

Treat this skill as a **strong-rule, high-quality Vue implementer**.

This skill is not only a Vue placement guide. It is a judgment-first implementation skill for Vue work:

- decide before editing, not while drifting into code
- make the intended behavior, implementation path, and key risks explicit first
- ask or confirm when the situation requires it instead of guessing
- implement with explicit quality coverage, not happy-path-only code
- keep Vue architecture boundaries strong while still adapting to the current repository and task

## Default Stance

Treat Vue single-file component architecture with **strong boundaries by default**.

Vue SFCs are a syntax convenience, not permission to accumulate business logic in the page. In architectural discipline, prefer a style closer to **React hook-style concern splitting** and **Java-style layered responsibility** than to script-heavy module accumulation.

Use this as a boundary heuristic, not a literal framework transplant:

- do **not** copy React APIs or Java folder names mechanically
- do apply the same level of concern separation, host health checks, and feature-local responsibility boundaries
- do treat "the code already lives in this `.vue` file" as weak evidence

## Priority Order

For implementation work, use this order:

1. explicit user instructions
2. repository-local rules such as `AGENTS.md`
3. system or tooling constraints
4. decision protocol and explicit confirmations
5. implementation quality contract
6. internal boundary-correct placement
7. minimizing edits, preserving current file boundaries, or matching nearby anti-patterns

If items 4-7 conflict, the earlier item wins.

## Use This Skill When

- any Vue implementation, refactor, or feature behavior change is being made
- you are adding or changing functionality in an existing Vue page, even if the change is small
- you need to decide whether logic belongs in a view, composable, `provide/inject`, or store
- the task requires a strong-rule implementer that should think before coding and avoid silent architectural drift
- a task risks turning into a broad architecture cleanup and you need to hold scope without defaulting to patch-style fixes in the wrong layer

Default trigger rule:

- for Vue implementation, refactor, bugfix, or feature-change work, use this skill by default
- this includes small functionality changes to old pages because those are exactly where boundary drift accumulates
- skip it only for purely presentational changes such as static markup, copy updates, icon swaps, or styling with no meaningful state, workflow, or placement decision

This skill focuses on **judgment-first Vue implementation**. Pair with `$vue-best-practices` for framework API usage, reactivity patterns, and component design. When rules overlap, explicit local docs and established repository conventions > this skill > `$vue-best-practices`.

## Do Not Use This Skill As The Only Skill When

- the task is large enough to require a full design/spec workflow — pair with `superpowers:brainstorming`
- the main challenge is framework setup, testing mechanics, or library API usage rather than code placement and implementation judgment
- the task is debugging-first and the root cause is still unknown — pair with `superpowers:systematic-debugging`
- the task is implementation-heavy and the repository expects test-first execution — pair with `superpowers:test-driven-development`
- you are about to claim work is complete — pair with `superpowers:verification-before-completion`

Do not copy those skills' full workflows into this one. This skill keeps the **local judgment** for Vue implementation:

- whether to ask before editing
- whether to confirm before editing
- what risks matter in this task
- what normal flow, failure handling, and boundary conditions are relevant
- what the smallest boundary-correct Vue implementation is

If multiple rules seem active at once and the tradeoff is unclear, read [examples/composite-scenarios.md](examples/composite-scenarios.md) for end-to-end conflict examples before editing.

---

## Rule Groups

### Identity & Decision

| Rule | Impact | Description |
|------|--------|-------------|
| [implementation-judgment-first](rules/implementation-judgment-first.md) | HIGH | Require an explicit implementation judgment before editing |
| [decision-thresholds](rules/decision-thresholds.md) | HIGH | Decide when to ask, when to confirm, and when to proceed |
| [architecture-planning-gate](rules/architecture-planning-gate.md) | HIGH | Require a lightweight feature-local architecture plan before editing |
| [architecture-conflict-protocol](rules/architecture-conflict-protocol.md) | HIGH | State request-vs-rule conflicts explicitly and propose the compliant path before editing |
| [repository-convention-first](rules/repository-convention-first.md) | HIGH | Follow explicit local docs first, then established repository conventions |

### Quality & Safety

| Rule | Impact | Description |
|------|--------|-------------|
| [implementation-quality-contract](rules/implementation-quality-contract.md) | HIGH | Cover normal flow, failure handling, and boundary conditions for behavior-changing work |
| [async-lifecycle-cleanup](rules/async-lifecycle-cleanup.md) | HIGH | Clean up watchers, timers, listeners, and in-flight requests via `onScopeDispose` |
| [no-leaked-subscriptions](rules/no-leaked-subscriptions.md) | HIGH | Every `start()`/`subscribe()` must have a matching cleanup path |

### State & Scope

| Rule | Impact | Description |
|------|--------|-------------|
| [utils-no-reactivity](rules/utils-no-reactivity.md) | HIGH | Keep `utils/` free of Vue reactivity, lifecycle, and component APIs |
| [state-placement-by-scope](rules/state-placement-by-scope.md) | HIGH | Place state at the narrowest scope: view → composable → provide/inject → store |
| [store-justification](rules/store-justification.md) | MEDIUM | Global store requires explicit admission criteria |
| [provide-inject-scope](rules/provide-inject-scope.md) | HIGH | `provide/inject` is for subtree sharing only |

### Composable Design

| Rule | Impact | Description |
|------|--------|-------------|
| [composable-weight-boundary](rules/composable-weight-boundary.md) | HIGH | Split independently changing business capabilities into smaller cohesive composables |
| [business-component-boundary](rules/business-component-boundary.md) | HIGH | Keep business components focused on rendering and screen-specific glue |
| [hollow-wrapper-ban](rules/hollow-wrapper-ban.md) | HIGH | Ban page-level composables that only rename or re-export without orchestration |
| [orchestration-composable](rules/orchestration-composable.md) | MEDIUM | Allow page-level composables when they coordinate real workflows |
| [shared-business-unit-extraction](rules/shared-business-unit-extraction.md) | HIGH | Extract repeated feature logic before it drifts across components |
| [single-purpose-composables](rules/single-purpose-composables.md) | MEDIUM | Prefer cohesive single-purpose composables without over-fragmentation |

### Scope Discipline

| Rule | Impact | Description |
|------|--------|-------------|
| [strong-boundary-default](rules/strong-boundary-default.md) | HIGH | Treat Vue SFC work with strong concern splitting by default |
| [current-host-viability](rules/current-host-viability.md) | HIGH | Treat an already overloaded host as non-compliant for new behavior |
| [minimum-compliant-architecture-priority](rules/minimum-compliant-architecture-priority.md) | HIGH | Use the smallest boundary-correct implementation instead of the smallest diff |
| [boundary-first-minimality](rules/boundary-first-minimality.md) | HIGH | Correct the boundary first when the current layer cannot host the change cleanly |
| [scope-guard-refactors](rules/scope-guard-refactors.md) | MEDIUM | Reject broad unrequested refactors while allowing local boundary corrections |
| [view-glue-growth-guard](rules/view-glue-growth-guard.md) | MEDIUM | Warn when a page is drifting toward workflow-container behavior |

---

## Core Procedure

1. Extract the local project rules or established repository conventions relevant to the current task.
2. Produce the implementation judgment before editing. See [implementation-judgment-first](rules/implementation-judgment-first.md).
3. Decide whether the next step is:
   - proceed directly
   - ask the user a clarifying question
   - ask for confirmation before editing
4. Apply [strong-boundary-default](rules/strong-boundary-default.md) so Vue SFC syntax does not weaken the architecture bar.
5. Run the lightweight architecture planning gate from [architecture-planning-gate](rules/architecture-planning-gate.md).
6. Evaluate whether the current component or composable is still a valid host by using [current-host-viability](rules/current-host-viability.md).
7. Run the pre-edit decision:
   1. What is the smallest diff?
   2. Is that option boundary-compliant?
   3. If not, what is the smallest boundary-correct implementation path?
   4. Implement that instead.
8. If the current structure cannot host the change without violating placement rules, perform the smallest feature-local refactor necessary before adding new behavior.
9. Implement with the [implementation-quality-contract](rules/implementation-quality-contract.md) in force.
10. After editing, explain the result in terms of normal flow, failure handling, boundary conditions, and remaining validation gaps.

## Implementation Judgment

Before editing, always produce an implementation judgment first. Do not start coding until the intended behavior, implementation path, and major risks are explicit.

The judgment should usually cover:

- **request understanding** — what behavior is changing
- **recommended implementation path** — where the logic should live and why
- **key risks, ambiguities, or conflicts** — what could be misread, break compatibility, or violate rules

Keep this lightweight for routine tasks, but do not skip it.

## Ask vs Confirm

### Ask Before Editing When

- the request can reasonably lead to two or more different user-visible behaviors
- critical product intent is missing, so implementing now would likely be wrong
- the user request conflicts with repository rules or this skill's architecture rules

### Confirm Before Editing When

- deleting or modifying key configuration
- changing public API signatures or exported behavior that other modules may depend on
- changing shared types, store schema, persistence shape, or directory structure
- changing behavior in a way that may break compatibility or expectations outside the current screen

If the change is local, low-impact, and behaviorally clear, proceed after the implementation judgment without forcing a confirmation round-trip.

## Implementation Quality Contract

For any task involving behavior, state, async logic, side effects, or data flow, both the implementation and the final explanation must cover:

- **normal flow** — the expected path when things work
- **failure handling** — request failures, empty states, invalid inputs, cancellation, cleanup, or degraded behavior as relevant
- **boundary conditions** — initial state, repeated triggers, concurrency, stale responses, unmount/disposal, and edge inputs as relevant

If one category is not relevant, do not silently omit it. State that it is not applicable or not implemented yet, and call out any remaining risk if relevant.

This is a coverage contract, not an invitation to pad the answer. Be specific to the current task.

## Placement Matrix (Quick Reference)

- **Keep in the view**: template wiring, event handlers tightly bound to one screen, page-only modal/message/navigation glue
- **Extract to a local composable**: reusable reactive state, async workflows, validation, derived UI state, or side effects needed by more than one component or too heavy for the view
- **Use `provide/inject`**: subtree-scoped coordination such as form sections, wizard steps, or feature-local services shared across nested components
- **Use global store**: app-wide session state, cross-route persistence, shared cache, or coordination across distant consumers

## Mandatory Split Triggers

Perform the smallest feature-local split first when any of these are true:

- one composable contains both data-loading workflow and editing or mutation workflow
- one composable contains both tree/query state and lifecycle or mutation state
- a bug fix would add new state or workflow to an already multi-responsibility composable
- two or more independently changing business capabilities live in the same composable
- the current component or composable is already overloaded, so adding one more concern would preserve a drifting boundary
- keeping the current boundary is justified only by "smaller diff" or "the code is already here"

## Output Contract

- If there is an architecture conflict, say so directly before editing.
- If a clarification or confirmation is required, stop and get it before editing.
- After implementation, mention the key rules that affected the solution and any important tradeoff or validation gap.
- If boundary correction overrode a smaller diff, explain that in plain language instead of centering the rule name.
- Do not report only the happy path for behavior-changing work; cover normal flow, failure handling, and boundary conditions.

## Review Guidance

When this skill is used during review, keep findings limited to:

- placement and layering
- implementation judgment quality
- whether the change skipped an ask/confirm threshold
- whether normal flow, failure handling, or boundary conditions were ignored

Classify each finding as one of:

- **skill-native rule** — the issue violates a rule defined in this skill
- **repo-local rule** — the issue violates local docs or established repository conventions
- **risk note** — the current code is not clearly wrong yet, but it is trending toward a problem

## Conflict Template

```text
Architecture conflict: the request pushes reusable reactive workflow logic into the view, but the project rules prefer that logic in a composable.

Compliant path: keep screen-only glue in the component, extract the reusable workflow state into a feature-local composable, and implement the requested behavior there.

Need from user: confirm whether to proceed with the compliant path, or explicitly override the architecture rule.
```
