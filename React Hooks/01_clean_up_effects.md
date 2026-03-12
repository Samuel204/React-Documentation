# 🧹 React Hooks — Clean Up Effects

---

## 📌 What Is a Cleanup Effect?

When you use `useEffect()`, sometimes your effect **creates something that persists** beyond the component's render — like an event listener, a timer, a subscription, or a WebSocket connection.

If you don't clean those up, they keep running **even after the component is gone**, which causes:

- 🐛 **Bugs** (e.g. stale handlers firing on old state)
- 🧠 **Memory leaks** (resources never freed)
- 📉 **Performance issues** (duplicate listeners stacking up)

A **cleanup function** tells React: *"Before you run this effect again — or when this component disappears — do this first."*

---

## 🔧 The Syntax

```jsx
useEffect(() => {
  // 1. Your effect: set up something
  document.addEventListener('keydown', handleKeyPress);

  // 2. Return a cleanup function: tear it down
  return () => {
    document.removeEventListener('keydown', handleKeyPress);
  };
});
```

### Key rule:
> If your `useEffect` callback **returns a function**, React treats it as the **cleanup function**.

---

## ⏱️ When Does the Cleanup Run?

React calls the cleanup function in **two situations**:

| Situation | When |
|---|---|
| **Before re-render** | Every time the component re-renders (if no dependency array, or deps changed) |
| **On unmount** | When the component is removed from the DOM |

### The lifecycle looks like this:

```
1. Component renders → Effect runs (adds event listener)
2. Component re-renders → Cleanup runs first (removes old listener) → Effect runs again (adds new listener)
3. Component unmounts → Cleanup runs (removes listener for good)
```

---

## 🔍 Why This Matters — Without Cleanup

Imagine `useEffect` runs **without** returning a cleanup:

```jsx
useEffect(() => {
  document.addEventListener('mousedown', increment);
  // ❌ No cleanup!
});
```

Every single re-render adds **a new event listener** on top of all the previous ones.

After 10 renders → 10 listeners → clicking once fires `increment` **10 times**. 🔥

---

## ✅ The Full Example — Explained Line by Line

```jsx
import React, { useState, useEffect } from 'react';

export default function Counter() {
  // 1. State: tracks how many times the mouse was clicked
  const [clickCount, setClickCount] = useState(0);

  // 2. Handler: increments state using the functional updater form
  //    (prev => prev + 1) is safer than (clickCount + 1) to avoid stale closures
  const increment = () => setClickCount(prev => prev + 1);

  // 3. Effect: attach the listener when the component renders
  useEffect(() => {
    document.addEventListener('mousedown', increment);

    // 4. Cleanup: remove the listener before the next render or on unmount
    return () => {
      document.removeEventListener('mousedown', increment);
    };
  }); // ← No dependency array: runs after EVERY render

  return (
    <h1>Document Clicks: {clickCount}</h1>
  );
}
```

### Line-by-line breakdown:

| Line | What it does |
|---|---|
| `useState(0)` | Creates the `clickCount` state, starts at 0 |
| `prev => prev + 1` | Uses the **functional updater** to always get the latest state value |
| `useEffect(() => { ... })` | Runs after every render |
| `document.addEventListener(...)` | Attaches `increment` to the whole document |
| `return () => { ... }` | Returns the **cleanup function** |
| `document.removeEventListener(...)` | Removes the exact listener that was added |

---

## 💡 The Functional Updater: `prev => prev + 1`

Notice this pattern:
```jsx
setClickCount(prev => prev + 1);
```

Instead of:
```jsx
setClickCount(clickCount + 1); // ⚠️ Can be stale in effects/callbacks
```

**Why?** Inside event listeners (and effects), the variable `clickCount` can be a **stale closure** — it captures the value at the time the function was created. Using `prev =>` tells React: *"Give me the real current value, then add 1."* This is **always safer inside callbacks**.

---

## 🧠 When Should You Return a Cleanup?

Ask yourself: *"Does my effect create something that outlives this render?"*

| Effect type | Needs cleanup? |
|---|---|
| `addEventListener` | ✅ Yes — `removeEventListener` |
| `setInterval` / `setTimeout` | ✅ Yes — `clearInterval` / `clearTimeout` |
| WebSocket / subscription | ✅ Yes — `.close()` / `.unsubscribe()` |
| `fetch` / API call | ⚠️ Sometimes — abort controller |
| Setting state directly | ❌ No |
| `console.log` | ❌ No |

---

## 🗂️ Quick Summary

```
useEffect(() => {
  // Setup: runs after render
  doSomething();

  return () => {
    // Cleanup: runs before next render or on unmount
    undoSomething();
  };
}, [dependencies]);
```

- The **returned function** = cleanup function
- Cleanup runs **before** the next effect call and **on unmount**
- Without cleanup, effects that add listeners/timers **stack up** and cause bugs
- Use `prev =>` functional updater inside callbacks to avoid stale state

---

*Part of the React Hooks learning series.*
