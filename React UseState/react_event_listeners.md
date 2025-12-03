# React Event Listeners: Complete Guide

## Introduction

Event listeners allow React components to respond to user interactions like clicks, typing, hovering, and form submissions. This guide covers all major React event listeners, when to use them, and how they work.

---

## React Events vs. HTML Events

### HTML (Vanilla JavaScript)

```html
<!-- HTML - lowercase, string as attribute -->
<button onclick="handleClick()">Click me</button>
```

### React (JSX)

```jsx
// React - camelCase, function reference in curly braces
<button onClick={handleClick}>Click me</button>
```

**Key Differences:**
1. **Naming:** React uses camelCase (`onClick` not `onclick`)
2. **Value:** React passes function reference, not string
3. **Event Object:** React uses SyntheticEvent (wrapper around native events)
4. **No `return false`:** Must call `preventDefault()` explicitly

---

## Mouse Events

### onClick

**When to use:** Detect when user clicks on an element

```jsx
function ClickButton() {
  const handleClick = () => {
    console.log('Button clicked!');
  };

  return <button onClick={handleClick}>Click me</button>;
}
```

**Common use cases:**
- Button actions
- Card/item selection
- Menu item clicks
- Toggle visibility

**Event object properties:**
```jsx
const handleClick = (event) => {
  console.log(event.target);      // The clicked element
  console.log(event.clientX);     // Mouse X coordinate
  console.log(event.clientY);     // Mouse Y coordinate
  console.log(event.button);      // Which button: 0=left, 1=middle, 2=right
};
```

**Examples:**

```jsx
// Example 1: Counter
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      {/* Increment when clicked */}
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}

// Example 2: Toggle visibility
function ToggleContent() {
  const [isVisible, setIsVisible] = useState(false);
  
  return (
    <div>
      {/* Toggle visibility on click */}
      <button onClick={() => setIsVisible(!isVisible)}>
        {isVisible ? 'Hide' : 'Show'} Content
      </button>
      {isVisible && <p>Hidden content revealed!</p>}
    </div>
  );
}

// Example 3: Get click coordinates
function ClickTracker() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  const handleClick = (event) => {
    // Track where the user clicked
    setPosition({
      x: event.clientX,
      y: event.clientY
    });
  };
  
  return (
    <div onClick={handleClick} style={{ height: '200px', border: '1px solid black' }}>
      Click anywhere! Last click: ({position.x}, {position.y})
    </div>
  );
}
```

---

### onDoubleClick

**When to use:** Detect double-clicks (two rapid clicks)

```jsx
function DoubleClickDemo() {
  const [message, setMessage] = useState('');
  
  const handleDoubleClick = () => {
    setMessage('Double clicked!');
  };

  return (
    <div>
      {/* Responds only to double-clicks */}
      <button onDoubleClick={handleDoubleClick}>
        Double click me
      </button>
      <p>{message}</p>
    </div>
  );
}
```

**Common use cases:**
- Open file/folder (like file explorer)
- Edit mode activation
- Advanced interactions

---

### onMouseEnter

**When to use:** Detect when mouse enters an element

```jsx
function HoverCard() {
  const [isHovered, setIsHovered] = useState(false);
  
  return (
    <div
      onMouseEnter={() => setIsHovered(true)}   // Mouse enters
      onMouseLeave={() => setIsHovered(false)}  // Mouse leaves
      style={{
        padding: '20px',
        backgroundColor: isHovered ? 'lightblue' : 'lightgray'
      }}
    >
      {/* Show different content on hover */}
      {isHovered ? 'Hovering!' : 'Hover over me'}
    </div>
  );
}
```

**Common use cases:**
- Hover effects
- Tooltips
- Preview cards
- Dropdown menus

---

### onMouseLeave

**When to use:** Detect when mouse leaves an element

