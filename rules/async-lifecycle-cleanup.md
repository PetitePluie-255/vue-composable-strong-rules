---
title: Async Lifecycle Cleanup
impact: HIGH
impactDescription: prevents memory leaks and stale state from uncleaned async operations
type: capability
tags: async, cleanup, AbortController, watch, lifecycle, composable
---

# Async Lifecycle Cleanup

**Impact: HIGH** — prevents memory leaks and stale state from uncleaned async operations

Every composable that creates watchers, timers, event listeners, or in-flight requests must clean them up when the consuming component unmounts or the effect scope is disposed.

## Symptoms

- `Failed to fetch` errors appear after navigating away from a page
- `Cannot set properties of null` warnings after component unmount
- Loading spinners stuck forever after route changes
- Stale data from a previous page overwriting current page data
- Memory usage grows with each page navigation

## Problem Pattern

```ts
// composables/usePolling.ts — MISSING CLEANUP
export function usePolling(url: string) {
  const data = ref(null)
  const loading = ref(false)

  // ❌ No way to cancel this interval
  setInterval(async () => {
    loading.value = true
    data.value = await fetch(url).then(r => r.json())
    loading.value = false
  }, 5000)

  // ❌ No AbortController for in-flight request
  async function fetchNow() {
    data.value = await fetch(url).then(r => r.json())
  }

  return { data, loading, fetchNow }
}
```

## Fix

**Pattern 1: AbortController + onScopeDispose**
```ts
export function usePolling(url: string, intervalMs = 5000) {
  const data = ref(null)
  const loading = ref(false)
  let controller: AbortController | null = null
  let timer: ReturnType<typeof setInterval> | null = null

  async function fetchNow() {
    controller?.abort()
    controller = new AbortController()
    loading.value = true
    try {
      const res = await fetch(url, { signal: controller.signal })
      data.value = await res.json()
    } catch (e) {
      if (e instanceof DOMException && e.name === 'AbortError') return
      throw e
    } finally {
      loading.value = false
    }
  }

  function start() {
    stop()
    fetchNow()
    timer = setInterval(fetchNow, intervalMs)
  }

  function stop() {
    controller?.abort()
    if (timer) { clearInterval(timer); timer = null }
  }

  onScopeDispose(stop)

  return { data, loading, start, stop, fetchNow }
}
```

**Pattern 2: watchEffect (auto-disposed)**
```ts
// Preferred when watcher is component-scoped — auto-stops on unmount
watchEffect((onCleanup) => {
  const controller = new AbortController()
  onCleanup(() => controller.abort())

  fetch(url.value, { signal: controller.signal })
    .then(r => r.json())
    .then(d => { data.value = d })
})
```

**Pattern 3: Ignore flag for non-abortable APIs**
```ts
export function useAsyncData<T>(fetchFn: () => Promise<T>) {
  const data = ref<T | null>(null) as Ref<T | null>
  let generation = 0

  async function reload() {
    const thisGen = ++generation
    const result = await fetchFn()
    if (thisGen === generation) {  // ignore stale responses
      data.value = result
    }
  }

  onScopeDispose(() => { generation++ })  // invalidate pending

  return { data, reload }
}
```

## Cleanup Checklist

| Resource | Cleanup | Recommended API |
|---|---|---|
| `fetch` / XHR | `AbortController.abort()` | `onScopeDispose` |
| `setInterval` | `clearInterval()` | `onScopeDispose` |
| `setTimeout` | `clearTimeout()` | `onScopeDispose` |
| `addEventListener` | `removeEventListener()` | `onScopeDispose` / `tryOnScopeDispose` |
| `watch` (manual) | return value `stop()` | auto if in `setup()`, manual otherwise |
| `watchEffect` | auto-disposed in scope | preferred for component-scoped |
| WebSocket | `close()` | `onScopeDispose` |
| `ResizeObserver` | `disconnect()` | `onScopeDispose` |

## Reference

- [Vue Effect Scope](https://vuejs.org/api/reactivity-advanced.html#effectscope)
- [Vue onScopeDispose](https://vuejs.org/api/reactivity-advanced.html#onscopedispose)
- [MDN AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)
