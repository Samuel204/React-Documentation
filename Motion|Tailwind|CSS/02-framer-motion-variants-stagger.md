# Framer Motion - Variants & Staggered Animations

## What are Variants?

**Variants** are named sets of animation states in Framer Motion. They are a powerful pattern that transforms simple animations into professional, organized, and coordinated animation systems.

### The Core Concept

Instead of defining animation properties directly on each element:
```jsx
// ❌ Manual approach (repetitive)
<motion.div initial={{opacity: 0}} animate={{opacity: 1}} />
```

You create reusable named states:
```jsx
// ✅ Variants approach (clean and reusable)
const myVariant = {
  hidden: {opacity: 0},
  visible: {opacity: 1}
};

<motion.div variants={myVariant} initial="hidden" animate="visible" />
```

---

## Why Use Variants?

### 1. **Organization and Cleanliness**
Your animation logic lives in one place, separate from your JSX.

### 2. **Reusability**
Define once, use everywhere in your component without copying code.

### 3. **Parent-Child Coordination**
The real power! Parents can orchestrate when children animate, while children define how they animate.

### 4. **Staggered Animations**
Create cascading effects where elements appear in sequence.

---

## Understanding Your Code

Let's break down your example step by step:

### Step 1: Define the Container Variant

```typescript
const container = {
  hidden: {opacity: 0},
  visible: {
    opacity: 1, 
    transition: {
      staggerChildren: 0.15,  // Delay between each child
      delayChildren: 1         // Initial delay before any child animates
    }
  }
};
```

**What this means:**
- **`hidden` state**: The container (ul) starts invisible (`opacity: 0`)
- **`visible` state**: The container becomes visible (`opacity: 1`)
- **`staggerChildren: 0.15`**: Each child waits 0.15 seconds after the previous one
- **`delayChildren: 1`**: All children wait 1 second before starting

### Step 2: Define the Item Variant

```typescript
const item = {
  hidden: {opacity: 0, y: 20},  // Start invisible and 20px down
  visible: {opacity: 1, y: 0},  // End visible and at original position
}
```

**What this means:**
- **`hidden` state**: Each item is invisible and positioned 20px below its final position
- **`visible` state**: Each item fades in and slides up to its natural position

### Step 3: Apply Variants to Parent

```jsx
<motion.ul
  variants={container}     // Use the container variant
  initial="hidden"         // Start in "hidden" state
  animate="visible"        // Animate to "visible" state
>
```

**The Magic:** When you set `initial="hidden"` and `animate="visible"` on the parent, these state names **automatically propagate** to all children that have matching variant names!

### Step 4: Apply Variants to Children

```jsx
{features.map((feature) => (
  <motion.li 
    key={feature}
    variants={item}  // Use the item variant
    // NO need for initial/animate - inherited from parent!
  >
    {feature}
  </motion.li>
))}
```

**Notice:** You don't need to add `initial` or `animate` to the children. They inherit the state names from the parent but use their own variant definitions!

---

## How the Animation Timeline Works

Let's visualize what happens in your code:

```
Time 0s:   Parent (ul) changes from hidden → visible (fades in)
           Children wait due to delayChildren: 1
           
Time 1s:   First child (li "Fast") starts animating
           - Fades from opacity 0 → 1
           - Slides from y: 20 → y: 0
           
Time 1.15s: Second child (li "Declarative") starts
            (0.15s after first due to staggerChildren)
            
Time 1.30s: Third child (li "Powerful") starts
            (0.15s after second)
            
Time 1.45s: Fourth child (li "Fun") starts
            (0.15s after third)
```

---

## Staggered Animations Explained

### What is Staggering?

**Staggering** creates a cascading effect where elements appear sequentially instead of all at once. It's visually appealing and guides the user's eye through the content.

### Key Properties

#### `staggerChildren`
- **Purpose**: Adds delay between each child's animation start
- **Unit**: Seconds
- **Example**: `staggerChildren: 0.15` means 0.15s between each child
- **Visual effect**: Creates the "cascade" or "wave" effect