```jsx
function TooltipDemo() {
  const [showTooltip, setShowTooltip] = useState(false);
  
  return (
    <div style={{ position: 'relative' }}>
      <button
        onMouseEnter={() => setShowTooltip(true)}   // Show on enter
        onMouseLeave={() => setShowTooltip(false)}  // Hide on leave
      >
        Hover for tooltip
      </button>
      {/* Tooltip appears on hover */}
      {showTooltip && (
        <div style={{
          position: 'absolute',
          top: '-30px',
          backgroundColor: 'black',
          color: 'white',
          padding: '5px'
        }}>
          This is a tooltip!
        </div>
      )}
    </div>
  );
}
```

**Common use cases:**
- Hide tooltips
- Reset hover states
- Close dropdown menus

---

### onMouseMove

**When to use:** Track mouse movement over an element

```jsx
function MouseTracker() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  const handleMouseMove = (event) => {
    // Update position on every mouse move
    setPosition({
      x: event.clientX,
      y: event.clientY
    });
  };
  
  return (
    <div 
      onMouseMove={handleMouseMove}
      style={{ height: '300px', border: '1px solid black' }}
    >
      {/* Display live mouse coordinates */}
      Mouse position: X: {position.x}, Y: {position.y}
    </div>
  );
}
```

**Common use cases:**
- Drawing applications
- Drag and drop
- Interactive visualizations
- Custom cursors

---

### onMouseDown / onMouseUp

**When to use:** Detect when mouse button is pressed/released

```jsx
function PressButton() {
  const [isPressed, setIsPressed] = useState(false);
  
  return (
    <button
      onMouseDown={() => setIsPressed(true)}   // Pressed
      onMouseUp={() => setIsPressed(false)}    // Released
      style={{
        backgroundColor: isPressed ? 'darkblue' : 'lightblue',
        transform: isPressed ? 'scale(0.95)' : 'scale(1)'
      }}
    >
      {/* Visual feedback while pressing */}
      Press me
    </button>
  );
}
```

**Common use cases:**
- Custom button press effects
- Drag and drop initiation
- Drawing/painting apps

---

## Keyboard Events

### onKeyDown

**When to use:** Detect when a key is pressed down

```jsx
function KeyboardInput() {
  const [lastKey, setLastKey] = useState('');
  
  const handleKeyDown = (event) => {
    // Capture which key was pressed
    setLastKey(event.key);
    
    // Check for specific keys
    if (event.key === 'Enter') {
      console.log('Enter pressed!');
    }
    if (event.key === 'Escape') {
      console.log('Escape pressed!');
    }
  };
  
  return (
    <input 
      onKeyDown={handleKeyDown}
      placeholder="Press any key"
    />
  );
}
```

**Event object properties:**
```jsx
const handleKeyDown = (event) => {
  console.log(event.key);         // 'a', 'Enter', 'ArrowUp', etc.
  console.log(event.code);        // 'KeyA', 'Enter', 'ArrowUp', etc.
  console.log(event.shiftKey);    // true if Shift is pressed
  console.log(event.ctrlKey);     // true if Ctrl is pressed
  console.log(event.altKey);      // true if Alt is pressed
  console.log(event.metaKey);     // true if Cmd (Mac) / Win key is pressed
};
```

**Common use cases:**
- Keyboard shortcuts (Ctrl+S, Ctrl+C)
- Form submission on Enter
- Modal closing on Escape
- Game controls
- Accessibility features

**Examples:**

