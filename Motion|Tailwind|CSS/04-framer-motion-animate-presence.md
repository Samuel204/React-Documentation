# Framer Motion - AnimatePresence

## What is AnimatePresence?

**AnimatePresence** is a component that enables exit animations in React. It solves a fundamental problem: by default, when a React component unmounts, it disappears instantly from the screen, making exit animations impossible.

### The Core Problem

```jsx
// ❌ WITHOUT AnimatePresence - element disappears instantly
{isVisible && (
  <motion.div 
    initial={{ opacity: 0 }}
    animate={{ opacity: 1 }}
    exit={{ opacity: 0 }}  // This exit animation NEVER runs!
  >
    Content
  </motion.div>
)}
```

When `isVisible` becomes `false`, React immediately removes the component from the DOM. The `exit` animation never has a chance to play.

### The Solution

```jsx
// ✅ WITH AnimatePresence - exit animation plays before removal
<AnimatePresence>
  {isVisible && (
    <motion.div 
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}  // Now this works!
    >
      Content
    </motion.div>
  )}
</AnimatePresence>
```

AnimatePresence keeps the component in the DOM long enough for the exit animation to complete, then removes it.

---

## How AnimatePresence Works

### The Lifecycle

1. **Component mounts** → `initial` state applied
2. **Component enters** → Animates from `initial` to `animate`
3. **Component stays** → Remains in `animate` state
4. **Component unmounts** → Animates from current state to `exit`
5. **After exit animation** → Component removed from DOM

### Visual Timeline

```
Mount:    initial={opacity: 0}  ━━━━━━━━━━►  animate={opacity: 1}
                                  (fade in)

Unmount:  current={opacity: 1}  ━━━━━━━━━━►  exit={opacity: 0}
                                  (fade out)
                                              ↓
                                         Removed from DOM
```

---

## Basic Usage Examples

### Example 1: Simple Toggle
```jsx
function ToggleExample() {
  const [isVisible, setIsVisible] = useState(true);
  
  return (
    <>
      <button onClick={() => setIsVisible(!isVisible)}>
        Toggle
      </button>
      
      <AnimatePresence>
        {isVisible && (
          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
          >
            I fade in and out!
          </motion.div>
        )}
      </AnimatePresence>
    </>
  );
}
```

### Example 2: Slide Out
```jsx
<AnimatePresence>
  {showModal && (
    <motion.div
      className="modal"
      initial={{ x: 300, opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      exit={{ x: -300, opacity: 0 }}
    >
      Modal content
    </motion.div>
  )}
</AnimatePresence>
```

### Example 3: Scale and Fade
```jsx
<AnimatePresence>
  {showNotification && (
    <motion.div
      className="notification"
      initial={{ scale: 0.8, opacity: 0 }}
      animate={{ scale: 1, opacity: 1 }}
      exit={{ scale: 0.8, opacity: 0 }}
      transition={{ duration: 0.2 }}
    >
      Notification message
    </motion.div>
  )}
</AnimatePresence>
```

---

## The `mode` Prop - Critical for Smooth Transitions

AnimatePresence has three modes that control how enter/exit animations interact:

### Mode 1: `sync` (Default)

Elements enter and exit **simultaneously**.

```jsx
<AnimatePresence mode="sync">
  {step === 1 && <StepOne />}
  {step === 2 && <StepTwo />}
</AnimatePresence>
```

**Behavior:**
- StepOne starts exiting
- StepTwo starts entering **at the same time**
- Both animations happen together

**Result:** Elements overlap during transition (can look messy)

**Use when:** You want fast transitions and don't mind overlap

### Mode 2: `wait` ✅ **RECOMMENDED**

New element **waits** for the old element to finish exiting.

```jsx
<AnimatePresence mode="wait">
  {step === 1 && <StepOne />}
  {step === 2 && <StepTwo />}
</AnimatePresence>
```

**Behavior:**
1. StepOne starts exiting
2. StepOne finishes exiting and is removed
3. **Then** StepTwo starts entering

**Result:** Clean transitions with no overlap

**Use when:** 
- Page transitions
- Multi-step forms
- Any time overlap would look bad

### Mode 3: `popLayout`

Exiting element is removed from layout immediately, siblings reflow smoothly.

```jsx
<AnimatePresence mode="popLayout">
  {items.map(item => (
    <motion.div key={item.id} exit={{ opacity: 0 }}>
      {item.name}
    </motion.div>
  ))}
</AnimatePresence>
```

