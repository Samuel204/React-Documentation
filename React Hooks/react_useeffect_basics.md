# React Effect Hook: Function Component Effects

## Introduction

The Effect Hook tells our component to execute code every time it renders or re-renders. Combined with state, we can create dynamic, interactive web pages that respond to user actions and synchronize with external systems.

---

## What Does useEffect Do?

**Core concept:** `useEffect()` runs code after every render (by default).

```jsx
function Component() {
  useEffect(() => {
    // This code runs after EVERY render
    console.log('Component rendered!');
  });
  
  return <div>Hello</div>;
}
```

**The render cycle:**
1. Component function runs
2. JSX is returned
3. React updates the DOM
4. **useEffect runs** ← Your side effect executes here

---

## Basic Syntax

### Importing useEffect

```jsx
import React, { useState, useEffect } from 'react';
//                        ^
//                        └─ Import useEffect from React
```

**Note:** `useEffect` is a named export, like `useState`

### Basic Structure

```jsx
useEffect(() => {
  // Effect code here
  // Runs after component renders
});
```

**Key parts:**
- `useEffect()` - The Hook function
- `() => { ... }` - Callback function (your effect)
- No return value (unlike useState)

---

## Complete Example: Dynamic Page Title

Let's build a component that updates the browser tab title as the user types:

```jsx
import React, { useState, useEffect } from 'react';

function PageTitle() {
  // State: tracks user input
  const [name, setName] = useState('');
  
  // Effect: updates document title after every render
  useEffect(() => {
    document.title = `Hi, ${name}`;
  });
  
  return (
    <div>
      <p>Use the input field below to rename this page!</p>
      <input 
        onChange={({target}) => setName(target.value)} 
        value={name} 
        type='text' 
      />
    </div>
  );
}
```

---

## Breaking Down the Example

### Step 1: Import Dependencies

```jsx
import React, { useState, useEffect } from 'react';
```

**What we're importing:**
- `React` - Core React library
- `useState` - State Hook for managing data
- `useEffect` - Effect Hook for side effects

### Step 2: Initialize State

```jsx
const [name, setName] = useState('');
```

**Purpose:**
- Stores the current input value
- Starts as empty string
- Updates when user types

### Step 3: Set Up the Effect

```jsx
useEffect(() => {
  document.title = `Hi, ${name}`;
});
```

**What's happening:**
- `useEffect()` registers the effect
- Callback function: `() => { document.title = ... }`
- Runs after component renders
- Has access to current `name` value

### Step 4: Create the UI

```jsx
return (
  <div>
    <p>Use the input field below to rename this page!</p>
    <input 
      onChange={({target}) => setName(target.value)} 
      value={name} 
      type='text' 
    />
  </div>
);
```

**How it works:**
- Input displays current `name` value
- `onChange` updates state when user types
- State update triggers re-render
- Re-render triggers effect

---

## The Effect Callback Function

### What Is the Effect?

```jsx
() => { 
  document.title = `Hi, ${name}`;
}
```

**Anatomy:**
- Arrow function: `() => { ... }`
- Side effect: Modifies `document.title`
- Uses current state: `${name}`
- Passed to `useEffect()` as argument

### Why a Callback?

```jsx
// ✅ Correct: Pass a function
useEffect(() => {
  document.title = `Hi, ${name}`;
});

// ❌ Wrong: Execute immediately
useEffect(
  document.title = `Hi, ${name}` // This runs immediately!
);
```

**Reason:** React needs to control *when* the effect runs, not *if* it runs.

---

## How the Effect Executes

### The Complete Flow

```
User Action (typing)
    ↓
onChange event fires
    ↓
setName() called
    ↓
State updates: name = "John"
    ↓
Component re-renders
    ↓
React updates DOM
    ↓
useEffect() runs
    ↓
document.title = "Hi, John"
```

### Timeline Visualization

#### Initial Render (Mount)

```
Time: 0ms
├─ PageTitle() function executes
│  ├─ useState('') initializes → name = ''
│  ├─ useEffect(() => {...}) registers effect
│  └─ return JSX

Time: 10ms
├─ React updates DOM
└─ Input field rendered (empty)

Time: 20ms
└─ useEffect runs
   └─ document.title = "Hi, "
```

#### User Types "J"

```
Time: 100ms
├─ onChange event fires
├─ setName('J') called
└─ State update scheduled

Time: 110ms
├─ PageTitle() function executes again
│  ├─ useState returns → name = 'J'
│  ├─ useEffect(() => {...}) registers effect
│  └─ return JSX

Time: 120ms
├─ React updates DOM
└─ Input field shows "J"

Time: 130ms
└─ useEffect runs
   └─ document.title = "Hi, J"
```

#### User Types "ohn" (completes "John")

