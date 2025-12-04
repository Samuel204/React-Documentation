# React State Hook: Set From Previous State

## Introduction

When updating state based on its previous value, React provides a safer way to handle updates using callback functions. This section explains why this approach is important and how to use it correctly.

---

## Review: Simple State Updates

In previous lessons, we learned to update state by passing a new value directly:

```jsx
setState(newStateValue);
```

**Example:**
```jsx
const [count, setCount] = useState(0);

// Simple update with a new value
setCount(5);
```

This works fine when the new state value doesn't depend on the previous state.

---

## The Problem: Asynchronous State Updates

**Critical Concept:** React state updates are asynchronous.

This means:
- State updates don't happen immediately
- Multiple state updates may be grouped together (batched)
- Your code continues running before the state finishes updating

### Why This Matters

**Good news:** Batching state updates improves performance by reducing unnecessary re-renders.

**Bad news:** You might accidentally use an outdated state value when calculating the next state.

---

## The Solution: Callback Functions

**Best Practice:** When the next state depends on the previous state, use a callback function instead of a direct value.

### The Safe Way

```jsx
setState(prevState => prevState + 1);
```

The callback function:
- Receives the most recent state value as an argument
- Returns the new state value
- Guarantees you're working with the correct previous state

---

## Complete Example: Counter with Callback

```jsx
import React, { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prevCount => prevCount + 1);

  return (
    <div>
      <p>Wow, you've clicked that button: {count} times</p>
      <button onClick={increment}>Click here!</button>
    </div>
  );
}
```
definisco una funzione e la assegno alla costante increment.

---

## Breaking Down the Callback Approach

### Step 1: State Initialization

```jsx
const [count, setCount] = useState(0);
```

- Creates `count` state variable with initial value of `0`
- Creates `setCount` setter function

### Step 2: Event Handler with Callback

```jsx
const increment = () => setCount(prevCount => prevCount + 1);
```

**What's happening:**
1. `increment` is a function that calls `setCount`
2. `setCount` receives a callback function: `prevCount => prevCount + 1`
3. The callback function takes one parameter: `prevCount` (the previous state value)
4. The callback returns the new state: `prevCount + 1`

### Step 3: Using the Event Handler

```jsx
<button onClick={increment}>Click here!</button>
```

When the button is clicked:
1. `increment()` is called
2. `setCount()` is called with the callback function
3. React calls the callback with the current `count` value
4. The callback returns `prevCount + 1`
5. React updates `count` with the returned value
6. Component re-renders with the new count

---

## Understanding the Callback Function

```jsx
setCount(prevCount => prevCount + 1)
```

### Anatomy of the Callback

- **Parameter:** `prevCount` - React automatically passes the current state value here
- **Arrow Function:** `prevCount => ...` - Defines the function
- **Return Value:** `prevCount + 1` - The new state value React will use

**You can name the parameter anything:**
```jsx
setCount(prev => prev + 1)
setCount(current => current + 1)
setCount(oldCount => oldCount + 1)
```

The name doesn't matter—React will always pass the current state value as the first argument.

---

## Why Use Callbacks? The Problem with Direct Updates

### Scenario: Multiple Quick Updates

Imagine a user rapidly clicks a button multiple times:

```jsx
// ❌ Problem: Direct state updates
const increment = () => setCount(count + 1);

// User clicks 3 times rapidly
// Click 1: setCount(0 + 1) → count should be 1
// Click 2: setCount(0 + 1) → count should be 2, but still uses old count (0)!
// Click 3: setCount(0 + 1) → count should be 3, but still uses old count (0)!
// Result: count = 1 (only one increment registered!)
```

**Why this happens:**
- State updates are batched
- All three clicks happen before the first update completes
- Each click uses the old `count` value (0)

### Solution: Callback Functions

```jsx
// ✅ Solution: Callback functions
const increment = () => setCount(prevCount => prevCount + 1);

// User clicks 3 times rapidly
// Click 1: setCount(prev => 0 + 1) → queued: 1
// Click 2: setCount(prev => 1 + 1) → queued: 2
// Click 3: setCount(prev => 2 + 1) → queued: 3
// Result: count = 3 (all increments registered!)
```

**Why this works:**
- Each callback receives the most recent state
- Updates are applied in order
- No updates are lost

---

## Comparison: Direct vs. Callback Updates

### When the New State Doesn't Depend on Previous State

```jsx
// ✅ Both work fine
setCount(5);
setCount(() => 5);
```

If you're setting a specific value that doesn't depend on the previous state, either approach works.

### When the New State Depends on Previous State

```jsx
// ⚠️ Works in simple cases, but not safe
setCount(count + 1);

// ✅ Always safe and recommended
setCount(prevCount => prevCount + 1);
```