```jsx
// Example 1: Submit on Enter
function SearchBox() {
  const [query, setQuery] = useState('');
  
  const handleKeyDown = (event) => {
    // Submit search when Enter is pressed
    if (event.key === 'Enter') {
      console.log('Searching for:', query);
    }
  };
  
  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      onKeyDown={handleKeyDown}
      placeholder="Search... (press Enter)"
    />
  );
}

// Example 2: Keyboard shortcuts
function Editor() {
  const handleKeyDown = (event) => {
    // Save on Ctrl+S (or Cmd+S on Mac)
    if ((event.ctrlKey || event.metaKey) && event.key === 's') {
      event.preventDefault(); // Prevent browser's save dialog
      console.log('Saving document...');
    }
  };
  
  return (
    <textarea
      onKeyDown={handleKeyDown}
      placeholder="Type here... (Ctrl+S to save)"
    />
  );
}

// Example 3: Arrow key navigation
function GameControls() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  const handleKeyDown = (event) => {
    // Move with arrow keys
    if (event.key === 'ArrowUp') {
      setPosition(prev => ({ ...prev, y: prev.y - 10 }));
    } else if (event.key === 'ArrowDown') {
      setPosition(prev => ({ ...prev, y: prev.y + 10 }));
    } else if (event.key === 'ArrowLeft') {
      setPosition(prev => ({ ...prev, x: prev.x - 10 }));
    } else if (event.key === 'ArrowRight') {
      setPosition(prev => ({ ...prev, x: prev.x + 10 }));
    }
  };
  
  return (
    <div onKeyDown={handleKeyDown} tabIndex={0}>
      {/* tabIndex makes div focusable */}
      <div style={{
        position: 'absolute',
        left: position.x,
        top: position.y,
        width: '50px',
        height: '50px',
        backgroundColor: 'blue'
      }} />
      Click here and use arrow keys to move
    </div>
  );
}
```

---

### onKeyUp

**When to use:** Detect when a key is released

```jsx
function KeyPressTracker() {
  const [isPressed, setIsPressed] = useState(false);
  
  return (
    <input
      onKeyDown={() => setIsPressed(true)}   // Key pressed
      onKeyUp={() => setIsPressed(false)}    // Key released
      placeholder={isPressed ? 'Key is down!' : 'Press any key'}
    />
  );
}
```

**Common use cases:**
- Detecting when user finishes typing
- Game controls (jump when key released)
- Animation triggers

---

### onKeyPress (Deprecated)

**Note:** `onKeyPress` is deprecated. Use `onKeyDown` instead.

---

## Form Events

### onChange

**When to use:** Detect when input value changes (most common for forms)

```jsx
function TextInput() {
  const [value, setValue] = useState('');
  
  const handleChange = (event) => {
    // Update state on every keystroke
    setValue(event.target.value);
  };
  
  return (
    <div>
      <input 
        value={value}
        onChange={handleChange}
        placeholder="Type here"
      />
      {/* Display current value */}
      <p>You typed: {value}</p>
    </div>
  );
}
```

**Works with:**
- `<input>` (text, number, email, password, etc.)
- `<textarea>`
- `<select>` (dropdowns)
- `<input type="checkbox">`
- `<input type="radio">`

**Common use cases:**
- Controlled inputs
- Form validation
- Search boxes
- Live text filtering

**Examples:**

```jsx
// Example 1: Text input
function NameInput() {
  const [name, setName] = useState('');
  
  return (
    <input
      value={name}
      onChange={(e) => setName(e.target.value)}
      placeholder="Enter your name"
    />
  );
}

// Example 2: Checkbox
function TodoItem() {
  const [isCompleted, setIsCompleted] = useState(false);
  
  return (
    <label>
      <input
        type="checkbox"
        checked={isCompleted}
        onChange={(e) => setIsCompleted(e.target.checked)} // Note: .checked not .value
      />
      Task completed
    </label>
  );
}

// Example 3: Dropdown
function CountrySelector() {
  const [country, setCountry] = useState('');
  
  return (
    <select value={country} onChange={(e) => setCountry(e.target.value)}>
      <option value="">Select a country</option>
      <option value="us">United States</option>
      <option value="uk">United Kingdom</option>
      <option value="ca">Canada</option>
    </select>
  );
}

// Example 4: Radio buttons
function SizeSelector() {
  const [size, setSize] = useState('');
  
  return (
    <div>
      <label>
        <input
          type="radio"
          value="small"
          checked={size === 'small'}
          onChange={(e) => setSize(e.target.value)}
        />
        Small
      </label>
      <label>
        <input
          type="radio"
          value="medium"
          checked={size === 'medium'}
          onChange={(e) => setSize(e.target.value)}
        />
        Medium
      </label>
      <label>
        <input
          type="radio"
          value="large"
          checked={size === 'large'}
          onChange={(e) => setSize(e.target.value)}
        />
        Large
      </label>
    </div>
  );
}
```

---

### onSubmit

