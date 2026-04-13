---
title: Business Component Boundary
impact: HIGH
impactDescription: keeps business components focused on UI composition and screen-specific wiring instead of owning reusable workflow logic
type: capability
tags: component, business-component, render, composable, workflow, boundary, architecture
---

# Business Component Boundary

**Impact: HIGH** — keeps business components focused on UI composition and screen-specific wiring instead of owning reusable workflow logic

Business components should mainly render state and connect actions. They may keep screen-specific glue such as modal open state, local message handling, and event wiring that is tightly bound to one screen. They should not become the long-term home for reusable workflow logic, request logic, or heavy business coordination.

## Symptoms

- A feature component fetches data directly and also transforms the result into reusable domain options
- A business component contains field mapping, synchronization, and validation flows that would be useful outside the current template block
- Business logic dominates the `<script setup>` while the template remains small
- Multiple components need the same workflow behavior, but each re-implements it locally because it never left the component

## Decision Rule

Keep logic in the component when it is:

- template wiring
- local event handlers
- screen-only modal, message, or navigation glue
- trivial presentational state

Extract logic to a composable when it is:

- async request or polling workflow
- reusable field mapping or synchronization logic
- derived state or validation used by more than one component
- logic that makes the component hard to read because rendering and workflow concerns are mixed together

## Problem Pattern

```ts
// FeaturePanel.vue
const sourceOptions = ref([])
const tableOptions = ref([])

onMounted(async () => {
  sourceOptions.value = await fetchDataSources()
})

watch(selectedSource, async (id) => {
  tableOptions.value = await fetchTables(id)
})
```

## Fix

```ts
export function useTableSourceOptions() {
  const sourceOptions = ref([])
  const tableOptions = ref([])

  async function loadSources() {}
  async function loadTables(id: string) {}

  return { sourceOptions, tableOptions, loadSources, loadTables }
}

// FeaturePanel.vue
const { sourceOptions, tableOptions, loadSources, loadTables } = useTableSourceOptions()
```

## Review Guidance

- Do not flag components for having ordinary event handlers or page-only UI glue.
- Do flag them when reusable request/workflow logic lives inside the component instead of a composable.
- Do not keep wrong-layer logic in a component just to minimize the diff; extract the smallest feature-local unit that restores the boundary.
- Prefer recommendations like "extract data source loading and table option sync into a local composable" over vague statements like "move logic out of the component."

## Reference

- [Vue Composables](https://vuejs.org/guide/reusability/composables.html)
- [boundary-first-minimality](boundary-first-minimality.md)