```
Time: 500ms
├─ Multiple onChange events
├─ Multiple setName calls
└─ Final: name = 'John'

Time: 510ms
├─ PageTitle() executes
│  ├─ name = 'John'
│  ├─ useEffect registered
│  └─ return JSX

Time: 520ms
├─ React updates DOM
└─ Input shows "John"

Time: 530ms
└─ useEffect runs
   └─ document.title = "Hi, John"
```

---

## Key Concept: Access to Current State

### The Effect Has Access to Variables in Scope

```jsx
function PageTitle() {
  const [name, setName] = useState('');
  
  // Effect can access 'name' even though it runs after render
  useEffect(() => {
    console.log('Current name:', name); // Has access!
    document.title = `Hi, ${name}`;
  });
  
  return <input value={name} onChange={e => setName(e.target.value)} />;
}
```

**Why this works:**
- Effects are defined inside the component function
- They're part of the component's closure
- They capture the current values of state and props
- Each render creates a new effect with fresh values

### Closures in Action

```jsx
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // This effect "closes over" the current count value
    console.log('Count is:', count);
    // If count is 5, this will always log 5
    // Even if count changes later, THIS effect has 5
  });
  
  // Next render creates a NEW effect with NEW count value
}
```

---

## When Does the Effect Run?

### Default Behavior: Every Render

```jsx
function Component() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  useEffect(() => {
    console.log('Effect ran!');
  }); // No dependency array
  
  // Effect runs when:
  // 1. Component mounts (first render)
  // 2. count changes (re-render)
  // 3. name changes (re-render)
  // 4. Parent re-renders (re-render)
  // 5. Any other reason for re-render
}
```

**Without dependencies:** Effect runs after *every* render, regardless of what caused it.

---

## React's Rendering Process

### The Order of Operations

```jsx
function Component() {
  console.log('1. Component function runs');
  
  const [state, setState] = useState('initial');
  console.log('2. State initialized/retrieved');
  
  useEffect(() => {
    console.log('5. Effect runs (after DOM update)');
  });
  
  console.log('3. Before return');
  
  return (
    <div>
      {console.log('4. JSX evaluated')}
      Hello
    </div>
  );
}

// Console output:
// 1. Component function runs
// 2. State initialized/retrieved
// 3. Before return
// 4. JSX evaluated
// (DOM updates happen here)
// 5. Effect runs (after DOM update)
```

**Key insight:** Effects run *after* the DOM has been updated, not during render.

---

## Practical Examples

### Example 1: Character Counter

```jsx
function CharacterCounter() {
  const [text, setText] = useState('');
  
  // Update document title with character count
  useEffect(() => {
    document.title = `${text.length} characters`;
  });
  
  return (
    <div>
      <textarea
        value={text}
        onChange={e => setText(e.target.value)}
        placeholder="Type something..."
      />
      <p>Characters: {text.length}</p>
    </div>
  );
}
```

**Flow:**
1. User types → `setText()` called
2. Component re-renders with new text
3. Effect runs → Updates title with new count

### Example 2: Console Logger

```jsx
function FormLogger() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  
  // Log form state after every change
  useEffect(() => {
    console.log('Form state:', { email, password });
  });
  
  return (
    <form>
      <input
        type="email"
        value={email}
        onChange={e => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={e => setPassword(e.target.value)}
        placeholder="Password"
      />
    </form>
  );
}
```

**Logs:**
- After every keystroke in either field
- Shows current state of both fields
- Useful for debugging

### Example 3: Live Theme Updater

```jsx
function ThemeSwitcher() {
  const [theme, setTheme] = useState('light');
  
  // Apply theme to body element
  useEffect(() => {
    document.body.className = theme;
    console.log(`Theme changed to: ${theme}`);
  });
  
  return (
    <div>
      <button onClick={() => setTheme('light')}>Light</button>
      <button onClick={() => setTheme('dark')}>Dark</button>
      <p>Current theme: {theme}</p>
    </div>
  );
}
```

**Flow:**
1. User clicks button → `setTheme()` called
2. Component re-renders with new theme
3. Effect runs → Applies class to body

### Example 4: Multiple Effects

```jsx
function MultiEffect() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  // Effect 1: Update title with count
  useEffect(() => {
    document.title = `Count: ${count}`;
  });
  
  // Effect 2: Log name changes
  useEffect(() => {
    console.log('Name:', name);
  });
  
  // Effect 3: Log everything
  useEffect(() => {
    console.log('State:', { count, name });
  });
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
    </div>
  );
}
```

**Note:** All three effects run after every render!

---

## Important Characteristics

### 1. Effects Run After Render

```jsx
function Component() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    // This runs AFTER the DOM has been updated
    // The user can see the UI before this executes
    console.log('Effect running');
  });
  
  return <div>UI is visible before effect runs</div>;
}
```

**Why?** 
- Doesn't block rendering
- UI appears quickly
- Side effects happen asynchronously

