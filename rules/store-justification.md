---
title: Global Store Justification
impact: MEDIUM
impactDescription: prevents store bloat by enforcing clear admission criteria
type: capability
tags: store, pinia, global-state, scope, architecture
---

# Global Store Justification

**Impact: MEDIUM** — prevents store bloat by enforcing clear admission criteria

Global store (Pinia) is the **last resort** for state placement. Every piece of state entering the store must pass an admission check.

## Symptoms

- Store has dozens of modules, most used by a single page
- Devtools show hundreds of reactive properties in the store
- Page-specific loading/error states clutter the store
- Developers default to `defineStore` for any shared data

## Admission Criteria

State belongs in a global store **only if** it satisfies at least one:

| Criterion | Example |
|---|---|
| **Cross-route persistence** | Shopping cart survives navigation |
| **Multiple distant consumers** | Auth token read by API layer, header, sidebar |
| **App-level coordination** | WebSocket connection state, global notifications |
| **Shared cache** | Cached API responses shared by unrelated views |

## Problem Pattern

```ts
// store/usePageFilterStore.ts — WRONG
export const usePageFilterStore = defineStore('pageFilter', () => {
  const keyword = ref('')          // only OrderList page uses this
  const dateRange = ref<[Date, Date] | null>(null) // only OrderList page
  return { keyword, dateRange }
})
```

## Fix

```ts
// composables/usePageFilter.ts — RIGHT: local composable
export function usePageFilter() {
  const keyword = ref('')
  const dateRange = ref<[Date, Date] | null>(null)

  function reset() {
    keyword.value = ''
    dateRange.value = null
  }

  return { keyword, dateRange, reset }
}
```

## When Store IS Correct

```ts
// store/useAuthStore.ts — CORRECT: cross-route, multi-consumer
export const useAuthStore = defineStore('auth', () => {
  const token = ref<string | null>(null)
  const user = ref<User | null>(null)
  const isAuthenticated = computed(() => !!token.value)

  // Used by: API interceptor, Header, Sidebar, Route guards
  return { token, user, isAuthenticated }
})
```

## Reference

- [Pinia: When to use a store](https://pinia.vuejs.org/introduction.html#when-should-i-use-a-store)
