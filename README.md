# Vue Composable Strong Rules

[中文说明](./README.zh-CN.md)

`vue-composable-strong-rules` is a Vue architecture skill focused on **logic placement**, **boundary discipline**, and **feature-local splitting**.

It is designed for Vue implementation work where the main question is not only "can this be built?" but also "where should this logic live?".

## Purpose

This skill exists to keep Vue codebases on a strong-boundary path:

- views keep screen-specific glue
- reusable or growing workflow logic moves into feature-local composables
- overloaded hosts are treated as invalid boundaries for new behavior
- the smallest compliant architecture outranks the smallest diff

The skill intentionally treats Vue SFC work with a stricter engineering bar, closer to React-style concern splitting and Java-style responsibility discipline than script-style accumulation.

## Default Trigger

Use this skill by default for:

- Vue implementation work
- Vue refactors
- Vue bugfixes that touch behavior, state, async logic, or placement
- Vue feature changes on old pages, even when the change is small

Skip it only for purely presentational changes such as:

- static markup updates
- copy-only edits
- icon swaps
- styling-only changes with no meaningful state or workflow decision

## What This Skill Decides

This skill helps decide whether logic should stay in:

- a view
- a feature-local composable
- a feature-local helper
- `provide/inject`
- a store

It also decides when the current host is already too heavy and must be split before more behavior is added.

## Core Position

The central architectural stance is:

- Vue SFC syntax does not lower the architecture bar
- existing local precedent is weak evidence
- "the code already lives here" is not a valid reason to preserve a drifting boundary
- small feature changes to old pages are exactly where boundary drift must be corrected

## Rule Groups

The skill is organized around these groups:

- State & Scope
  Covers view vs composable vs `provide/inject` vs store placement.
- Composable Design
  Covers composable size, orchestration, splitting, and reusable business-unit extraction.
- Lifecycle & Safety
  Covers cleanup of watchers, timers, listeners, and long-lived subscriptions.
- Scope Discipline
  Covers strong-boundary defaults, lightweight planning, host viability, minimal compliant architecture, and conflict handling.

## Key Rules

The most important rules in this skill are:

- [strong-boundary-default](./rules/strong-boundary-default.md)
- [architecture-planning-gate](./rules/architecture-planning-gate.md)
- [current-host-viability](./rules/current-host-viability.md)
- [minimum-compliant-architecture-priority](./rules/minimum-compliant-architecture-priority.md)
- [boundary-first-minimality](./rules/boundary-first-minimality.md)
- [composable-weight-boundary](./rules/composable-weight-boundary.md)

## Decision Flow

For implementation work, the expected flow is:

1. Read local project rules and repository conventions.
2. Apply the strong-boundary default.
3. Do a lightweight feature-local architecture plan.
4. Check whether the current host is still viable.
5. Compare the smallest diff against the smallest compliant architecture.
6. If the host is overloaded or the diff preserves the wrong layer, do the smallest feature-local correction first.

## Planning Policy

This skill includes lightweight planning by default.

It does **not** require writing a plan file to `docs` for ordinary Vue work.

Write a formal plan only when:

- the user explicitly asks for a plan
- the task is large enough to justify `superpowers:writing-plans`
- multiple people or agents need a durable execution artifact

## Maintenance Guidance

If this skill fails in real use, file issues against one of these failure modes:

- false trigger
  The skill was applied to a purely presentational task.
- missed trigger
  The task changed behavior or placement, but the skill did not engage strongly enough.
- over-splitting
  The skill pushed extraction when screen-specific glue should have stayed local.
- under-splitting
  The skill allowed one more concern into an already overloaded host.
- host-viability miss
  The skill judged the current component or composable as acceptable when it was already too heavy.

## Files

- [SKILL.md](./SKILL.md)
  Main skill entrypoint, trigger guidance, procedure, and output contract.
- [rules/](./rules)
  Focused rule documents used by the main skill.
- [agents/openai.yaml](./agents/openai.yaml)
  Agent metadata/config for the skill environment.

## Usage Note

This README is a maintainer guide. The normative behavior lives in:

- [SKILL.md](./SKILL.md)
- the files under [rules/](./rules)
