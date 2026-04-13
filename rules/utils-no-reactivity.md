---
title: Utils Must Stay Free of Vue Reactivity
impact: HIGH
impactDescription: prevents reactive coupling in pure utility modules
type: capability
tags: utils, reactivity, separation, composable, architecture
---

# Utils Must Stay Free of Vue Reactivity

**Impact: HIGH** — prevents reactive coupling in pure utility modules

If a utility function imports `ref`, `reactive`, `watch`, `computed`, `onMounted`, or any Vue lifecycle/reactivity API, it belongs in a composable — not `utils/`.

## Symptoms

- Unit testing a utility requires mocking Vue internals
- Importing a utility triggers Vue warnings outside component context
- A "utility" function only works inside `setup()` but its filename suggests otherwise
- Tree-shaking fails because the utility pulls in Vue runtime

## Problem Pattern

```ts
// utils/formatPrice.ts — WRONG
import { computed, type Ref } from 'vue'

export function formatPrice(price: Ref<number>) {
  return computed(() => `¥${price.value.toFixed(2)}`)
}
```

This looks like a utility, but it requires a reactive ref and returns a computed — it is a composable in disguise.

## Fix

**Option 1: Keep the utility pure**
```ts
// utils/formatPrice.ts — pure function, no Vue dependency
export function formatPrice(price: number): string {
  return `¥${price.toFixed(2)}`
}
```

**Option 2: Move reactive version to composable**
```ts
// composables/useFormattedPrice.ts
import { computed, type Ref } from 'vue'
import { formatPrice } from '@/utils/formatPrice'

export function useFormattedPrice(price: Ref<number>) {
  return computed(() => formatPrice(price.value))
}
```

## Decision Rule

| Condition | Placement |
|---|---|
| No Vue imports needed, pure input → output | `utils/` |
| Needs `ref`, `computed`, `watch`, or lifecycle hooks | `composables/` |
| Wraps a pure utility with reactivity for convenience | `composables/`, import the pure utility internally |

## Reference

- [Vue Composition API](https://vuejs.org/guide/reusability/composables.html)
