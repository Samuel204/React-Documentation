# React Motion: Understanding the "transition" Property

## What is `transition`?

The `transition` property controls **HOW** the animation happens. It defines the speed, timing, easing, and physics of the movement between `initial` and `animate` states. Think of it as the "style of travel" for your animation journey.

---

## Basic Syntax

```jsx
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  transition={{ duration: 0.5 }}
>
  Content here
</motion.div>
```

---

## The Animation Trinity

```
initial → animate → transition
 WHERE      WHERE      HOW
 START      END      TRAVEL
```

**Example:**
```jsx
<motion.div
  initial={{ x: -100 }}     // Start 100px left
  animate={{ x: 0 }}        // End at normal position
  transition={{ duration: 1, ease: "easeOut" }}  // Take 1 second, slow down at end
>
  Content
</motion.div>
```

**Translation:** Start 100px to the left, move to normal position, take 1 second, and slow down smoothly at the end.

---

## Core Transition Properties

### 1. **duration** - How Long the Animation Takes

Controls the length of the animation in seconds.

```jsx
transition={{ duration: 0.3 }}  // Fast (0.3 seconds)
transition={{ duration: 1 }}    // Medium (1 second)
transition={{ duration: 2.5 }}  // Slow (2.5 seconds)
```

**Default:** `0.3` seconds

**Use cases:**
- Quick UI feedback: `0.2 - 0.3s`
- Normal animations: `0.5 - 1s`
- Dramatic effects: `1.5 - 3s`

### 2. **delay** - Wait Before Starting

Pauses before the animation begins (in seconds).

```jsx
transition={{ delay: 0.5 }}     // Wait 0.5s then animate
transition={{ delay: 1 }}       // Wait 1s then animate
transition={{ delay: 0 }}       // No delay (default)
```

**Use cases:**
- Staggered animations (items appear one after another)
- Sequencing multiple elements
- Creating rhythm in your UI

### 3. **ease** - Animation Curve/Feel

Controls how the animation accelerates and decelerates.

```jsx
transition={{ ease: "linear" }}      // Constant speed
transition={{ ease: "easeIn" }}      // Start slow, end fast
transition={{ ease: "easeOut" }}     // Start fast, end slow
transition={{ ease: "easeInOut" }}   // Start slow, fast middle, end slow
```

**Common easing curves:**

| Easing | Feel | Best For |
|--------|------|----------|
| `linear` | Robot-like, constant | Loading bars, spinners |
| `easeIn` | Starts gentle, gains speed | Elements exiting screen |
| `easeOut` | Starts fast, slows down | Elements entering screen (most natural) |
| `easeInOut` | Smooth start and end | General purpose, elegant |

**Custom cubic bezier:**
```jsx
transition={{ ease: [0.17, 0.67, 0.83, 0.67] }}  // Custom curve
```

### 4. **type** - Animation Engine

Chooses between different animation systems.

```jsx
transition={{ type: "tween" }}    // Standard duration-based (default)
transition={{ type: "spring" }}   // Physics-based, bouncy
transition={{ type: "inertia" }}  // Momentum-based, decelerating
```

---

## Animation Types Deep Dive

### Type 1: "tween" (Duration-Based)

The default. Precise control over timing.

```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{ 
    type: "tween",
    duration: 0.5,
    ease: "easeOut"
  }}
>
  Predictable smooth animation
</motion.div>
```

**Characteristics:**
- ✅ Precise timing
- ✅ Predictable end state
- ✅ Full control over duration
- ❌ Can feel mechanical

**Best for:** UI transitions, fade effects, precise timing needs

### Type 2: "spring" (Physics-Based)

Creates bouncy, natural-feeling animations using physics simulation.

```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{ 
    type: "spring",
    stiffness: 300,
    damping: 20
  }}
>
  Bouncy spring animation!
</motion.div>
```

**Spring Properties:**

#### **stiffness** - How Strong the Spring Is
```jsx
transition={{ type: "spring", stiffness: 100 }}   // Loose, wobbly
transition={{ type: "spring", stiffness: 300 }}   // Medium bounce
transition={{ type: "spring", stiffness: 500 }}   // Tight, snappy
```

Higher = faster, snappier bounce

#### **damping** - How Much Resistance/Friction
```jsx
transition={{ type: "spring", damping: 5 }}    // Bounces a lot (low damping)
transition={{ type: "spring", damping: 20 }}   // Some bounce (medium)
transition={{ type: "spring", damping: 40 }}   // Almost no bounce (high)
```

Lower = more bouncy, higher = less bouncy

