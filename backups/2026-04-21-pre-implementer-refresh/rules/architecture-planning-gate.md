---
title: Architecture Planning Gate
impact: HIGH
impactDescription: forces a lightweight feature-local plan before editing without turning every Vue change into a docs task
type: capability
tags: planning, architecture, boundary, placement, feature-local
---

# Architecture Planning Gate

**Impact: HIGH** — forces a lightweight feature-local plan before editing without turning every Vue change into a docs task

Before editing Vue implementation code, do a short architecture plan. The goal is not a formal spec. The goal is to prevent edits from starting before the placement and split decisions are clear.

## Required Planning Questions

Answer these before editing:

1. What behavior is changing?
2. What new or changed state, async workflow, event handling, or derived logic is involved?
3. Which concerns stay in the view as screen glue?
4. Which concerns belong in a composable, feature-local helper, `provide/inject`, or store?
5. Does the current structure already violate those boundaries?
6. If yes, what is the smallest feature-local correction needed before implementing?

Keep this planning lightweight. In many tasks it can be a few sentences or a short internal checklist.

## Do Not Over-Plan

This rule does **not** require writing a plan file for every Vue task.

Do **not** default to `docs` output when:

- the task is a normal Vue feature change, bugfix, or refactor
- the plan is short enough to hold inline while implementing
- no separate execution handoff is needed

Write a plan document only when:

- the user explicitly asks for a plan
- the task is large or multi-step enough that `superpowers:writing-plans` is the right workflow
- multiple agents or handoffs need a durable plan artifact

## Review Guidance

- Flag edits that begin coding before the placement decision is clear.
- Do not demand heavyweight planning artifacts for routine feature-local work.
- Prefer "show the boundary plan first" over "write a full spec."

## Reference

- [minimum-compliant-architecture-priority](minimum-compliant-architecture-priority.md)
- [boundary-first-minimality](boundary-first-minimality.md)
- [repository-convention-first](repository-convention-first.md)