**Behavior:**
- Element starts exit animation
- Immediately "popped" out of document flow
- Siblings smoothly reposition
- Element continues fading out

**Use when:** 
- Lists where items are removed
- Grids where gaps should close smoothly

---

## Visual Comparison of Modes

### Sync Mode
```
Timeline:
0s ──────────────────► 0.5s
Old: [████████████]  (exiting)
New:     [████████████]  (entering)
     ↑
     Overlap!
```

### Wait Mode ✅
```
Timeline:
0s ────► 0.5s ────► 1s
Old: [██████]  (exiting)
New:           [██████]  (entering)
              ↑
              Wait!
```

### PopLayout Mode
```
Timeline:
0s ──────────────────► 0.5s
Item: [fade out...]
Siblings: [smoothly move up]
```

---

## React Router Integration

AnimatePresence is perfect for animating page transitions in React Router.

### Complete Example with React Router

```jsx
import { Routes, Route, useLocation } from 'react-router-dom';
import { AnimatePresence } from 'framer-motion';

function App() {
  const location = useLocation();
  
  return (
    <AnimatePresence mode="wait">
      <Routes location={location} key={location.pathname}>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </AnimatePresence>
  );
}
```

**Critical parts explained:**

1. **`useLocation()`** - Gets current route information
2. **`key={location.pathname}`** - Tells React when route changed
3. **`mode="wait"`** - Prevents pages from overlapping
4. **`location={location}`** - Passes location to Routes

### Page Component with Animations

```jsx
function Home() {
  return (
    <motion.div
      initial={{ opacity: 0, x: -100 }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: 100 }}
      transition={{ duration: 0.3 }}
    >
      <h1>Home Page</h1>
      <p>Welcome!</p>
    </motion.div>
  );
}
```

### Common Page Transition Patterns

#### Pattern 1: Fade
```jsx
const pageVariants = {
  initial: { opacity: 0 },
  animate: { opacity: 1 },
  exit: { opacity: 0 }
};

function Page() {
  return (
    <motion.div variants={pageVariants} initial="initial" animate="animate" exit="exit">
      Page content
    </motion.div>
  );
}
```

#### Pattern 2: Slide Left/Right
```jsx
const pageVariants = {
  initial: { x: 300, opacity: 0 },
  animate: { x: 0, opacity: 1 },
  exit: { x: -300, opacity: 0 }
};
```

#### Pattern 3: Scale and Fade
```jsx
const pageVariants = {
  initial: { scale: 0.9, opacity: 0 },
  animate: { scale: 1, opacity: 1 },
  exit: { scale: 1.1, opacity: 0 }
};
```

#### Pattern 4: Vertical Slide
```jsx
const pageVariants = {
  initial: { y: 50, opacity: 0 },
  animate: { y: 0, opacity: 1 },
  exit: { y: -50, opacity: 0 }
};
```

---

## The Importance of `key`

The `key` prop is **essential** for AnimatePresence to work correctly.

### Why Key Matters

```jsx
// ❌ WITHOUT key - AnimatePresence doesn't know component changed
<AnimatePresence>
  {currentStep === 1 ? <StepOne /> : <StepTwo />}
</AnimatePresence>
```

React sees this as the same position in the tree → No exit animation!

```jsx
// ✅ WITH key - AnimatePresence knows these are different components
<AnimatePresence mode="wait">
  {currentStep === 1 && <StepOne key="step1" />}
  {currentStep === 2 && <StepTwo key="step2" />}
</AnimatePresence>
```

Now React knows the component changed → Exit animation triggers!

### Key Strategies

```jsx
// For routes
key={location.pathname}

// For steps/tabs
key={currentStep}

// For dynamic content
key={item.id}

// For boolean toggles
key={isVisible ? "visible" : "hidden"}
```

---

## Real-World Examples

### Example 1: Multi-Step Form