#### **mass** - Weight of the Element
```jsx
transition={{ type: "spring", mass: 0.5 }}   // Light (fast)
transition={{ type: "spring", mass: 1 }}     // Normal
transition={{ type: "spring", mass: 2 }}     // Heavy (slow)
```

**Characteristics:**
- ✅ Feels natural and organic
- ✅ Responds to interruptions smoothly
- ✅ Adds personality
- ❌ Less predictable timing
- ❌ Can overshoot

**Best for:** Buttons, modals, interactive elements, playful UIs

#### Spring Presets

```jsx
// Gentle bounce
transition={{ type: "spring", stiffness: 120, damping: 14 }}

// Snappy
transition={{ type: "spring", stiffness: 400, damping: 25 }}

// Wobbly
transition={{ type: "spring", stiffness: 180, damping: 12 }}

// No bounce (overdamped)
transition={{ type: "spring", damping: 100 }}
```

### Type 3: "inertia" (Momentum-Based)

Decelerates like a physical object sliding to a stop.

```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{ 
    type: "inertia",
    velocity: 50
  }}
>
  Slides with momentum
</motion.div>
```

**Best for:** Drag-and-release interactions, scroll effects

---

## Practical Examples

### Example 1: Quick Fade In

```jsx
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  transition={{ duration: 0.3 }}
>
  <p>Fast fade in</p>
</motion.div>
```

**Feel:** Quick and snappy UI feedback

### Example 2: Smooth Slide with Ease

```jsx
<motion.div
  initial={{ x: -100, opacity: 0 }}
  animate={{ x: 0, opacity: 1 }}
  transition={{ 
    duration: 0.6,
    ease: "easeOut"
  }}
>
  <h1>Slides in smoothly</h1>
</motion.div>
```

**Feel:** Natural entrance, slows down at the end

### Example 3: Bouncy Button

```jsx
<motion.button
  initial={{ scale: 0 }}
  animate={{ scale: 1 }}
  transition={{ 
    type: "spring",
    stiffness: 260,
    damping: 20
  }}
>
  Click Me!
</motion.button>
```

**Feel:** Fun, playful pop-in with bounce

### Example 4: Staggered List Items

```jsx
{items.map((item, index) => (
  <motion.div
    key={item.id}
    initial={{ opacity: 0, y: 20 }}
    animate={{ opacity: 1, y: 0 }}
    transition={{ 
      duration: 0.4,
      delay: index * 0.1  // Each item delayed by 0.1s
    }}
  >
    {item.name}
  </motion.div>
))}
```

**Feel:** Items cascade in one after another

### Example 5: Infinite Rotation (Loading Spinner)

```jsx
<motion.div
  animate={{ rotate: 360 }}
  transition={{ 
    duration: 1,
    ease: "linear",
    repeat: Infinity  // Keep repeating forever
  }}
>
  🔄
</motion.div>
```

**Feel:** Constant spinning at steady speed

### Example 6: Pulse Animation

```jsx
<motion.div
  animate={{ 
    scale: [1, 1.1, 1]  // Keyframes: normal → bigger → normal
  }}
  transition={{ 
    duration: 2,
    ease: "easeInOut",
    repeat: Infinity
  }}
>
  📢 Notification
</motion.div>
```

**Feel:** Breathing, pulsing effect

---

## Advanced Transition Features

### 1. **repeat** - Loop Animations

```jsx
transition={{ 
  repeat: 0,          // No repeat (default)
  repeat: 3,          // Repeat 3 times
  repeat: Infinity    // Loop forever
}}
```

### 2. **repeatType** - How to Repeat

```jsx
transition={{ 
  repeat: Infinity,
  repeatType: "loop"      // Restart from beginning
}}

transition={{ 
  repeat: Infinity,
  repeatType: "reverse"   // Go back and forth
}}

transition={{ 
  repeat: Infinity,
  repeatType: "mirror"    // Reverse the animation curve
}}
```

**Example - Smooth Back and Forth:**
```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{ 
    duration: 1,
    repeat: Infinity,
    repeatType: "reverse",
    ease: "easeInOut"
  }}
>
  Slides left and right smoothly
</motion.div>
```

### 3. **repeatDelay** - Pause Between Repeats

```jsx
transition={{ 
  repeat: Infinity,
  repeatDelay: 0.5  // Wait 0.5s between each repeat
}}
```

### 4. **times** - Keyframe Timing Control

When using keyframe arrays, control the timing of each keyframe:

