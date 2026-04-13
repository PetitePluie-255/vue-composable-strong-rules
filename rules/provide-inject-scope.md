---
title: provide/inject Scope Boundaries
impact: HIGH
impactDescription: prevents inject failures and misplaced shared state
type: capability
tags: provide, inject, scope, subtree, store, architecture
---

# provide/inject Scope Boundaries

**Impact: HIGH** — prevents inject failures and misplaced shared state

`provide/inject` is designed for **subtree-scoped** state sharing. Misusing it across routes or unrelated component trees causes silent `undefined` injections.

## Symptoms

- `inject()` returns `undefined` after navigation
- A component works on one route but breaks on another
- State "disappears" when the provider component unmounts
- Developers reach for `provide` as a "lighter store" across the entire app

## When provide/inject Is Right ✅

- **Form context**: parent form provides validation state to nested field components
- **Wizard/stepper**: step container shares progress state with step children
- **Feature-local service**: a feature root provides a shared API client or config to its subtree
- **Layout slots**: a layout component shares theme or sizing context

```ts
// ✅ Form context — provider and consumers share a subtree
// FormRoot.vue
const formCtx = useFormContext()
provide(FORM_CTX_KEY, formCtx)

// FormField.vue (child of FormRoot)
const { validate, resetField } = inject(FORM_CTX_KEY)!
```

## When provide/inject Is Wrong ❌

- **Cross-route state**: provider on Route A, consumer on Route B → inject gets `undefined`
- **No parent-child relationship**: two sibling pages both need the state
- **State must survive route transitions**: user session, cart, preferences → use store
- **App-wide singletons**: auth, notifications, feature flags → use store

```ts
// ❌ WRONG: provider only exists on /checkout, but /order-confirm also injects
// CheckoutPage.vue
provide(ORDER_KEY, orderState)

// OrderConfirmPage.vue — separate route, no provider ancestor
const order = inject(ORDER_KEY)  // undefined!
```

## Fix for Cross-Route Case

```ts
// Use a Pinia store instead
export const useOrderStore = defineStore('order', () => {
  const order = ref<Order | null>(null)
  return { order }
})
```

## Decision Checklist

1. Are all consumers **descendants** of the provider component? → provide/inject ✅
2. Can consumers exist **without** the provider in the component tree? → store ✅
3. Does the state need to persist across **route changes**? → store ✅
4. Is the sharing limited to a **single feature subtree**? → provide/inject ✅

## Reference

- [Vue provide/inject](https://vuejs.org/guide/components/provide-inject.html)
- [provide-inject-types](../../../vue-best-practices/rules/provide-inject-types.md)
