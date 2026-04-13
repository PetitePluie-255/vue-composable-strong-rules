---
title: Shared Business Unit Extraction
impact: HIGH
impactDescription: reduces repeated business logic scattered across components by extracting feature-local reusable units
type: capability
tags: reuse, duplication, composable, feature, business-logic, architecture
---

# Shared Business Unit Extraction

**Impact: HIGH** — reduces repeated business logic scattered across components by extracting feature-local reusable units

When multiple components maintain the same kind of domain data, options, mapping rules, or synchronization behavior, that repeated business logic should be extracted into a shared feature-local unit. The goal is not abstraction for its own sake; the goal is to stop the same business rule from drifting across multiple components.

## Symptoms

- Several business components define the same field mock data or option lists separately
- Similar mapping or synchronization logic is copied with minor changes across sibling components
- Updating one business rule requires touching multiple components in the same feature
- Components know too much about the same domain structure because no shared unit exists

## Decision Rule

Extract a shared composable, constant module, or feature-local service when:

- the same domain options or schema shape appear in multiple components
- the same mapping or sync rule is implemented in more than one place
- duplicated logic is likely to drift or already has diverging behavior
- the repeated logic belongs to the same feature domain

Keep logic local when:

- the logic is genuinely one-off
- the duplication is superficial and the underlying business behavior is different
- extraction would create a misleading abstraction with no stable domain meaning

## Problem Pattern

```ts
// SqlConfigPanel.vue
const fieldTypeOptions = ['string', 'number', 'boolean']
function syncFieldsToParams() {}

// ParamConfigPanel.vue
const fieldTypeOptions = ['string', 'number', 'boolean']
function syncFieldsToParams() {}
```

## Fix

```ts
// feature-local shared unit
export const fieldTypeOptions = ['string', 'number', 'boolean']

export function useFieldParamSync() {
  function syncFieldsToParams() {}
  return { syncFieldsToParams }
}
```

## Review Guidance

- Do not require extraction for every tiny duplicate string or local helper.
- Do require extraction when repeated business logic or domain configuration is causing maintenance drift.
- Prefer feature-local shared units before reaching for app-wide globals.

## Reference

- [DRY Is About Knowledge](https://martinfowler.com/bliki/Don’tRepeatYourself.html)
