---
title: When to Allow Orchestration Composables
impact: MEDIUM
impactDescription: defines when a page-level composable is justified
type: capability
tags: composable, orchestration, page-level, coordination, architecture
---

# When to Allow Orchestration Composables

**Impact: MEDIUM** — defines when a page-level composable is justified

Not every page-level composable is a hollow wrapper. An **orchestration composable** is valid when it coordinates multiple data sources, manages cross-concern lifecycle, or hides a complex workflow behind a clean screen-level API.

## Valid Orchestration Indicators

A page-level composable is justified when it does **two or more** of:

- Coordinates lifecycle across multiple composables (e.g., fetch B after A resolves)
- Owns a combined loading/error state
- Manages permissions or route guards that affect multiple sub-features
- Implements a multi-step workflow (wizard, checkout, form submission pipeline)
- Provides a single `reload()` / `reset()` that cascades across sub-composables

## Problem Pattern

```ts
// WRONG: claims orchestration but only forwards
export function useCheckoutPage() {
  const cart = useCart()
  const payment = usePayment()
  return { ...cart, ...payment }  // zero coordination
}
```

## Fix

```ts
// RIGHT: genuine orchestration
export function useCheckoutPage() {
  const { items, total, loading: cartLoading } = useCart()
  const { pay, loading: payLoading } = usePayment()
  const { validateCoupon } = useCoupon()

  const loading = computed(() => cartLoading.value || payLoading.value)
  const canSubmit = computed(() => items.value.length > 0 && !loading.value)

  async function submit(couponCode?: string) {
    if (couponCode) await validateCoupon(couponCode)
    await pay(total.value)
  }

  return { items, total, loading, canSubmit, submit }
}
```

## Decision Rule

> Does this composable **own coordination logic** that would be messy or duplicated if inlined?

- **Yes** → keep the orchestration composable
- **No** → inline in `<script setup>`, see [hollow-wrapper-ban](hollow-wrapper-ban.md)

## Reference

- [Vue Composables](https://vuejs.org/guide/reusability/composables.html)