### 2. Effects Have Access to Latest State

```jsx
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // Always has the latest count value
    console.log('Current count:', count);
  });
  
  return <button onClick={() => setCount(count + 1)}>+</button>;
}
```

**Why?**
- Effect is recreated on each render
- Captures current values via closure
- Always in sync with component state

### 3. Every Render Gets Its Own Effect

```jsx
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // This is a NEW function on each render
    // With the current count value captured
    setTimeout(() => {
      console.log('Count was:', count);
    }, 3000);
  });
  
  return <button onClick={() => setCount(count + 1)}>+</button>;
}
```

**Demonstration:**
1. Click button (count = 1)
2. Wait 1 second
3. Click again (count = 2)
4. Wait 1 second
5. Click again (count = 3)
6. After 3 seconds from step 1: logs "Count was: 1"
7. After 3 seconds from step 3: logs "Count was: 2"
8. After 3 seconds from step 5: logs "Count was: 3"

Each effect captured its own count value!

---

## Common Patterns

### Pattern 1: Synchronizing with External Systems

```jsx
function WindowTitle({ title }) {
  useEffect(() => {
    // Sync React state with browser
    document.title = title;
  });
  
  return <h1>{title}</h1>;
}
```

### Pattern 2: Logging and Debugging

```jsx
function DebugComponent({ user }) {
  useEffect(() => {
    // Log state changes for debugging
    console.log('Component rendered with:', user);
  });
  
  return <div>User: {user.name}</div>;
}
```

### Pattern 3: Side Effects After State Changes

```jsx
function Notification({ message }) {
  const [show, setShow] = useState(true);
  
  useEffect(() => {
    // Show notification, then hide after delay
    if (show) {
      console.log('Showing:', message);
    }
  });
  
  return show ? <div>{message}</div> : null;
}
```

---

## What We Haven't Covered Yet

This basic useEffect runs after every render. But what if we want:

- To run only once? (on mount)
- To run only when specific values change?
- To clean up resources?
- To prevent infinite loops?

**Don't worry!** We'll learn about:
- **Dependency arrays** - Control when effects run
- **Cleanup functions** - Clean up resources
- **Effect optimization** - Prevent unnecessary runs

These topics are covered in the next sections!

---

## Common Mistakes (We'll Fix Later)

### Mistake 1: Infinite Loops

```jsx
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // ⚠️ This causes infinite loop!
    setCount(count + 1); // State update → re-render → effect → state update...
  });
}
```

**Solution:** Use dependency array (next section)

### Mistake 2: Running Too Often

```jsx
function Component() {
  const [data, setData] = useState([]);
  
  useEffect(() => {
    // ⚠️ Fetches on EVERY render (inefficient)
    fetch('/api/data')
      .then(res => res.json())
      .then(data => setData(data));
  });
}
```

**Solution:** Use dependency array (next section)

---

## Key Takeaways

1. **useEffect runs after render** - DOM updates first, then effects
2. **Runs on every render by default** - Mount and all updates
3. **Has access to current state/props** - Via closure/scope
4. **No return value** - Used to call other functions
5. **Callback function is the effect** - The code you want to run
6. **Multiple effects are fine** - Use multiple useEffect calls
7. **Effects don't block rendering** - Run asynchronously
8. **Each render gets new effect** - Fresh values captured

---

## Practice Exercises

### Exercise 1: Input Tracker

Create a component that logs every character the user types:

```jsx
function InputTracker() {
  const [input, setInput] = useState('');
  
  // Add useEffect to log input changes
  
  return (
    <input
      value={input}
      onChange={e => setInput(e.target.value)}
    />
  );
}
```

### Exercise 2: Color Theme

Create a component that applies a background color to the page:

```jsx
function ColorTheme() {
  const [color, setColor] = useState('white');
  
  // Add useEffect to set document.body.style.backgroundColor
  
  return (
    <div>
      <button onClick={() => setColor('lightblue')}>Blue</button>
      <button onClick={() => setColor('lightgreen')}>Green</button>
      <button onClick={() => setColor('lightyellow')}>Yellow</button>
    </div>
  );
}
```

### Exercise 3: Live Clock Title

Create a component that shows the current time in the page title:

```jsx
function ClockTitle() {
  const [time, setTime] = useState(new Date());
  
  // Add useEffect to update document.title with time
  
  return <div>{time.toLocaleTimeString()}</div>;
}
```

---

## Next Steps

Now that you understand the basics of useEffect, you're ready to learn:

1. **Dependency Arrays** - Control when effects run
2. **Cleanup Functions** - Clean up resources properly
3. **Effect Optimization** - Prevent unnecessary executions
4. **Common Patterns** - Data fetching, subscriptions, timers
5. **Best Practices** - Write maintainable, performant effects

Continue to the next section to master these advanced concepts!