---
title: Ban Hollow Page-Level Wrapper Composables
impact: HIGH
impactDescription: prevents fake extraction that adds indirection without value
type: capability
tags: composable, wrapper, extraction, hollow, architecture
---

# Ban Hollow Page-Level Wrapper Composables

**Impact: HIGH** — prevents fake extraction that adds indirection without value

A "hollow wrapper" composable mostly gathers view-level refs, renames or re-exports nested composables, and does not add orchestration, lifecycle management, or a coherent API boundary.

## Symptoms

- A `useXxxPage()` composable that returns 10+ refs but owns no logic
- The composable's body is mostly `const { a } = useA(); const { b } = useB(); return { a, b }`
- Removing the wrapper and inlining the calls in `<script setup>` changes nothing
- The wrapper has no `watch`, `computed`, lifecycle hooks, or error coordination

## Problem Pattern

```ts
// composables/useUserPage.ts — HOLLOW WRAPPER
export function useUserPage() {
  const { user, loading } = useUser()
  const { roles } = useRoles()
  const showModal = ref(false)
  const activeTab = ref('profile')

  return { user, loading, roles, showModal, activeTab }
}
```

This adds a file, an import hop, and a name — but zero orchestration.

## Fix

**Option 1: Inline in view (preferred when no orchestration needed)**
```vue
<script setup lang="ts">
const { user, loading } = useUser()
const { roles } = useRoles()
const showModal = ref(false)
const activeTab = ref('profile')
</script>
```

**Option 2: Upgrade to real orchestration composable**
```ts
// composables/useUserPage.ts — REAL ORCHESTRATION
export function useUserPage() {
  const { user, loading: userLoading, fetchUser } = useUser()
  const { roles, loading: rolesLoading, fetchRoles } = useRoles()

  const loading = computed(() => userLoading.value || rolesLoading.value)
  const canEdit = computed(() => roles.value.includes('admin'))

  // Coordinate: fetch roles after user loads
  watch(user, (u) => { if (u) fetchRoles(u.id) })

  // Error boundary
  const error = ref<Error | null>(null)
  async function reload() {
    error.value = null
    try { await fetchUser() } catch (e) { error.value = e as Error }
  }

  return { user, roles, loading, canEdit, error, reload }
}
```

## Litmus Test

> If I delete this composable and paste its body into `<script setup>`, does the page still work identically with no loss of coordination or testability?

If **yes** → it is a hollow wrapper. Do not extract.
If **no** → it provides real value. Keep it.

## Reference

- [Vue Composables](https://vuejs.org/guide/reusability/composables.html)