```jsx
function MultiStepForm() {
  const [step, setStep] = useState(1);
  
  return (
    <div>
      <AnimatePresence mode="wait">
        {step === 1 && (
          <motion.div
            key="step1"
            initial={{ x: 300, opacity: 0 }}
            animate={{ x: 0, opacity: 1 }}
            exit={{ x: -300, opacity: 0 }}
          >
            <h2>Step 1: Personal Info</h2>
            {/* Form fields */}
          </motion.div>
        )}
        
        {step === 2 && (
          <motion.div
            key="step2"
            initial={{ x: 300, opacity: 0 }}
            animate={{ x: 0, opacity: 1 }}
            exit={{ x: -300, opacity: 0 }}
          >
            <h2>Step 2: Address</h2>
            {/* Form fields */}
          </motion.div>
        )}
      </AnimatePresence>
      
      <button onClick={() => setStep(step + 1)}>Next</button>
    </div>
  );
}
```

### Example 2: Modal with Backdrop

```jsx
function Modal({ isOpen, onClose, children }) {
  return (
    <AnimatePresence>
      {isOpen && (
        <>
          {/* Backdrop */}
          <motion.div
            className="backdrop"
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            onClick={onClose}
          />
          
          {/* Modal */}
          <motion.div
            className="modal"
            initial={{ scale: 0.8, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            exit={{ scale: 0.8, opacity: 0 }}
          >
            {children}
          </motion.div>
        </>
      )}
    </AnimatePresence>
  );
}
```

### Example 3: Notification System

```jsx
function NotificationList({ notifications }) {
  return (
    <div className="notification-container">
      <AnimatePresence mode="popLayout">
        {notifications.map(notification => (
          <motion.div
            key={notification.id}
            layout
            initial={{ x: 300, opacity: 0 }}
            animate={{ x: 0, opacity: 1 }}
            exit={{ x: 300, opacity: 0 }}
            className="notification"
          >
            {notification.message}
          </motion.div>
        ))}
      </AnimatePresence>
    </div>
  );
}
```

### Example 4: Tab Navigation

```jsx
function Tabs() {
  const [activeTab, setActiveTab] = useState('home');
  
  const tabs = {
    home: <HomeContent />,
    profile: <ProfileContent />,
    settings: <SettingsContent />
  };
  
  return (
    <div>
      {/* Tab buttons */}
      <div className="tab-buttons">
        {Object.keys(tabs).map(tab => (
          <button 
            key={tab}
            onClick={() => setActiveTab(tab)}
            className={activeTab === tab ? 'active' : ''}
          >
            {tab}
          </button>
        ))}
      </div>
      
      {/* Tab content with animations */}
      <AnimatePresence mode="wait">
        <motion.div
          key={activeTab}
          initial={{ y: 20, opacity: 0 }}
          animate={{ y: 0, opacity: 1 }}
          exit={{ y: -20, opacity: 0 }}
          transition={{ duration: 0.2 }}
        >
          {tabs[activeTab]}
        </motion.div>
      </AnimatePresence>
    </div>
  );
}
```

---

## Advanced Features

### Feature 1: Custom Exit Timing

```jsx
<AnimatePresence>
  {isVisible && (
    <motion.div
      exit={{ 
        opacity: 0, 
        transition: { duration: 1.5 }  // Slow exit
      }}
    >
      Slow fade out
    </motion.div>
  )}
</AnimatePresence>
```

### Feature 2: onExitComplete Callback

```jsx
<AnimatePresence onExitComplete={() => console.log("Exit done!")}>
  {isVisible && <motion.div exit={{ opacity: 0 }}>Content</motion.div>}
</AnimatePresence>
```

### Feature 3: Initial={false} to Skip Initial Animation

```jsx
<AnimatePresence initial={false}>
  {/* First render won't animate, but subsequent will */}
  {isVisible && <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
    Content
  </motion.div>}
</AnimatePresence>
```

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Forgetting AnimatePresence
```jsx
// WRONG - exit won't work
{isVisible && (
  <motion.div exit={{ opacity: 0 }}>Content</motion.div>
)}

// CORRECT
<AnimatePresence>
  {isVisible && (
    <motion.div exit={{ opacity: 0 }}>Content</motion.div>
  )}
</AnimatePresence>
```

### ❌ Mistake 2: Missing Key
```jsx
// WRONG - React can't tell components changed
<AnimatePresence>
  {condition ? <ComponentA /> : <ComponentB />}
</AnimatePresence>

// CORRECT
<AnimatePresence mode="wait">
  {condition ? <ComponentA key="a" /> : <ComponentB key="b" />}
</AnimatePresence>
```