```typescript
// Slow stagger (for testing)
transition: { staggerChildren: 1 }  // 1 second between each

// Professional stagger (smooth and modern)
transition: { staggerChildren: 0.15 }  // 0.15s between each

// Rapid stagger (energetic)
transition: { staggerChildren: 0.05 }  // 0.05s between each
```

#### `delayChildren`
- **Purpose**: Delays when ALL children start animating
- **Unit**: Seconds
- **Example**: `delayChildren: 1` means wait 1 second before any child animates
- **Visual effect**: Adds a pause before the stagger begins

```typescript
// Start immediately
transition: { staggerChildren: 0.15 }

// Wait 1 second, then stagger
transition: { staggerChildren: 0.15, delayChildren: 1 }
```

---

## Visual Comparison

### Without Stagger (All at Once)
```typescript
const container = {
  visible: {
    opacity: 1
    // No staggerChildren - all appear together!
  }
};
```
Result: All 4 items fade in simultaneously → Less interesting

### With Stagger (Cascading)
```typescript
const container = {
  visible: {
    opacity: 1,
    transition: { staggerChildren: 0.15 }
  }
};
```
Result: Items appear one after another → More dynamic and professional!

---

## Real-World Use Cases

### 1. Navigation Menu
```typescript
const menuVariant = {
  closed: { opacity: 0 },
  open: { 
    opacity: 1, 
    transition: { staggerChildren: 0.07, delayChildren: 0.2 }
  }
};

const menuItemVariant = {
  closed: { x: -20, opacity: 0 },
  open: { x: 0, opacity: 1 }
};

<motion.nav variants={menuVariant} initial="closed" animate="open">
  <motion.a variants={menuItemVariant}>Home</motion.a>
  <motion.a variants={menuItemVariant}>About</motion.a>
  <motion.a variants={menuItemVariant}>Contact</motion.a>
</motion.nav>
```

### 2. Card Grid
```typescript
const gridVariant = {
  hidden: { opacity: 0 },
  visible: { 
    opacity: 1, 
    transition: { staggerChildren: 0.1 }
  }
};

const cardVariant = {
  hidden: { scale: 0.8, opacity: 0 },
  visible: { scale: 1, opacity: 1 }
};

<motion.div variants={gridVariant} initial="hidden" animate="visible">
  {cards.map(card => (
    <motion.div key={card.id} variants={cardVariant}>
      {card.content}
    </motion.div>
  ))}
</motion.div>
```

### 3. Hero Section Features
```typescript
const heroVariant = {
  hidden: { y: 50, opacity: 0 },
  visible: { 
    y: 0, 
    opacity: 1, 
    transition: { 
      staggerChildren: 0.2,
      delayChildren: 0.5  // Let hero title appear first
    }
  }
};

const featureVariant = {
  hidden: { y: 20, opacity: 0 },
  visible: { y: 0, opacity: 1 }
};
```

---

## Advanced Patterns

### Pattern 1: Different Stagger Directions

```typescript
// Stagger from first to last
const container = {
  visible: { 
    transition: { staggerChildren: 0.1 }
  }
};

// Stagger from last to first (reverse)
const container = {
  visible: { 
    transition: { 
      staggerChildren: 0.1,
      staggerDirection: -1  // Reverse order!
    }
  }
};
```

### Pattern 2: Combining Multiple Animation Properties

```typescript
const item = {
  hidden: { 
    opacity: 0, 
    y: 20,          // Slide from below
    scale: 0.9,     // Start slightly smaller
    rotate: -5      // Start slightly rotated
  },
  visible: { 
    opacity: 1, 
    y: 0, 
    scale: 1, 
    rotate: 0,
    transition: { 
      duration: 0.5,
      ease: "easeOut"
    }
  }
};
```

### Pattern 3: Custom Transitions Per Property

```typescript
const item = {
  hidden: { opacity: 0, x: -50 },
  visible: { 
    opacity: 1, 
    x: 0,
    transition: {
      opacity: { duration: 0.3 },      // Fast fade
      x: { duration: 0.8, ease: "easeOut" }  // Slow slide
    }
  }
};
```