```jsx
<motion.div
  animate={{ 
    x: [0, 100, 0]  // Three positions
  }}
  transition={{ 
    duration: 2,
    times: [0, 0.2, 1]  // Stay at 100 for longer
  }}
>
  Custom keyframe timing
</motion.div>
```

`times` array: `[0, 0.2, 1]` means:
- `0%` of animation: at position 0
- `20%` of animation: at position 100
- `100%` of animation: back to position 0

---

## Property-Specific Transitions

You can set different transitions for different properties!

```jsx
<motion.div
  animate={{ x: 100, opacity: 1 }}
  transition={{
    x: { type: "spring", stiffness: 300 },
    opacity: { duration: 0.2 }
  }}
>
  Position uses spring, opacity uses duration
</motion.div>
```

**Use case:** Different properties need different feels - position might be bouncy while opacity fades quickly.

---

## Common Transition Recipes

### Recipe 1: Natural Entrance
```jsx
transition={{ 
  duration: 0.5,
  ease: "easeOut"
}}
```
**Feel:** Smooth, natural, professional

### Recipe 2: Playful Pop
```jsx
transition={{ 
  type: "spring",
  stiffness: 300,
  damping: 15
}}
```
**Feel:** Bouncy, fun, energetic

### Recipe 3: Subtle Fade
```jsx
transition={{ 
  duration: 0.3,
  ease: "easeInOut"
}}
```
**Feel:** Quick, elegant, unobtrusive

### Recipe 4: Dramatic Entrance
```jsx
transition={{ 
  duration: 1.2,
  ease: [0.6, -0.05, 0.01, 0.99]  // Custom curve
}}
```
**Feel:** Anticipation, impact, memorable

### Recipe 5: Continuous Spin
```jsx
transition={{ 
  duration: 2,
  ease: "linear",
  repeat: Infinity
}}
```
**Feel:** Steady, mechanical, loading state

---

## Transition Defaults

If you don't specify a `transition`, Motion uses sensible defaults:

```jsx
// Default transition
{
  type: "tween",
  duration: 0.3,
  ease: "easeOut"
}

// For spring type
{
  type: "spring",
  stiffness: 100,
  damping: 10
}
```

---

## Choosing the Right Transition

### Use **tween** when you need:
- Precise timing
- Fade effects
- Simple, predictable animations
- Loading bars, progress indicators

### Use **spring** when you need:
- Natural, organic feel
- Interactive elements (buttons, modals)
- Playful, energetic UI
- Elements that respond to user input

### Use **inertia** when you need:
- Drag interactions
- Momentum-based movement
- Realistic physics

---

## Key Takeaways

1. **`transition` controls HOW you animate** - speed, timing, feel
2. **Three main types**: tween (duration), spring (physics), inertia (momentum)
3. **Duration is in seconds** - `0.3` = 300 milliseconds
4. **Ease curves change the feel** - easeOut is most natural for entrances
5. **Springs feel alive** - use stiffness (speed) and damping (bounciness)
6. **Can repeat animations** - use `repeat: Infinity` for loops
7. **Different properties can have different transitions**
8. **Default is fast and smooth** - 0.3s with easeOut

---

## Mental Model

```
Car Journey Analogy:

initial = Starting city
animate = Destination city
transition = How you drive

duration = Trip length
ease = Acceleration style (smooth vs jerky)
type: spring = Bouncy suspension
type: tween = Cruise control
delay = Wait time before leaving
```

---

## Quick Reference

| Need | Transition | Code |
|------|------------|------|
| Fast feedback | Short duration | `{ duration: 0.2 }` |
| Smooth entrance | EaseOut | `{ duration: 0.5, ease: "easeOut" }` |
| Bouncy button | Spring | `{ type: "spring", stiffness: 300, damping: 20 }` |
| Gentle bounce | Soft spring | `{ type: "spring", stiffness: 120, damping: 14 }` |
| Loading spinner | Linear + infinite | `{ duration: 1, ease: "linear", repeat: Infinity }` |
| Stagger items | Delays | `{ duration: 0.4, delay: index * 0.1 }` |

---

## Practice Exercise

Try to predict what these animations will feel like:

```jsx
// Animation 1
transition={{ duration: 2, ease: "linear" }}

// Animation 2
transition={{ type: "spring", stiffness: 500, damping: 5 }}

// Animation 3
transition={{ duration: 0.3, ease: "easeOut", delay: 1 }}
```

**Answers:**
1. Slow, robotic, constant speed
2. Very bouncy and fast
3. Waits 1 second, then quick smooth animation

Ready for the next argument? 🚀
