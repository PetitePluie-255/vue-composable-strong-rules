---
title: No Leaked Subscriptions
impact: HIGH
impactDescription: prevents subscription leaks from unpaired start/stop contracts
type: capability
tags: subscription, cleanup, start-stop, composable, memory-leak
---

# No Leaked Subscriptions

**Impact: HIGH** — prevents subscription leaks from unpaired start/stop contracts

If a composable returns a `start()`, `subscribe()`, or `connect()` handle, it **must** also return or auto-register the matching cleanup handle. The cleanup contract must be obvious to callers.

## Symptoms

- WebSocket connections stay open after navigating away
- Event bus listeners fire after the subscribing component unmounts
- Multiple subscriptions accumulate on each re-mount
- `onDeactivated` (keep-alive) does not pause subscriptions

## Problem Pattern

```ts
// composables/useNotifications.ts — LEAKED SUBSCRIPTION
export function useNotifications() {
  const messages = ref<Message[]>([])

  function subscribe() {
    // ❌ Returns nothing — caller has no way to unsubscribe
    eventBus.on('notification', (msg) => {
      messages.value.push(msg)
    })
  }

  return { messages, subscribe }
}
```

## Fix

**Pattern 1: Auto-cleanup with cleanup registration**
```ts
export function useNotifications() {
  const messages = ref<Message[]>([])
  let unsubscribe: (() => void) | null = null

  function subscribe() {
    // Clean up previous subscription first
    unsubscribe?.()

    const handler = (msg: Message) => { messages.value.push(msg) }
    eventBus.on('notification', handler)
    unsubscribe = () => eventBus.off('notification', handler)
  }

  function stop() {
    unsubscribe?.()
    unsubscribe = null
  }

  // Auto-cleanup when scope is disposed
  onScopeDispose(stop)

  return { messages, subscribe, stop }
}
```

**Pattern 2: Constructor-style with auto-start**
```ts
export function useWebSocket(url: string) {
  const data = ref<string | null>(null)
  let ws: WebSocket | null = null

  function connect() {
    disconnect()
    ws = new WebSocket(url)
    ws.onmessage = (e) => { data.value = e.data }
  }

  function disconnect() {
    ws?.close()
    ws = null
  }

  // Always register cleanup
  onScopeDispose(disconnect)
  // Also handle keep-alive
  onDeactivated(disconnect)
  onActivated(connect)

  return { data, connect, disconnect }
}
```

## Contract Rules

| Composable exposes | Must also expose/register | Notes |
|---|---|---|
| `start()` | `stop()` + `onScopeDispose(stop)` | |
| `subscribe()` | `unsubscribe()` + `onScopeDispose(unsubscribe)` | |
| `connect()` | `disconnect()` + `onScopeDispose(disconnect)` | Handle `onDeactivated` for keep-alive |
| `open()` | `close()` + `onScopeDispose(close)` | |

## Reference

- [Vue onScopeDispose](https://vuejs.org/api/reactivity-advanced.html#onscopedispose)
- [VueUse tryOnScopeDispose](https://vueuse.org/shared/tryOnScopeDispose/)
