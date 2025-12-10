# Clean Up Effects in React useEffect

## What Are Cleanup Functions?

When you use `useEffect` to perform side effects (like adding event listeners, subscriptions, or timers), you often need to **clean up** after those effects to prevent memory leaks and bugs. A cleanup function is a way to tell React: "Before you run this effect again or before the component is removed, do this cleanup first."

---

## Why Do We Need Cleanup?

### The Problem Without Cleanup

Imagine this scenario:

```javascript
useEffect(() => {
  document.addEventListener('keydown', handleKeyPress);
  // ❌ NO CLEANUP - This creates a problem!
});
```

**What happens:**
1. Component renders → Event listener added
2. Component re-renders → **Another** event listener added (the old one is still there!)
3. Component re-renders again → **Another** event listener added
4. After 10 re-renders → You have **10 event listeners** all doing the same thing!

**Result:** Memory leaks, performance issues, unexpected behavior, potential crashes.

---

## The Solution: Return a Cleanup Function

```javascript
useEffect(() => {
  // 1. Set up the effect
  document.addEventListener('keydown', handleKeyPress);
  
  // 2. Return a cleanup function
  return () => {
    document.removeEventListener('keydown', handleKeyPress);
  };
});
```

### How It Works:

1. **First render:** React runs the effect, adds the event listener
2. **Before re-render:** React runs the cleanup function, removes the old listener
3. **Re-render:** React runs the effect again, adds a fresh listener
4. **Before unmount:** React runs the cleanup function one last time

---

## Analyzing Your Code Example

```javascript
import React, { useState, useEffect } from 'react';

export default function Counter() {
  const [clickCount, setClickCount] = useState(0);

  // The useEffect Hook
  useEffect(() => {
    // SETUP: Add event listener when component renders
    document.addEventListener('mousedown', increment);
    
    // CLEANUP: Remove event listener before re-render or unmount
    return () => {
      document.removeEventListener('mousedown', increment);
    }
  });

  const increment = () => {
    setClickCount((prev) => clickCount + 1);
  };

  return (
    <h1>Document Clicks: {clickCount}</h1>
  );
}
```

### Code Breakdown:

#### 1. **The State**
```javascript
const [clickCount, setClickCount] = useState(0);
```
- Creates a state variable to track clicks
- Starts at 0

#### 2. **The Effect Setup**
```javascript
document.addEventListener('mousedown', increment);
```
- Attaches a listener to the **entire document** (not just the component)
- Every mousedown anywhere on the page will trigger `increment`

#### 3. **The Cleanup Function**
```javascript
return () => {
  document.removeEventListener('mousedown', increment);
}
```
- This function is returned from `useEffect`
- React will call this **before** the next effect runs
- React will call this when the component **unmounts**
- Removes the event listener to prevent duplicates

#### 4. **The Event Handler**
```javascript
const increment = () => {
  setClickCount((prev) => clickCount + 1);
};
```
- Updates the click count when called
- ⚠️ **Note:** There's a bug here! Should use `prev + 1` instead of `clickCount + 1`

---

## When Does Cleanup Run?

React calls your cleanup function in two situations:

### 1. **Before Re-running the Effect**
```
Render 1 → Effect runs → State changes → 
Cleanup runs → Render 2 → Effect runs again
```

### 2. **Before Component Unmounts**
```
Component visible → User navigates away → 
Cleanup runs → Component removed from DOM
```

---

## Common Use Cases for Cleanup

### Example 1: Timers
```javascript
useEffect(() => {
  const timerId = setInterval(() => {
    console.log('Tick');
  }, 1000);

  return () => {
    clearInterval(timerId); // Clean up the timer
  };
}, []);
```

### Example 2: Subscriptions
```javascript
useEffect(() => {
  const subscription = dataSource.subscribe();

  return () => {
    subscription.unsubscribe(); // Clean up the subscription
  };
}, []);
```

### Example 3: Event Listeners
```javascript
useEffect(() => {
  const handleResize = () => console.log('Resized!');
  window.addEventListener('resize', handleResize);

  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []);
```

---

## Key Takeaways

1. **Cleanup is optional** - Only return a cleanup function if your effect needs it
2. **Cleanup prevents memory leaks** - Always clean up event listeners, timers, subscriptions
3. **Cleanup runs before re-render** - React ensures old effects are cleaned up before new ones run
4. **Cleanup runs on unmount** - Ensures no lingering effects after component is gone
5. **Return a function** - The cleanup must be a function returned from `useEffect`

---

## Practice Challenge

Try to identify what needs cleanup:

```javascript
// ✅ Needs cleanup
useEffect(() => {
  document.addEventListener('scroll', handleScroll);
  // Need to remove listener
});

// ✅ Needs cleanup
useEffect(() => {
  const timer = setTimeout(() => {}, 1000);
  // Need to clear timer
});

// ❌ No cleanup needed
useEffect(() => {
  setCount(count + 1);
  // State updates don't need cleanup
});
```

---

## Bug Fix in Your Code

Your increment function has a small issue:

```javascript
// ❌ Current (uses stale value)
const increment = () => {
  setClickCount((prev) => clickCount + 1);
};

// ✅ Fixed (uses prev parameter correctly)
const increment = () => {
  setClickCount((prev) => prev + 1);
};
```

The parameter `prev` represents the current state value, so you should use `prev + 1` instead of `clickCount + 1` to avoid stale closure issues.