**Use callbacks when:**
- Incrementing/decrementing values
- Toggling booleans
- Updating based on current state
- Multiple updates might happen quickly

---

## Practical Examples

### Example 1: Increment and Decrement

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);
  const reset = () => setCount(0); // Direct value is fine here

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### Example 2: Toggle Boolean

```jsx
function ToggleSwitch() {
  const [isOn, setIsOn] = useState(false);

  const toggle = () => setIsOn(prevIsOn => !prevIsOn);

  return (
    <div>
      <p>Switch is {isOn ? 'ON' : 'OFF'}</p>
      <button onClick={toggle}>Toggle</button>
    </div>
  );
}
```

### Example 3: Multiple Increments

```jsx
function MultiIncrement() {
  const [count, setCount] = useState(0);

  const incrementByFive = () => {
    // All five updates use the callback
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={incrementByFive}>+5</button>
    </div>
  );
}
```

---

## The Callback Function in Detail

### What React Does Behind the Scenes

```jsx
setCount(prevCount => prevCount + 1);
```

**Step-by-step execution:**

1. You call `setCount` with a callback function
2. React queues this update
3. When processing updates, React calls your callback
4. React passes the current state value as `prevCount`
5. Your callback returns the new value
6. React uses that returned value as the new state
7. Component re-renders with the new state

### The Parameter Name Doesn't Matter

All these are equivalent:

```jsx
setCount(prevCount => prevCount + 1)
setCount(prev => prev + 1)
setCount(c => c + 1)
setCount(oldValue => oldValue + 1)
setCount(previousState => previousState + 1)
```

Choose a name that makes sense to you!

---

## When to Use Each Approach

### Use Direct Values When:

```jsx
// Setting a specific value
setName("Alice");
setIsOpen(true);
setColor("blue");
setCount(0); // Reset to specific value
```

- The new state doesn't depend on the old state
- You're setting a specific, known value
- You're resetting to a default value

### Use Callback Functions When:

```jsx
// Updating based on previous state
setCount(prev => prev + 1);
setIsOpen(prev => !prev);
setItems(prev => [...prev, newItem]);
```

- The new state depends on the previous state
- You're doing calculations with the current state
- Multiple updates might happen quickly
- You're toggling boolean values
- You're adding/removing items from arrays or objects

---

## Common Patterns

### Pattern 1: Increment/Decrement

```jsx
const increment = () => setValue(prev => prev + 1);
const decrement = () => setValue(prev => prev - 1);
const incrementBy = (amount) => setValue(prev => prev + amount);
```

### Pattern 2: Toggle

```jsx
const toggle = () => setIsActive(prev => !prev);
```

### Pattern 3: Conditional Update

```jsx
const increment = () => {
  setCount(prev => {
    if (prev >= 10) return prev; // Don't go above 10
    return prev + 1;
  });
};
```

### Pattern 4: Complex Calculation

```jsx
const updateScore = (points) => {
  setScore(prevScore => {
    const newScore = prevScore + points;
    const bonus = newScore >= 100 ? 50 : 0;
    return newScore + bonus;
  });
};
```

---

## Why It's "Safer" - Technical Details

While `setCount(count + 1)` may work in simple scenarios, using callbacks is safer because:

1. **Closures:** The `count` variable in your function might be "stale" (from a previous render)
2. **Batching:** React batches multiple state updates for performance
3. **Asynchronous nature:** State updates don't happen immediately
4. **Concurrent features:** Future React features rely on proper state updates

The callback approach guarantees you always work with the most recent state value.

---

## Common Mistakes to Avoid

### 1. Forgetting to Return a Value

```jsx
// ❌ Wrong - No return value
setCount(prev => { prev + 1 });

// ✅ Correct - Explicit return
setCount(prev => { return prev + 1 });

// ✅ Correct - Implicit return (no curly braces)
setCount(prev => prev + 1);
```

### 2. Mutating State Directly

```jsx
// ❌ Wrong - Don't mutate the previous state
setItems(prevItems => {
  prevItems.push(newItem); // Mutation!
  return prevItems;
});

// ✅ Correct - Return a new array
setItems(prevItems => [...prevItems, newItem]);
```

### 3. Using Current State Instead of Callback

```jsx
// ⚠️ Not safe for multiple rapid updates
setCount(count + 1);

// ✅ Safe for all scenarios
setCount(prev => prev + 1);
```

---

## Key Takeaways

1. **React state updates are asynchronous** - they don't happen immediately
2. **Use callback functions** when the new state depends on the previous state
3. **Callbacks receive the most recent state** as their first parameter
4. **The callback must return** the new state value
5. **Direct values are fine** when not depending on previous state
6. **Callbacks prevent bugs** in scenarios with multiple rapid updates
7. **The parameter name is your choice** - React just passes the current state
