---
title: Boundary-First Minimality
impact: HIGH
impactDescription: prevents patch-style fixes from preserving the wrong architectural boundary
type: capability
tags: minimality, boundary, refactor, scope, placement, architecture
---

# Boundary-First Minimality

**Impact: HIGH** — prevents patch-style fixes from preserving the wrong architectural boundary

In this skill, "minimal" means the smallest change that keeps logic in the correct layer. It does **not** mean the smallest diff regardless of layering.

## Why This Rule Exists

Without this rule, an agent can misread "hold scope" and "smallest boundary-correct implementation" as:

- keep logic where it already lives, even if that layer is wrong
- patch the current component or view first, then clean up later
- avoid a necessary local extraction because it touches more files today

That behavior creates short-term progress but weakens the boundary the skill is supposed to protect.

## Decision Rule

Before implementing, ask:

1. What new reactive workflow, request logic, validation flow, or coordination is being added?
2. Can the current layer host that logic without violating placement rules?
3. If not, what is the smallest feature-local boundary correction that makes the implementation compliant?

If the current structure cannot host the request cleanly, do the boundary correction first, then add the feature.

## Preflight Checklist

Stop and correct the boundary when any of these are true:

- you are adding reusable workflow logic to a view or business component only because the code is already there
- you are keeping async/request/synchronization logic in a component to avoid creating a composable
- you are duplicating or partially copying business logic to avoid touching the existing structure
- the implementation would be "temporary" only because the current layer is already overloaded
- one more nearby requirement would immediately force the same logic to be moved out

Proceed without refactor only when:

- the new logic is genuinely screen-only glue
- the current layer is the correct long-term home for that logic
- extracting it now would create a fake abstraction or unnecessary indirection

## Wrong Interpretation

```text
User request: "Add a details modal with tabbed data loading"

AI reasoning:
- I should keep the diff small
- The page already owns the modal state
- I will also put the detail fetch, tab coordination, and reload logic in the page for now
- Review can tell me what to extract later
```

Problem:

- this optimizes for diff size, not boundary correctness
- it knowingly grows the wrong layer
- it turns review into a cleanup phase instead of a guardrail

## Correct Interpretation

```text
User request: "Add a details modal with tabbed data loading"

AI reasoning:
- The page may keep open/close glue
- The tabbed detail workflow is reusable feature logic, not page-only glue
- I will first extract the smallest feature-local composable for modal detail state and tab coordination
- Then I will wire the page and modal component to that composable
```

## Example

### Wrong

```ts
// ApiList.vue
const visible = ref(false)
const activeTab = ref('detail')
const detail = ref(null)
const versionList = ref([])
const debugState = ref({})

async function openDetail(row) {
  visible.value = true
  detail.value = await fetchDetail(row.id)
}

watch(activeTab, async (tab) => {
  if (tab === 'version') versionList.value = await fetchVersions()
})
```

### Right

```ts
// feature-local composable
export function useApiDetailDialog() {
  const visible = ref(false)
  const activeTab = ref('detail')
  const detail = ref(null)
  const versionList = ref([])

  async function open(rowId: string) {}
  async function loadTab(tab: string) {}

  return { visible, activeTab, detail, versionList, open, loadTab }
}

// ApiList.vue
const dialog = useApiDetailDialog()
```

## Allowed Boundary Corrections

These are still "minimal" when they are required to keep layering correct:

- creating one new feature-local composable
- splitting a heavy composable into a couple of smaller feature-local units
- moving request, sync, or validation logic out of a component into a local composable
- adjusting same-feature imports after that extraction

These are **not** required by this rule:

- repo-wide renames
- new global directory patterns without justification
- unrelated cleanup in neighboring features
- public API changes that are not needed for the requested feature

## Review Guidance

- Flag implementations that preserve the wrong layer in order to keep the diff small.
- Do not praise a change as "minimal" if it relies on obvious follow-up cleanup to become compliant.
- Prefer review language like "this needs a small feature-local extraction first" over vague comments about cleanliness.

## Reference

- [scope-guard-refactors](scope-guard-refactors.md)
- [business-component-boundary](business-component-boundary.md)
- [composable-weight-boundary](composable-weight-boundary.md)