**When to use:** Handle form submission

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  
  const handleSubmit = (event) => {
    event.preventDefault(); // Prevent page reload
    console.log('Submitting:', { email, password });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      {/* Submit button triggers onSubmit */}
      <button type="submit">Login</button>
    </form>
  );
}
```

**Important:** Always call `event.preventDefault()` to prevent default form submission (page reload)

**Common use cases:**
- Form validation before submission
- API calls with form data
- Multi-step forms
- Search forms

---

### onFocus

**When to use:** Detect when an element receives focus

```jsx
function FocusInput() {
  const [isFocused, setIsFocused] = useState(false);
  
  return (
    <input
      onFocus={() => setIsFocused(true)}      // Gets focus
      onBlur={() => setIsFocused(false)}      // Loses focus
      placeholder="Click to focus"
      style={{
        border: isFocused ? '2px solid blue' : '1px solid gray'
      }}
    />
  );
}
```

**Common use cases:**
- Highlight active input
- Show helper text
- Validation messages
- Keyboard navigation feedback

---

### onBlur

**When to use:** Detect when an element loses focus

```jsx
function ValidatedInput() {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');
  
  const handleBlur = () => {
    // Validate when user leaves the field
    if (email && !email.includes('@')) {
      setError('Please enter a valid email');
    } else {
      setError('');
    }
  };
  
  return (
    <div>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        onBlur={handleBlur}
        placeholder="Email"
      />
      {/* Show error after user leaves field */}
      {error && <p style={{ color: 'red' }}>{error}</p>}
    </div>
  );
}
```

**Common use cases:**
- Form field validation
- Save draft data
- Close dropdowns/menus
- Format input (e.g., phone numbers)

---

### onInput

**When to use:** Similar to `onChange`, fires on every input change

**Note:** In React, `onChange` is preferred. Use `onInput` only if you need the specific HTML5 behavior.

---

## Focus Events

### onFocus vs onBlur Summary

```jsx
function FocusDemo() {
  const [status, setStatus] = useState('');
  
  return (
    <div>
      <input
        onFocus={() => setStatus('Input is focused')}
        onBlur={() => setStatus('Input lost focus')}
        placeholder="Click here"
      />
      <p>{status}</p>
    </div>
  );
}
```

---

## Clipboard Events

### onCopy / onCut / onPaste

**When to use:** Handle clipboard operations

```jsx
function ClipboardDemo() {
  const [message, setMessage] = useState('');
  
  const handleCopy = () => {
    setMessage('Text copied!');
  };
  
  const handlePaste = (event) => {
    // Access pasted content
    const pastedText = event.clipboardData.getData('text');
    setMessage(`You pasted: ${pastedText}`);
  };
  
  return (
    <div>
      <input
        onCopy={handleCopy}
        onPaste={handlePaste}
        placeholder="Try copying or pasting"
      />
      <p>{message}</p>
    </div>
  );
}
```

**Common use cases:**
- Tracking copy actions
- Preventing paste (security)
- Formatting pasted content
- Analytics

---

## Touch Events (Mobile)

### onTouchStart / onTouchMove / onTouchEnd

**When to use:** Handle touch interactions on mobile devices

```jsx
function SwipeDetector() {
  const [touchStart, setTouchStart] = useState(0);
  const [touchEnd, setTouchEnd] = useState(0);
  
  const handleTouchStart = (e) => {
    setTouchStart(e.touches[0].clientX);
  };
  
  const handleTouchEnd = () => {
    // Detect swipe direction
    if (touchStart - touchEnd > 50) {
      console.log('Swiped left');
    }
    if (touchEnd - touchStart > 50) {
      console.log('Swiped right');
    }
  };
  
  return (
    <div
      onTouchStart={handleTouchStart}
      onTouchMove={(e) => setTouchEnd(e.touches[0].clientX)}
      onTouchEnd={handleTouchEnd}
      style={{ height: '200px', border: '1px solid black' }}
    >
      Swipe left or right
    </div>
  );
}
```

**Common use cases:**
- Swipe gestures
- Mobile games
- Image galleries
- Drawer menus

---

## Scroll Events

### onScroll

**When to use:** Detect when element is scrolled

```jsx
function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);
  
  const handleScroll = (event) => {
    // Track scroll position
    setScrollY(event.target.scrollTop);
  };
  
  return (
    <div
      onScroll={handleScroll}
      style={{
        height: '200px',
        overflow: 'auto',
        border: '1px solid black'
      }}
    >
      <div style={{ height: '600px' }}>
        <p>Scroll position: {scrollY}px</p>
        <p>Content here...</p>
      </div>
    </div>
  );
}
```

**Common use cases:**
- Infinite scroll
- Parallax effects
- Scroll-to-top button
- Sticky headers
- Loading more content

---

## Drag and Drop Events

### onDrag / onDrop

**When to use:** Handle drag and drop interactions

```jsx
function DragDropDemo() {
  const [draggedItem, setDraggedItem] = useState('');
  
  const handleDragStart = (event) => {
    // Store what's being dragged
    setDraggedItem(event.target.textContent);
  };
  
  const handleDrop = (event) => {
    event.preventDefault();
    console.log('Dropped:', draggedItem);
  };
  
  const handleDragOver = (event) => {
    event.preventDefault(); // Required to allow drop
  };
  
  return (
    <div>
      <div
        draggable
        onDragStart={handleDragStart}
        style={{ padding: '10px', border: '1px solid blue' }}
      >
        Drag me
      </div>
      
      <div
        onDrop={handleDrop}
        onDragOver={handleDragOver}
        style={{
          padding: '20px',
          border: '2px dashed gray',
          marginTop: '20px'
        }}
      >
        Drop here
      </div>
    </div>
  );
}
```

**Common use cases:**
- File uploads
- Kanban boards
- Reorderable lists
- Visual editors

---

## Composition Events

### onCompositionStart / onCompositionEnd

**When to use:** Handle complex input methods (Asian languages, voice input)

```jsx
function CompositionInput() {
  const [isComposing, setIsComposing] = useState(false);
  const [value, setValue] = useState('');
  
  return (
    <input
      value={value}
      onChange={(e) => setValue(e.target.value)}
      onCompositionStart={() => setIsComposing(true)}
      onCompositionEnd={() => setIsComposing(false)}
      placeholder="Type in any language"
    />
  );
}
```

**Common use cases:**
- International text input
- IME (Input Method Editor) handling
- Voice-to-text

---

## Animation Events

### onAnimationStart / onAnimationEnd

**When to use:** React to CSS animation lifecycle

```jsx
function AnimatedBox() {
  const [animating, setAnimating] = useState(false);
  
  return (
    <div
      className={animating ? 'slide-in' : ''}
      onAnimationStart={() => console.log('Animation started')}
      onAnimationEnd={() => setAnimating(false)}
    >
      Animated content
    </div>
  );
}
```

---

## Transition Events

### onTransitionEnd

**When to use:** React to CSS transition completion

```jsx
function FadeComponent() {
  const [visible, setVisible] = useState(true);
  
  const handleTransitionEnd = () => {
    console.log('Transition complete');
  };
  
  return (
    <div
      style={{
        opacity: visible ? 1 : 0,
        transition: 'opacity 0.5s'
      }}
      onTransitionEnd={handleTransitionEnd}
    >
      Fading content
    </div>
  );
}
```

---

## Event Handler Patterns

### Pattern 1: Inline Arrow Function

```jsx
<button onClick={() => console.log('Clicked')}>
  Click me
