---
title: State Placement by Scope
impact: HIGH
impactDescription: prevents state leaking to wrong architectural layer
type: capability
tags: state, scope, composable, store, provide-inject, architecture
---

# State Placement by Scope

**Impact: HIGH** — prevents state leaking to wrong architectural layer

Every piece of reactive state should live at the narrowest scope that satisfies its consumers. Placing state too high (global store) wastes coordination overhead; too low (view) blocks reuse.

## Symptoms

- Global store bloated with page-specific data that no other page reads
- Two sibling components duplicating the same fetch logic because state is stuck in a view
- `provide/inject` used across routes where the provider doesn't exist on the target route
- A composable imports a store just to read one flag

## Placement Matrix

| Scope | When to use | Example |
|---|---|---|
| **View component** | Screen-only presentation state, one-off UI glue | `showModal`, `activeTab`, form draft before submit |
| **Local composable** | Reusable reactive logic, async workflows, validation | `useSearch()`, `useFormValidation()`, `usePagination()` |
| **`provide/inject`** | Subtree-scoped sharing among nested components | Form context, wizard step state, feature-local service |
| **Global store** | Cross-route persistence, app-wide session, distant consumers | Auth state, user preferences, shared cache |

## Problem Pattern

```ts
// store/modules/orderList.ts — WRONG: page-specific state in global store
export const useOrderListStore = defineStore('orderList', () => {
  const searchKeyword = ref('')       // only used by OrderList page
  const selectedTab = ref('pending')  // only used by OrderList page
  const orders = ref<Order[]>([])
  // ...
})
```

## Fix

```ts
// composables/useOrderList.ts — RIGHT: page-scoped composable
export function useOrderList() {
  const searchKeyword = ref('')
  const selectedTab = ref('pending')
  const orders = ref<Order[]>([])

  async function fetchOrders() { /* ... */ }

  watch([searchKeyword, selectedTab], () => fetchOrders())
  onScopeDispose(() => { /* cleanup */ })

  return { searchKeyword, selectedTab, orders, fetchOrders }
}
```

## Decision Checklist

1. Does any **other page** read or write this state? → If no, keep it out of global store.
2. Does this state need to **survive route transitions**? → If no, composable or view is enough.
3. Do **nested child components** need this state? → Consider `provide/inject` before reaching for store.
4. Is this state **purely presentational** (tab, modal, animation)? → Keep in view.

## Reference

- [Vue State Management](https://vuejs.org/guide/scaling-up/state-management.html)
- [Pinia: When to use](https://pinia.vuejs.org/introduction.html#when-should-i-use-a-store)
