# Vue Composable Strong Rules

[中文说明](./README.zh-CN.md)

`vue-composable-strong-rules` is a Vue implementation skill focused on **judgment-first execution**, **boundary discipline**, and **quality coverage**.

It is designed for Vue work where the questions are both:

- "where should this logic live?"
- "what should be decided and stated before coding starts?"

## Purpose

This skill exists to keep Vue codebases on a strong-boundary path while also enforcing a higher implementation bar:

- implementation judgment comes before editing
- ambiguity is surfaced before code, not after
- high-impact changes require confirmation
- behavior-changing work must cover normal flow, failure handling, and boundary conditions
- views keep screen-specific glue
- reusable or growing workflow logic moves into feature-local composables
- overloaded hosts are treated as invalid boundaries for new behavior
- boundary-correct implementation outranks the smallest diff

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

This skill decides:

- what behavior is actually being changed
- whether implementation can proceed now, needs a question, or needs confirmation
- a view
- a feature-local composable
- a feature-local helper
- `provide/inject`
- a store

These options answer where the logic should stay.

It also decides:

- when the current host is already too heavy and must be split first
- which failure paths and edge conditions are relevant to the current task
- what the smallest boundary-correct Vue implementation is

## Structure

The skill is organized around four layers:

- `Identity`
  Strong-rule, high-quality, judgment-first implementer.
- `Decision Protocol`
  State understanding, implementation path, and risks before editing. Ask or confirm when thresholds are crossed.
- `Implementation Quality Contract`
  Cover normal flow, failure handling, and boundary conditions for behavior-changing work.
- `Domain Rules`
  Existing Vue composable, state-placement, and boundary rules.

## Rule Highlights

The most important rules in this skill are now:

- [implementation-judgment-first](./rules/implementation-judgment-first.md)
- [decision-thresholds](./rules/decision-thresholds.md)
- [implementation-quality-contract](./rules/implementation-quality-contract.md)
- [architecture-planning-gate](./rules/architecture-planning-gate.md)
- [minimum-compliant-architecture-priority](./rules/minimum-compliant-architecture-priority.md)
- [current-host-viability](./rules/current-host-viability.md)

## Relationship To Other Skills

This skill intentionally does **not** duplicate the full workflows of:

- `superpowers:brainstorming`
- `superpowers:test-driven-development`
- `superpowers:systematic-debugging`
- `superpowers:verification-before-completion`

Instead, it keeps the Vue-local judgment:

- whether to ask
- whether to confirm
- what quality coverage is relevant
- where the logic should live
- what the smallest boundary-correct Vue implementation is

## Default Flow

For implementation work, the expected flow is:

1. Read local project rules and repository conventions.
2. Produce the implementation judgment before editing.
3. Decide whether to proceed, ask, or confirm.
4. Apply the strong-boundary default and lightweight architecture plan.
5. Check whether the current host is still viable.
6. Compare the smallest diff against the smallest boundary-correct path.
7. If the host is overloaded or the diff preserves the wrong layer, do the smallest feature-local correction first.
8. Implement with explicit quality coverage.

## Planning Policy

This skill includes lightweight planning by default.

It does **not** require writing a plan file to `docs` for ordinary Vue work.

Write a formal plan only when:

- the user explicitly asks for a plan
- the task is large enough to justify `superpowers:writing-plans`
- multiple people or agents need a durable execution artifact

## Maintenance Guidance

If this skill fails in real use, file issues against one of these failure modes:

- missing judgment
  The skill started coding before behavior, path, and risks were explicit.
- missed ask
  The skill should have asked before editing, but inferred a product choice.
- missed confirmation
  The skill crossed a high-impact threshold without confirming first.
- quality gap
  The skill implemented only the happy path and skipped relevant failure handling or edge cases.
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
  Main skill entrypoint, trigger guidance, implementation judgment flow, and output contract.
- [rules/](./rules)
  Focused rule documents used by the main skill.
- [agents/openai.yaml](./agents/openai.yaml)
  Agent metadata/config for the skill environment.

## Usage Note

This README is a maintainer guide. The normative behavior lives in:

- [SKILL.md](./SKILL.md)
- the files under [rules/](./rules)
