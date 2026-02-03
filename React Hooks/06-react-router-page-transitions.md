# React Router + Framer Motion - Page Transitions

## Table of Contents
1. [React Router Fundamentals](#react-router-fundamentals)
2. [Framer Motion Page Transitions](#framer-motion-page-transitions)
3. [Complete Integration](#complete-integration)

---

# Part 1: React Router Fundamentals

## What is React Router?

**React Router** is the standard library for handling navigation in React applications. It allows you to create Single Page Applications (SPAs) where different "pages" are actually different components that render based on the URL.

### Why Do We Need It?

Without React Router:
```jsx
// ❌ Manual, messy navigation
function App() {
  const [page, setPage] = useState('home');
  
  if (page === 'home') return <HomePage />;
  if (page === 'about') return <AboutPage />;
  if (page === 'contact') return <ContactPage />;
}
```

**Problems:**
- No browser history (back/forward buttons don't work)
- No bookmarkable URLs
- Manual state management
- No URL parameters

With React Router:
```jsx
// ✅ Clean, standard navigation
<Routes>
  <Route path="/" element={<HomePage />} />
  <Route path="/about" element={<AboutPage />} />
  <Route path="/contact" element={<ContactPage />} />
</Routes>
```

**Benefits:**
- Browser history works automatically
- URLs are bookmarkable and shareable
- Declarative routing
- Built-in features (nested routes, parameters, etc.)

---

## Core React Router Concepts

### 1. BrowserRouter

**What it does:** Wraps your entire app and enables routing functionality.

```jsx
import { BrowserRouter } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      {/* Your app content */}
    </BrowserRouter>
  );
}
```

**Think of it as:** The "power switch" that turns on routing for your entire application.

**Important:** Place it at the TOP of your component tree, wrapping everything.

### 2. Routes and Route

**What they do:** Define which component to render for each URL path.

```jsx
import { Routes, Route } from 'react-router-dom';

<Routes>
  <Route path="/" element={<HomePage />} />
  <Route path="/about" element={<AboutPage />} />
  <Route path="/contact" element={<ContactPage />} />
</Routes>
```

**Breakdown:**
- `<Routes>` - Container for all your routes
- `<Route>` - Individual route definition
- `path` - The URL pattern to match (e.g., "/about")
- `element` - The component to render when path matches

**How it works:**
```
User visits "/about"
    ↓
React Router checks all Routes
    ↓
Finds: <Route path="/about" element={<AboutPage />} />
    ↓
Renders: <AboutPage />
```

### 3. Link

**What it does:** Creates clickable navigation links (replaces `<a>` tags).

```jsx
import { Link } from 'react-router-dom';

<nav>
  <Link to="/">Home</Link>
  <Link to="/about">About</Link>
  <Link to="/contact">Contact</Link>
</nav>
```

**Why use Link instead of `<a>`?**
- `<a href="/about">` - Full page reload (slow, loses state)
- `<Link to="/about">` - Instant navigation (fast, keeps state)

**Visual difference:**
```
<a> tag:
Click → Full page reload → New HTTP request → Everything reloads
         [████████████] (slow)

<Link>:
Click → Component swap → Instant
         [█] (fast)
```

### 4. useLocation Hook

**What it does:** Provides information about the current URL location.

```jsx
import { useLocation } from 'react-router-dom';

function MyComponent() {
  const location = useLocation();
  
  console.log(location.pathname);  // "/about"
  console.log(location.search);    // "?id=123"
  console.log(location.hash);      // "#section"
  
  return <div>Current path: {location.pathname}</div>;
}
```

**The location object contains:**
```javascript
{
  pathname: "/about",        // Current path
  search: "?id=123",        // Query string
  hash: "#section",         // Hash fragment
  state: { ... },           // Custom state
  key: "ac3df4"            // Unique key
}
```

**Critical for animations:** The `pathname` tells us which page we're on!

---

## React Router - Common Patterns

### Pattern 1: Navigation Bar

```jsx
function NavBar() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/products">Products</Link>
      <Link to="/about">About</Link>
    </nav>
  );
}
```

### Pattern 2: Dynamic Routes

```jsx
<Routes>
  <Route path="/user/:id" element={<UserProfile />} />
</Routes>

// Access the parameter
function UserProfile() {
  const { id } = useParams();
  return <div>User ID: {id}</div>;
}

// Visit: /user/123 → Shows "User ID: 123"
```

### Pattern 3: Nested Routes

```jsx
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    <Route path="stats" element={<Stats />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>

// Visit: /dashboard/stats → Shows Dashboard with Stats inside
```

### Pattern 4: Programmatic Navigation

```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  
  const handleLogin = () => {
    // After successful login
    navigate('/dashboard');
  };
  
  return <button onClick={handleLogin}>Login</button>;
}
```

---

# Part 2: Framer Motion Page Transitions

## The Problem with Page Changes

When you navigate between routes in React Router, the page changes **instantly**:

```jsx
// Click "About" link
<HomePage />  ━━━━━━━━━━━━━━━━━►  <AboutPage />
             (instant, no animation)
```

This feels abrupt and lacks polish.

## The Solution: Animate the Transition

We want smooth transitions:

```jsx
// Click "About" link
<HomePage />  ━━━━ fade/slide ━━━━►  <AboutPage />
             (smooth, professional)
```

---

## Key Components for Page Transitions

### 1. AnimatePresence (Required!)

**Why it's needed:** 
Without it, when you change routes:
1. Old page component unmounts immediately
2. New page component mounts immediately
3. No time for exit animation!

With AnimatePresence:
1. Old page stays mounted
2. Exit animation plays
3. Old page unmounts
4. New page mounts with enter animation

```jsx
<AnimatePresence mode="wait">
  <Routes location={location} key={location.pathname}>
    {/* Your routes */}
  </Routes>
</AnimatePresence>
```

### 2. The `mode="wait"` Prop

Controls how transitions overlap:

**Without `mode="wait"`:**
```
Timeline:
0s ──────────────────► 0.5s
Old Page: [fadeout...]
New Page:     [fadein...]
          ↑
          Overlap! (looks messy)
```

**With `mode="wait"`:**
```
Timeline:
0s ────► 0.35s ────► 0.7s
Old Page: [fadeout]
New Page:           [fadein]
                    ↑
                    Wait!
```

**Result:** Clean transitions with no visual overlap.

### 3. The `location` and `key` Props

**Critical for detecting route changes!**

```jsx
const location = useLocation();

<Routes location={location} key={location.pathname}>
```

**Why both are needed:**

**`location={location}`:**
- Tells Routes which URL we're at
- Needed for AnimatePresence to work

**`key={location.pathname}`:**
- Tells React when the route actually changed
- When key changes, React knows to unmount old and mount new
- Without it, React thinks it's the same component!

**Example:**
```
Navigate from "/" to "/about"
    ↓
location.pathname changes: "/" → "/about"
    ↓
key changes → React unmounts old, mounts new
    ↓
AnimatePresence detects change → Triggers animations!
```

---

# Part 3: Complete Integration

## Understanding Your Code Architecture

Your code has a smart, modular structure. Let's break it down:

```
App (BrowserRouter wrapper)
  ├── NavBar (navigation links)
  └── AnimateRoutes (handles routing + animations)
       └── AnimatePresence
            └── Routes
                 ├── Route → PageTransition → HomePage
                 ├── Route → PageTransition → AboutPage
                 └── Route → PageTransition → ContactPage
```

### Component 1: App (Main Container)

```jsx
function App() {
  return (
    <BrowserRouter>
      <div className="app">
        <nav className="nav">
          <div className="nav-links">
            <Link to="/">Home</Link>
            <Link to="/about">About</Link>
            <Link to="/contact">Contact</Link>
          </div>
        </nav>
        <AnimateRoutes/>
      </div>
    </BrowserRouter>
  );
}
```

**Responsibilities:**
- Wraps everything in `BrowserRouter` to enable routing
- Renders navigation (always visible, doesn't change)
- Renders `AnimateRoutes` (content that changes)

**Why the structure?**
- Navigation stays fixed (no re-render)
- Only the content area animates
- Clean separation of concerns

### Component 2: AnimateRoutes (Animation Logic)

```jsx
function AnimateRoutes() {
  const location = useLocation();

  return(
    <AnimatePresence mode="wait">
      <Routes location={location} key={location.pathname}>
        <Route path="/" element={
          <PageTransition>
            <HomePage/>
          </PageTransition>
        }/>
        {/* More routes... */}
      </Routes>
    </AnimatePresence>
  )
}
```

**Responsibilities:**
- Gets current location with `useLocation()`
- Wraps Routes in AnimatePresence for exit animations
- Sets `mode="wait"` for clean transitions
- Passes `location` and `key` to Routes

**Why separate component?**
- Can't call `useLocation()` above `BrowserRouter`
- Need to be inside Router context to use hooks
- Keeps App component clean

**The Flow:**
```
1. User clicks Link → URL changes
2. useLocation() detects change
3. key={location.pathname} changes
4. AnimatePresence knows component changed
5. Old page exit animation plays
6. mode="wait" prevents new page from starting
7. Old page finishes, unmounts
8. New page mounts, enter animation plays
```

### Component 3: PageTransition (Animation Wrapper)

```jsx
function PageTransition({ children }) {
  return (
    <motion.main 
      className="page" 
      initial={{ opacity: 0, x: 50 }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: -50 }}
      transition={{ duration: 0.35, ease: "easeOut" }}
    >
      {children}
    </motion.main>
  )
}
```

**Responsibilities:**
- Wraps each page component
- Defines enter animation (`initial` → `animate`)
- Defines exit animation (`exit`)
- Sets timing and easing

**The Animation States:**

**Initial (before visible):**
```jsx
{ opacity: 0, x: 50 }
```
- Invisible
- 50px to the right
- Waiting off-screen

**Animate (visible):**
```jsx
{ opacity: 1, x: 0 }
```
- Fully visible
- At natural position
- On-screen

**Exit (leaving):**
```jsx
{ opacity: 0, x: -50 }
```
- Fading out
- Moving 50px to the left
- Sliding off-screen

**Timing:**
```jsx
{ duration: 0.35, ease: "easeOut" }
```
- Takes 0.35 seconds
- `easeOut` = fast start, slow end (natural feel)

**Visual Timeline:**
```
0s          0.35s       0.7s
Old: [━━━━━━━━]
     fade out
     slide left
     
New:          [━━━━━━━━]
              fade in
              slide from right
```

**Why `children` prop?**
- Reusable wrapper for ANY page
- Don't repeat animation code
- DRY principle (Don't Repeat Yourself)

### Component 4: Page Components

```jsx
function HomePage() {
  return (
    <>
      <h2>Home</h2>
      <p>Welcome</p>
    </>
  )
}

function AboutPage() {
  return (
    <>
      <h2>About</h2>
      <p>Welcome</p>
    </>
  )
}

function ContactPage() {
  return (
    <>
      <h2>Contact</h2>
      <p>Welcome</p>
    </>
  )
}
```

**Responsibilities:**
- Just render content
- No animation logic
- Keep it simple

**Why wrapped in PageTransition?**
- PageTransition handles ALL animation
- Page components stay clean
- Easy to add new pages

---

## The Complete Flow - Step by Step

Let's trace what happens when you click a link:

### Step 1: User Clicks Link

```jsx
<Link to="/about">About</Link>
```

**What happens:**
- React Router intercepts the click
- URL changes to "/about"
- No page reload (SPA magic!)

### Step 2: useLocation Detects Change

```jsx
const location = useLocation();
// location.pathname changed: "/" → "/about"
```

### Step 3: Key Changes, React Unmounts Old Component

```jsx
<Routes location={location} key={location.pathname}>
// key changed: "/" → "/about"
// React knows: "This is a different component!"
```

### Step 4: AnimatePresence Triggers Exit Animation

```jsx
<AnimatePresence mode="wait">
```

**AnimatePresence says:**
"Wait! Don't remove old component yet. Let it animate out first."

Old component starts exit animation:
```jsx
exit={{ opacity: 0, x: -50 }}
// HomePage fades out and slides left
```

### Step 5: Old Page Finishes, Unmounts

After 0.35s:
- HomePage exit animation completes
- AnimatePresence removes it from DOM

### Step 6: New Page Mounts with Enter Animation

```jsx
initial={{ opacity: 0, x: 50 }}
animate={{ opacity: 1, x: 0 }}
// AboutPage starts invisible, right
// Animates to visible, centered
```

### Step 7: Transition Complete

User sees:
- Smooth transition
- Professional feel
- No jarring jumps

---

## Animation Customization

### Different Animation Styles

#### Style 1: Simple Fade (Your Code)

```jsx
initial={{ opacity: 0, x: 50 }}
animate={{ opacity: 1, x: 0 }}
exit={{ opacity: 0, x: -50 }}
```

**Effect:** Fade + horizontal slide

#### Style 2: Vertical Slide

```jsx
initial={{ opacity: 0, y: 50 }}
animate={{ opacity: 1, y: 0 }}
exit={{ opacity: 0, y: -50 }}
```

**Effect:** Fade + vertical slide (pages from bottom)

#### Style 3: Scale + Fade

```jsx
initial={{ opacity: 0, scale: 0.9 }}
animate={{ opacity: 1, scale: 1 }}
exit={{ opacity: 0, scale: 1.1 }}
```

**Effect:** Zooms in/out while fading

#### Style 4: Rotate + Slide

```jsx
initial={{ opacity: 0, x: 100, rotate: 10 }}
animate={{ opacity: 1, x: 0, rotate: 0 }}
exit={{ opacity: 0, x: -100, rotate: -10 }}
```

**Effect:** Playful rotation while sliding

#### Style 5: Only Fade (Subtle)

```jsx
initial={{ opacity: 0 }}
animate={{ opacity: 1 }}
exit={{ opacity: 0 }}
```

**Effect:** Clean, minimal crossfade

### Timing Variations

#### Fast and Snappy

```jsx
transition={{ duration: 0.2, ease: "easeOut" }}
```

**Feel:** Quick, responsive (good for apps)

#### Smooth and Professional (Your Code)

```jsx
transition={{ duration: 0.35, ease: "easeOut" }}
```

**Feel:** Balanced (good for most sites)

#### Slow and Dramatic

```jsx
transition={{ duration: 0.6, ease: "easeInOut" }}
```

**Feel:** Luxurious (good for portfolios)

#### Custom Bezier Curve

```jsx
transition={{ 
  duration: 0.4, 
  ease: [0.43, 0.13, 0.23, 0.96]  // Custom cubic-bezier
}}
```

**Feel:** Unique, branded motion

---

## Advanced Patterns

### Pattern 1: Different Animations Per Page

```jsx
function PageTransition({ children, variant = "default" }) {
  const variants = {
    default: {
      initial: { opacity: 0, x: 50 },
      animate: { opacity: 1, x: 0 },
      exit: { opacity: 0, x: -50 }
    },
    scale: {
      initial: { opacity: 0, scale: 0.8 },
      animate: { opacity: 1, scale: 1 },
      exit: { opacity: 0, scale: 1.2 }
    }
  };
  
  return (
    <motion.main
      initial={variants[variant].initial}
      animate={variants[variant].animate}
      exit={variants[variant].exit}
    >
      {children}
    </motion.main>
  );
}

// Usage:
<Route path="/" element={
  <PageTransition variant="default">
    <HomePage />
  </PageTransition>
} />

<Route path="/gallery" element={
  <PageTransition variant="scale">
    <GalleryPage />
  </PageTransition>
} />
```

### Pattern 2: Direction-Based Transitions

```jsx
function PageTransition({ children }) {
  const location = useLocation();
  const [direction, setDirection] = useState(1);
  
  // Determine direction based on route index
  useEffect(() => {
    const routes = ['/', '/about', '/contact'];
    const currentIndex = routes.indexOf(location.pathname);
    const prevIndex = routes.indexOf(prevLocation);
    
    setDirection(currentIndex > prevIndex ? 1 : -1);
  }, [location]);
  
  return (
    <motion.main
      initial={{ opacity: 0, x: 100 * direction }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: -100 * direction }}
    >
      {children}
    </motion.main>
  );
}
```

**Effect:** Forward slides right, back slides left!

### Pattern 3: Shared Element Transitions

```jsx
function PageTransition({ children }) {
  return (
    <motion.main
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    >
      <motion.div layoutId="header">
        {/* This element morphs between pages! */}
      </motion.div>
      {children}
    </motion.main>
  );
}
```

### Pattern 4: Staggered Page Content

```jsx
function PageTransition({ children }) {
  return (
    <motion.main
      initial="initial"
      animate="animate"
      exit="exit"
      variants={{
        initial: { opacity: 0 },
        animate: { 
          opacity: 1,
          transition: { staggerChildren: 0.1 }
        },
        exit: { opacity: 0 }
      }}
    >
      {children}
    </motion.main>
  );
}

// In page component:
function HomePage() {
  return (
    <>
      <motion.h1 variants={childVariant}>Title</motion.h1>
      <motion.p variants={childVariant}>Paragraph 1</motion.p>
      <motion.p variants={childVariant}>Paragraph 2</motion.p>
    </>
  );
}
```

---

## Common Issues and Solutions

### Issue 1: Animations Don't Work

**Symptoms:** Pages change instantly, no animation

**Possible causes:**

1. **Missing AnimatePresence**
```jsx
// ❌ Wrong
<Routes location={location} key={location.pathname}>

// ✅ Correct
<AnimatePresence mode="wait">
  <Routes location={location} key={location.pathname}>
</AnimatePresence>
```

2. **Missing key**
```jsx
// ❌ Wrong
<Routes location={location}>

// ✅ Correct
<Routes location={location} key={location.pathname}>
```

3. **useLocation() in wrong place**
```jsx
// ❌ Wrong - outside BrowserRouter
function App() {
  const location = useLocation(); // Error!
  return <BrowserRouter>...</BrowserRouter>
}

// ✅ Correct - inside Router context
function AnimateRoutes() {
  const location = useLocation(); // Works!
  return <Routes>...</Routes>
}
```

### Issue 2: Pages Overlap

**Symptoms:** Old and new page visible at same time

**Solution:** Add `mode="wait"`
```jsx
<AnimatePresence mode="wait">
```

### Issue 3: Navigation Flickers

**Symptoms:** Brief flash between transitions

**Solutions:**

1. **Ensure both pages have same background**
```css
.page {
  background: white;
}
```

2. **Use position: absolute**
```jsx
<motion.main
  style={{ position: "absolute", width: "100%" }}
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  exit={{ opacity: 0 }}
>
```

### Issue 4: Scroll Position Not Resetting

**Symptoms:** New page starts scrolled down

**Solution:** Scroll to top on route change
```jsx
function ScrollToTop() {
  const location = useLocation();
  
  useEffect(() => {
    window.scrollTo(0, 0);
  }, [location]);
  
  return null;
}

// Add to App:
<BrowserRouter>
  <ScrollToTop />
  <AnimateRoutes />
</BrowserRouter>
```

---

## Best Practices

### 1. Keep Transitions Short

```jsx
// ✅ Good - responsive
transition={{ duration: 0.3 }}

// ❌ Bad - feels sluggish
transition={{ duration: 1.5 }}
```

### 2. Use Consistent Easing

```jsx
// Choose one and stick with it across all pages
transition={{ ease: "easeOut" }}
```

### 3. Match Enter and Exit Timing

```jsx
// ✅ Good - symmetrical
initial={{ opacity: 0, x: 50 }}
animate={{ opacity: 1, x: 0 }}
exit={{ opacity: 0, x: -50 }}
transition={{ duration: 0.35 }}  // Same for both!

// ❌ Bad - asymmetrical feels weird
initial={{ opacity: 0 }}
animate={{ opacity: 1, transition: { duration: 0.2 } }}
exit={{ opacity: 0, transition: { duration: 1 } }}
```

### 4. Avoid Animating Width/Height

```jsx
// ❌ Slow - causes layout recalculation
initial={{ width: 0 }}
animate={{ width: "100%" }}

// ✅ Fast - GPU accelerated
initial={{ opacity: 0, x: -100 }}
animate={{ opacity: 1, x: 0 }}
```

### 5. Test on Slow Devices

```jsx
// Use Chrome DevTools > Performance > CPU throttling
// Make sure transitions are smooth even on slower devices
```

---

## Performance Optimization

### 1. Lazy Load Pages

```jsx
import { lazy, Suspense } from 'react';

const HomePage = lazy(() => import('./pages/HomePage'));
const AboutPage = lazy(() => import('./pages/AboutPage'));

function AnimateRoutes() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <AnimatePresence mode="wait">
        <Routes location={location} key={location.pathname}>
          <Route path="/" element={
            <PageTransition><HomePage /></PageTransition>
          } />
        </Routes>
      </AnimatePresence>
    </Suspense>
  );
}
```

### 2. Reduce Animation Complexity

```jsx
// If performance is poor, simplify:
// ✅ Simple fade (fast)
initial={{ opacity: 0 }}
animate={{ opacity: 1 }}

// ❌ Complex multi-property (slower)
initial={{ opacity: 0, x: 50, scale: 0.8, rotate: 10 }}
```

### 3. Use CSS Transform Properties

```jsx
// ✅ GPU accelerated
animate={{ x: 0, y: 0, scale: 1, rotate: 0 }}

// ❌ Forces layout recalc
animate={{ left: 0, top: 0, width: 100 }}
```

---

## Practice Exercises

### Exercise 1: Add More Pages

```jsx
// Add these pages to your app:
// - /services (slides from bottom)
// - /blog (fades in)
// - /portfolio (scales up)
// Each with different animation!
```

### Exercise 2: Add Loading State

```jsx
// Show a loading spinner while page transitions
// Hint: Use onAnimationComplete callback
```

### Exercise 3: Build a Multi-Step Form

```jsx
// Create a form with multiple steps
// Each step is a "page" with transitions
// Add Next/Previous buttons
```

### Exercise 4: Add Route Parameters

```jsx
// Create a blog with individual post pages
// Route: /blog/:postId
// Animate between post pages
```

---

## Key Takeaways

### React Router
1. **BrowserRouter** - Wraps app, enables routing
2. **Routes & Route** - Define URL → Component mappings
3. **Link** - Navigate without page reload
4. **useLocation** - Get current URL information

### Framer Motion Transitions
5. **AnimatePresence** - Enables exit animations
6. **mode="wait"** - Prevents page overlap
7. **location + key** - Tells React when route changed
8. **PageTransition wrapper** - Reusable animation logic

### Animation Best Practices
9. **Keep it short** - 0.3-0.4s for responsiveness
10. **Use easeOut** - Natural deceleration
11. **Match timing** - Same duration for enter/exit
12. **GPU properties** - opacity, x, y, scale, rotate

---

## Complete Code Reference

Here's your complete working example with comments:

```jsx
import './App.css'
import { AnimatePresence, motion } from "motion/react";
import { BrowserRouter, Routes, Route, Link, useLocation } from "react-router"

// Main app component - wraps everything in BrowserRouter
function App() {
  return (
    <BrowserRouter>
      <div className="app">
        {/* Navigation - stays visible, doesn't animate */}
        <nav className="nav">
          <div className="nav-links">
            <Link to="/">Home</Link>
            <Link to="/about">About</Link>
            <Link to="/contact">Contact</Link>
          </div>
        </nav>
        
        {/* Content area - animates on route change */}
        <AnimateRoutes/>
      </div>
    </BrowserRouter>
  );
}

// Separate component for routing logic
// (Must be inside BrowserRouter to use useLocation)
function AnimateRoutes() {
  // Get current URL location
  const location = useLocation();

  return(
    // AnimatePresence enables exit animations
    // mode="wait" prevents page overlap
    <AnimatePresence mode="wait">
      {/* 
        location={location} - Tells Routes where we are
        key={location.pathname} - Tells React when route changed
      */}
      <Routes location={location} key={location.pathname}>
        {/* Each route wrapped in PageTransition for animations */}
        <Route path="/" element={
          <PageTransition>
            <HomePage/>
          </PageTransition>
        }/>
        <Route path="/about" element={
          <PageTransition>
            <AboutPage/>
          </PageTransition>
        }/>
        <Route path="/contact" element={
          <PageTransition>
            <ContactPage/>
          </PageTransition>
        }/>
      </Routes>
    </AnimatePresence>
  )
}

// Reusable animation wrapper for all pages
function PageTransition({ children }) {
  return (
    <motion.main 
      className="page"
      // Start: invisible, 50px right
      initial={{ opacity: 0, x: 50 }}
      // End: visible, centered
      animate={{ opacity: 1, x: 0 }}
      // Exit: invisible, 50px left
      exit={{ opacity: 0, x: -50 }}
      // Timing: 0.35s with natural deceleration
      transition={{ duration: 0.35, ease: "easeOut" }}
    >
      {children}
    </motion.main>
  )
}

// Individual page components - just content, no animation logic
function HomePage() {
  return (
    <>
      <h2>Home</h2>
      <p>Welcome to the home page</p>
    </>
  )
}

function AboutPage() {
  return (
    <>
      <h2>About</h2>
      <p>Learn about us</p>
    </>
  )
}

function ContactPage() {
  return (
    <>
      <h2>Contact</h2>
      <p>Get in touch</p>
    </>
  )
}

export default App
```

---

## Next Steps

Now that you understand React Router and page transitions, explore:
- **Nested Routes** - Routes within routes
- **Protected Routes** - Authentication-based routing
- **Route Parameters** - Dynamic URLs like `/user/:id`
- **Programmatic Navigation** - `useNavigate()` hook
- **Scroll Restoration** - Remember scroll position
- **Code Splitting** - Lazy load pages for performance
- **Shared Element Transitions** - Elements that morph between pages

You now have the foundation for building professional, animated SPAs! 🚀✨
