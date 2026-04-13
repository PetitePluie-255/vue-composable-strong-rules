---
title: Architecture Conflict Protocol
impact: HIGH
impactDescription: prevents silent violation of project architecture rules when user requests conflict
type: capability
tags: conflict, architecture, protocol, compliance, communication
---

# Architecture Conflict Protocol

**Impact: HIGH** — prevents silent violation of project architecture rules when user requests conflict

When a user request directly conflicts with the project's composable-first architecture rules, the AI must **state the conflict explicitly** and **propose the compliant alternative** before making any edits.

## Why This Matters

- Silent compliance with a bad request creates tech debt the user didn't intend
- Silent violation of architecture rules undermines the project's conventions
- Stating the conflict gives the user the chance to make an informed decision
- The user may not realize their request conflicts with established patterns

## Symptoms of Missing This Protocol

- A composable is created that violates architecture rules because the user asked for it
- Reactive logic ends up in `utils/` because the user said "add it to utils"
- Page-specific state is pushed to global store because the user said "put it in the store"
- No explanation of why the implementation differs from the request
- Post-implementation review catches architecture violations that could have been prevented

## Problem Pattern

```
User: "Add a useOrderPage() composable that wraps useOrder() and useShipping()"

AI silently creates:
```

```ts
// ❌ AI complied without flagging the hollow wrapper problem
export function useOrderPage() {
  const { order } = useOrder()
  const { shipping } = useShipping()
  return { order, shipping }  // hollow wrapper
}
```

## Correct Protocol

### Step 1: State the conflict BEFORE editing

```text
Architecture conflict: the request asks for a useOrderPage() composable,
but this would create a hollow wrapper that only re-exports useOrder() and
useShipping() without adding orchestration (violates hollow-wrapper-ban rule).

Two options:
1. Inline both calls in the view's <script setup> (recommended if no
   orchestration is needed)
2. Add real orchestration — e.g., coordinate loading states, fetch shipping
   after order loads, combined error handling — to justify the composable

Which approach would you prefer?
```

### Step 2: Implement the chosen approach

```ts
// Option 1: Inline in view (if user chose simplicity)
// OrderPage.vue
const { order, loading: orderLoading } = useOrder()
const { shipping, loading: shippingLoading } = useShipping()

// Option 2: Real orchestration (if user chose composable)
export function useOrderPage() {
  const { order, loading: orderLoading, fetchOrder } = useOrder()
  const { shipping, loading: shippingLoading, fetchShipping } = useShipping()

  const loading = computed(() => orderLoading.value || shippingLoading.value)

  // Orchestration: fetch shipping after order resolves
  watch(order, (o) => { if (o) fetchShipping(o.id) })

  const error = ref<Error | null>(null)
  async function reload() {
    error.value = null
    try { await fetchOrder() } catch (e) { error.value = e as Error }
  }

  return { order, shipping, loading, error, reload }
}
```

## Conflict Declaration Template

```text
Architecture conflict: <what the request asks for> conflicts with
<which rule> because <why it violates the rule>.

Compliant alternative: <smallest compliant change that satisfies the intent
while respecting the rule. Include a local boundary correction first if the
current structure cannot host the change cleanly>.

Want me to proceed with the compliant alternative, or do you prefer
the original approach with an explicit override?
```

## Common Conflict Scenarios

| User request | Conflicting rule | Compliant alternative |
|---|---|---|
| "Put this in utils" (but it uses `ref`/`watch`) | [utils-no-reactivity](utils-no-reactivity.md) | Move to `composables/`, keep pure helper in `utils/` |
| "Add this to the store" (single-page state) | [store-justification](store-justification.md) | Use local composable instead |
| "Create useXxxPage() that wraps everything" | [hollow-wrapper-ban](hollow-wrapper-ban.md) | Inline in view, or add real orchestration |
| "Use provide/inject for cross-route data" | [provide-inject-scope](provide-inject-scope.md) | Use Pinia store for cross-route state |
| "Refactor all composables while you're at it" | [scope-guard-refactors](scope-guard-refactors.md) | Ship the requested change only, plus any local boundary correction required for compliance; propose broader refactor as follow-up |

## Key Principle

> **Never silently comply with a request that violates architecture rules.**
> **Never silently override a user's request with a different implementation.**
> **Always state the conflict, explain the tradeoff, and let the user decide.**

## Reference

- Procedure §4 and Implementation Pattern §4 in the parent SKILL.md
- [scope-guard-refactors](scope-guard-refactors.md) for related scope discipline
- [boundary-first-minimality](boundary-first-minimality.md) for deciding when local boundary correction is part of the compliant path