### ❌ Mistake 3: Not Using `mode="wait"` for Pages
```jsx
// WRONG - pages overlap during transition
<AnimatePresence>
  <Routes location={location} key={location.pathname}>
    ...
  </Routes>
</AnimatePresence>

// CORRECT
<AnimatePresence mode="wait">
  <Routes location={location} key={location.pathname}>
    ...
  </Routes>
</AnimatePresence>
```

### ❌ Mistake 4: Multiple Direct Children Without Keys
```jsx
// WRONG - AnimatePresence can't track which is which
<AnimatePresence>
  {showA && <ComponentA />}
  {showB && <ComponentB />}
</AnimatePresence>

// CORRECT - each has unique key
<AnimatePresence>
  {showA && <ComponentA key="a" />}
  {showB && <ComponentB key="b" />}
</AnimatePresence>
```

---

## Best Practices

### 1. **Always Use Keys for Dynamic Content**
```jsx
<AnimatePresence>
  {items.map(item => (
    <motion.div key={item.id} exit={{ opacity: 0 }}>
      {item.name}
    </motion.div>
  ))}
</AnimatePresence>
```

### 2. **Use `mode="wait"` for Single-Item Transitions**
```jsx
// Good for: page transitions, step forms, modals
<AnimatePresence mode="wait">
  {currentView}
</AnimatePresence>
```

### 3. **Use `mode="popLayout"` for Lists**
```jsx
// Good for: todo lists, notifications, removed items
<AnimatePresence mode="popLayout">
  {list.map(item => <Item key={item.id} />)}
</AnimatePresence>
```

### 4. **Keep Exit Animations Short**
```jsx
// ✅ Good - quick and responsive
exit={{ opacity: 0, transition: { duration: 0.2 } }}

// ❌ Bad - too slow, frustrating
exit={{ opacity: 0, transition: { duration: 2 } }}
```

### 5. **Match Enter and Exit Timing**
```jsx
const variants = {
  initial: { opacity: 0, x: -100 },
  animate: { 
    opacity: 1, 
    x: 0,
    transition: { duration: 0.3 }
  },
  exit: { 
    opacity: 0, 
    x: 100,
    transition: { duration: 0.3 }  // Same duration!
  }
};
```

---

## Performance Tips

### 1. Avoid Animating Expensive Properties
```jsx
// ❌ Slow - causes layout recalculation
exit={{ width: 0, height: 0 }}

// ✅ Fast - GPU accelerated
exit={{ opacity: 0, scale: 0 }}
```

### 2. Use `initial={false}` for Server-Side Rendering
```jsx
<AnimatePresence initial={false}>
  {/* Won't animate on first render */}
</AnimatePresence>
```

### 3. Limit Simultaneous Animations
```jsx
// Stagger list exits for better performance
const list = {
  exit: { 
    transition: { staggerChildren: 0.05, staggerDirection: -1 }
  }
};
```

---

## Practice Exercises

### Exercise 1: Create a Dismissible Alert
```jsx
// Requirements:
// - Alert slides in from top
// - Click X to dismiss
// - Slides out to top when dismissed
// - Use AnimatePresence
```

### Exercise 2: Build Page Transitions
```jsx
// Requirements:
// - 3 pages with React Router
// - Pages fade and slide
// - Use mode="wait"
// - Smooth transitions
```

### Exercise 3: Todo List with Animations
```jsx
// Requirements:
// - Items fade in when added
// - Items slide out when deleted
// - Remaining items smoothly reposition
// - Use mode="popLayout"
```

---

## Key Takeaways

1. **AnimatePresence enables exit animations** - Without it, exit animations won't work
2. **Wrap conditional content** - Must wrap the element that appears/disappears
3. **`mode` controls timing** - `sync` (overlap), `wait` (sequential), `popLayout` (reflow)
4. **`mode="wait"` for pages** - Prevents overlap in transitions
5. **Keys are essential** - Tells React when components change
6. **`useLocation()` for routing** - Required for React Router integration
7. **Keep exits short** - 0.2-0.4s for responsive feel

---

## Next Steps

Now that you understand AnimatePresence, explore:
- **Layout Animations** - Shared element transitions
- **LayoutGroup** - Coordinating layout changes
- **Reordering** - Drag-to-reorder lists with AnimatePresence
- **Presence Context** - Accessing presence state in child components
- **Custom Transitions** - Different enter/exit animations

AnimatePresence brings your UI to life with smooth enter and exit animations! 🎭✨
