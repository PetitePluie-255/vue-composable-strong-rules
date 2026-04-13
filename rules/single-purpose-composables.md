---
title: Single-Purpose Composables
impact: MEDIUM
impactDescription: balances composable granularity to avoid both monoliths and over-fragmentation
type: capability
tags: composable, granularity, single-responsibility, architecture
---

# Single-Purpose Composables

**Impact: MEDIUM** — balances composable granularity to avoid both monoliths and over-fragmentation

Prefer multiple single-purpose composables over one god-composable. But do not split so aggressively that the feature becomes harder to follow.

## Symptoms of Under-Split (Monolith)

- A single composable with 200+ lines and 15+ returned refs
- Unrelated concerns mixed: search logic + modal state + export logic in one file
- Changing one feature risks breaking others in the same composable
- Cannot test one concern without setting up all others

## Symptoms of Over-Split (Fragmentation)

- 8 composables each owning a single ref and a single function
- View component imports 10+ composables with trivial bodies
- The mental overhead of tracing data flow across files exceeds the benefit
- A 3-line composable that just wraps `ref('')` and a setter

## Decision Guide

| Indicator | Split ✅ | Keep together ❌ |
|---|---|---|
| Two concerns are independently reusable | ✅ | |
| Two concerns share no state | ✅ | |
| Logic is < 10 lines with no side effects | | ❌ keep inline |
| Splitting would require passing 5+ refs between composables | | ❌ keep together |
| Concern is only used in one place and is trivially simple | | ❌ keep inline |

## Problem Pattern

```ts
// ❌ Over-split: useless single-ref composables
export function useSearchKeyword() {
  const keyword = ref('')
  return { keyword }
}

export function useSearchResults() {
  const results = ref([])
  return { results }
}

export function useSearchLoading() {
  const loading = ref(false)
  return { loading }
}
```

## Fix

```ts
// ✅ Single-purpose but cohesive
export function useSearch(fetchFn: (q: string) => Promise<Item[]>) {
  const keyword = ref('')
  const results = ref<Item[]>([])
  const loading = ref(false)

  const debouncedSearch = useDebounceFn(async (q: string) => {
    loading.value = true
    try { results.value = await fetchFn(q) }
    finally { loading.value = false }
  }, 300)

  watch(keyword, (q) => debouncedSearch(q))

  return { keyword, results, loading }
}
```

## Splitting Guideline

```
Page: OrderList
├── useOrderSearch()     — keyword, debounced fetch, results
├── useOrderExport()     — export logic, file download
├── useOrderFilters()    — date range, status filter, tag filter
└── inline in view       — modal open/close, tab selection
```

Each composable owns a **cohesive concern** with its own state, side effects, and cleanup.

## Reference

- [Vue Composables](https://vuejs.org/guide/reusability/composables.html)