</button>
```

**When to use:** Simple, one-line handlers

**Pros:** Concise
**Cons:** Creates new function on every render

---

### Pattern 2: Separate Function

```jsx
function MyComponent() {
  const handleClick = () => {
    console.log('Clicked');
  };
  
  return <button onClick={handleClick}>Click me</button>;
}
```

**When to use:** Complex logic, reusable handlers

**Pros:** Better performance, easier to test
**Cons:** More code

---

### Pattern 3: With Parameters

```jsx
function ItemList() {
  const handleDelete = (id) => {
    console.log('Deleting item:', id);
  };
  
  return (
    <div>
      {/* Need arrow function to pass parameters */}
      <button onClick={() => handleDelete(1)}>Delete Item 1</button>
      <button onClick={() => handleDelete(2)}>Delete Item 2</button>
    </div>
  );
}
```

---

### Pattern 4: Using Event Object

```jsx
function Form() {
  const handleChange = (event) => {
    console.log('New value:', event.target.value);
  };
  
  return <input onChange={handleChange} />;
}
```

---

## Preventing Default Behavior

```jsx
function LinkButton() {
  const handleClick = (event) => {
    event.preventDefault(); // Prevent link navigation
    console.log('Link clicked, but not navigating');
  };
  
  return (
    <a href="https://example.com" onClick={handleClick}>
      Click me
    </a>
  );
}
```

**Common uses for `preventDefault()`:**
- Form submission
- Link navigation
- Context menu
- Drag and drop

---

## Stopping Event Propagation

```jsx
function NestedButtons() {
  const handleParentClick = () => {
    console.log('Parent clicked');
  };
  
  const handleChildClick = (event) => {
    event.stopPropagation(); // Prevent parent's onClick
    console.log('Child clicked');
  };
  
  return (
    <div onClick={handleParentClick} style={{ padding: '20px', background: 'lightgray' }}>
      Parent
      <button onClick={handleChildClick}>
        Child (stops propagation)
      </button>
    </div>
  );
}
```

**When to use:** Prevent event bubbling to parent elements

---

## Event Listener Quick Reference

| Event | When It Fires | Common Use Cases |
|-------|---------------|------------------|
| `onClick` | Element clicked | Buttons, links, selections |
| `onChange` | Input value changes | Form inputs, controlled components |
| `onSubmit` | Form submitted | Form validation, API calls |
| `onKeyDown` | Key pressed | Keyboard shortcuts, form submission |
| `onKeyUp` | Key released | Game controls, typing detection |
| `onMouseEnter` | Mouse enters | Hover effects, tooltips |
| `onMouseLeave` | Mouse leaves | Hide tooltips, reset states |
| `onMouseMove` | Mouse moves | Tracking, drawing, drag |
| `onFocus` | Element focused | Highlight inputs, show helpers |
| `onBlur` | Element unfocused | Validation, save drafts |
| `onScroll` | Element scrolled | Infinite scroll, sticky headers |
| `onDrop` | Item dropped | File uploads, reordering |

---

## Best Practices

1. **Use camelCase** - `onClick` not `onclick`
2. **Pass function references** - `onClick={handleClick}` not `onClick={handleClick()}`
3. **Use arrow functions for parameters** - `onClick={() => doSomething(id)}`
4. **Call preventDefault when needed** - Prevent default form/link behavior
5. **Consider performance** - Don't create functions in render for large lists
6. **Name handlers clearly** - `handleClick`, `handleSubmit`, `handleChange`
7. **Use event object** - Access `event.target`, `event.key`, etc.
8. **Stop propagation carefully** - Only when necessary

---

## Key Takeaways

1. **React events use camelCase** - `onClick`, `onChange`, `onSubmit`
2. **Pass function references** - Not function calls
3. **Event object is synthetic** - Wrapper around native events
4. **onChange for inputs** - Most common for controlled components
5. **onClick for interactions** - Buttons, clicks, selections
6. **onKeyDown for keyboard** - Shortcuts, accessibility
7. **preventDefault() prevents defaults** - Form submission, links
8. **stopPropagation() stops bubbling** - Prevents parent handlers

---

## Practice Exercises

1. **Click Counter** - Increment on click
2. **Search Box** - Filter on onChange, submit on Enter
3. **Hover Card** - Show details on mouse enter/leave
4. **Form with Validation** - Validate on blur
5. **Keyboard Navigation** - Arrow keys to move element
6. **Todo List** - Add on submit, toggle on checkbox change
7. **Modal** - Close on Escape key or outside click
8. **Drag and Drop** - Reorder list items

---

## Next Steps

- Learn about event delegation in React
- Explore custom hooks for common event patterns
- Study performance optimization with event handlers
- Understand synthetic events in depth