---

## Understanding the Commented Code

In your example, you have commented code:

```jsx
/*initial={{y: 10}}
  animate={{y: 0}}
  transition={{duration: 1}}*/
```

**This is the OLD way** (manual approach):
- Each element defined its own animation
- No coordination between parent and children
- Repetitive and hard to maintain
- No stagger effect possible

**The NEW way** (with variants):
- Animation logic centralized in variant objects
- Parent orchestrates timing (stagger)
- Children define movement (how they animate)
- Clean, reusable, professional

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Forgetting `variants` Prop
```jsx
// WRONG - animation won't work
<motion.div initial="hidden" animate="visible">
```

```jsx
// CORRECT - include variants
<motion.div variants={myVariant} initial="hidden" animate="visible">
```

### ❌ Mistake 2: Mismatched State Names
```jsx
// WRONG - names don't match
const container = {
  hide: { opacity: 0 },  // "hide"
  show: { opacity: 1 }   // "show"
};

<motion.ul variants={container} initial="hidden" animate="visible">
  // "hidden" and "visible" don't exist in container!
```

### ❌ Mistake 3: Adding initial/animate to Children Unnecessarily
```jsx
// UNNECESSARY - children inherit from parent
<motion.li 
  variants={item}
  initial="hidden"  // Not needed
  animate="visible" // Not needed
>
```

### ❌ Mistake 4: Using staggerChildren Without Variants
```jsx
// WRONG - stagger only works with variants
<motion.ul transition={{ staggerChildren: 0.1 }}>
  <motion.li initial={{opacity: 0}} animate={{opacity: 1}} />
```

---

## Optimization Tips

### 1. **Choose the Right Stagger Speed**
- **0.05-0.1s**: Fast, energetic (good for many items)
- **0.15-0.2s**: Balanced, professional (most common)
- **0.3-0.5s**: Slow, dramatic (good for hero sections)
- **1s+**: Testing only (too slow for production)

### 2. **Don't Overuse Delays**
```typescript
// Too much delay feels sluggish
transition: { delayChildren: 3 }  // ❌ User waits too long

// Keep delays short
transition: { delayChildren: 0.3 }  // ✅ Quick and responsive
```

### 3. **Keep Animations Smooth**
```typescript
const item = {
  visible: { 
    opacity: 1, 
    y: 0,
    transition: { 
      duration: 0.4,        // Not too fast, not too slow
      ease: "easeOut"       // Natural deceleration
    }
  }
};
```

---

## Practice Exercises

### Exercise 1: Create a Staggered Button List
```jsx
// Create a component that animates a list of buttons
// Requirements:
// - Buttons slide in from the left
// - 0.1s stagger between each
// - 0.5s initial delay

const buttonContainer = {
  // Your code here
};

const buttonItem = {
  // Your code here
};
```

### Exercise 2: Reverse Stagger
```jsx
// Make the list animate in reverse order (last item first)
// Hint: Use staggerDirection
```

### Exercise 3: Different Enter/Exit Animations
```jsx
// Create variants with both enter and exit states
// Make items slide in from bottom, but fade out in place
```

---

## Key Takeaways

1. **Variants = Named Animation States** - Clean, reusable animation definitions
2. **Parent Controls When, Children Control How** - Separation of concerns
3. **`staggerChildren`** - Creates cascade effect (delay between items)
4. **`delayChildren`** - Delays entire animation sequence
5. **No need for `initial`/`animate` on children** - They inherit from parent
6. **Professional timing** - Use 0.15s for smooth, modern stagger effects
7. **Variants unlock advanced patterns** - Reverse stagger, custom transitions, complex orchestration

---

## Next Steps

Now that you understand variants and staggering, explore:
- **AnimatePresence** - For exit animations
- **useAnimation** hook - Programmatic control
- **Gesture animations** - Hover, tap, drag with variants
- **Layout animations** - Smooth transitions between states
- **Orchestration** - Complex multi-element choreography

Variants are the foundation of professional Framer Motion animations! 🎬✨
