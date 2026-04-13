---
title: View Glue Growth Guard
impact: MEDIUM
impactDescription: prevents screens from quietly turning into workflow containers while still allowing page-specific glue
type: capability
tags: view, glue, page, boundary, growth, architecture
---

# View Glue Growth Guard

**Impact: MEDIUM** — prevents screens from quietly turning into workflow containers while still allowing page-specific glue

Views may keep screen-specific glue, but they should not keep accumulating unrelated workflow coordination until they become the feature's real business container. This rule is primarily a growth-risk check, not an automatic hard violation.

## Symptoms

- One page owns list refresh orchestration, context switching, modal flow, delete flow, and create flow all together
- New feature logic keeps being added to the page because it is "already there"
- The page imports many unrelated helpers and composables just to coordinate them manually
- Testing or changing one screen behavior requires understanding most of the feature's business flow

## Decision Rule

Treat this as a risk signal when:

- the page still mostly acts as screen glue, but is trending toward workflow ownership
- new changes are likely to push more reusable logic into the page
- a small local extraction would preserve the current boundary

Treat this as a stronger finding when:

- the page already owns reusable workflow state or domain coordination that should live in a composable
- multiple child areas depend on page-held business logic
- the page has become the de facto feature service layer

## Review Guidance

- Use this rule for boundary warnings and growth-risk notes.
- Do not label every busy page as architecturally wrong.
- When possible, recommend the smallest extraction that keeps the page in a render-and-connect role.

## Reference

- [Vue Components Basics](https://vuejs.org/guide/essentials/component-basics.html)
