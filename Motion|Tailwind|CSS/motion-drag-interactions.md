# Framer Motion - Drag Interactions

## What are Drag Interactions?

**Drag interactions** allow users to grab and move elements on the screen with their mouse or touch. This creates intuitive, tactile interfaces perfect for:
- Image uploaders
- Draggable cards
- Trello-style list organization
- Sliders and controls
- Interactive dashboards
- Games and playful UIs

---

## Basic Drag - Getting Started

### The Simplest Implementation

To make any element draggable, you need just two things:

1. Convert it to a `motion` component
2. Add the `drag` prop

```jsx
<motion.div drag>
  Drag me!
</motion.div>
```

**That's it!** The element is now draggable in all directions. The user can:
- Click and hold to grab it
- Move it anywhere on the screen
- Throw it (it has momentum)
- Release it to let it settle

---

## Understanding Your Code

Let's analyze your example step by step:

```jsx
<motion.div 
  className="card"
  drag
  dragConstraints={{left: -130, right: 130, top: -40, bottom: 40}}
  dragElastic={0.2}
>
  Drag me!
</motion.div>
```

### Part 1: `drag`
```jsx
drag
```

This enables dragging in **all directions** (x and y axes).

**Alternatives:**
- `drag="x"` - Only horizontal dragging
- `drag="y"` - Only vertical dragging
- `drag={true}` - All directions (same as just `drag`)
- `drag={false}` - Disable dragging

### Part 2: `dragConstraints`
```jsx
dragConstraints={{left: -130, right: 130, top: -40, bottom: 40}}
```

This defines **boundaries** - an invisible box the element cannot escape from.

**How it works:**
- The element's **original position** is the center (0, 0)
- `left: -130` → Can move 130px to the LEFT of original position
- `right: 130` → Can move 130px to the RIGHT of original position
- `top: -40` → Can move 40px ABOVE original position
- `bottom: 40` → Can move 40px BELOW original position

**Visual representation:**
```
              top: -40px
                  ↑
                  |
    -130px ←─────●─────→ +130px
    (left)   original   (right)
                  |
                  ↓
             bottom: +40px
```

### Part 3: `dragElastic`
```jsx
dragElastic={0.2}
```

This controls how "springy" the boundaries feel.

**Values:**
- `0` = No elasticity (hard stop at boundary)
- `0.2` = Slight give (recommended - natural feel)
- `0.5` = Medium bounce
- `1` = Very elastic (can stretch far beyond boundary)

---

## The Coordinate System Explained

### Why Negative for Left/Top?

The coordinate system starts from the element's **original position**:

```
        Y-axis (negative = up)
             ↑
             |
             |
    ←────────●────────→  X-axis
 (negative)  |  (positive)
   left      |     right
             |
             ↓
      (positive = down)
```

**Examples:**

```jsx
// Box 200px wide, 100px tall
dragConstraints={{
  left: -100,   // Can go 100px left from center
  right: 100,   // Can go 100px right from center
  top: -50,     // Can go 50px up from center
  bottom: 50    // Can go 50px down from center
}}
```

This creates a draggable area of 200x100px centered on the original position.

---

## Drag Direction Control

### Horizontal Only (X-axis)
```jsx
<motion.div 
  drag="x"
  dragConstraints={{left: -200, right: 200}}
>
  Slide me left/right
</motion.div>
```
**Use case:** Image carousel, slider controls

### Vertical Only (Y-axis)
```jsx
<motion.div 
  drag="y"
  dragConstraints={{top: -100, bottom: 100}}
>
  Slide me up/down
</motion.div>
```
**Use case:** Vertical scrollers, pull-to-refresh

### Both Directions (Default)
```jsx
<motion.div 
  drag
  dragConstraints={{left: -150, right: 150, top: -150, bottom: 150}}
>
  Drag me anywhere
</motion.div>
```
**Use case:** Movable cards, Trello-style items

---

## Drag Elasticity Deep Dive

The `dragElastic` prop controls resistance when hitting constraints.

### Comparison of Values

```jsx
// No elasticity - immediate hard stop
<motion.div drag dragConstraints={{...}} dragElastic={0}>
  Rigid boundaries
</motion.div>

// Slight resistance (RECOMMENDED for professional feel)
<motion.div drag dragConstraints={{...}} dragElastic={0.2}>
  Natural feel
</motion.div>

// Medium bounce
<motion.div drag dragConstraints={{...}} dragElastic={0.5}>
  Bouncy
</motion.div>

// Very elastic - can pull far beyond boundary
<motion.div drag dragConstraints={{...}} dragElastic={1}>
  Super stretchy
</motion.div>
```

### What Happens at Different Values?

**dragElastic={0}:**
- User drags to boundary → **STOP** (no give)
- Feels mechanical, precise
- Good for: Technical interfaces, precise positioning

**dragElastic={0.2}:** ✅ **RECOMMENDED**
- User drags to boundary → slight resistance → springs back
- Feels natural and responsive
- Good for: Most use cases, professional apps

**dragElastic={1}:**
- User drags to boundary → can pull element way beyond it
- Feels playful, cartoon-like
- Good for: Games, playful interfaces, demo effects

---

## Real-World Examples

### Example 1: Image Uploader Preview
```jsx
function ImageUploader() {
  return (
    <motion.div 
      className="upload-preview"
      drag
      dragConstraints={{left: -50, right: 50, top: -50, bottom: 50}}
      dragElastic={0.1}
      whileDrag={{ scale: 1.05, cursor: "grabbing" }}
    >
      <img src="photo.jpg" alt="Upload preview" />
    </motion.div>
  );
}
```

### Example 2: Trello-Style Card
```jsx
function DraggableCard({ title, content }) {
  return (
    <motion.div 
      className="trello-card"
      drag
      dragConstraints={{left: -300, right: 300, top: 0, bottom: 0}}
      dragElastic={0.2}
      whileDrag={{ 
        scale: 1.05, 
        rotate: 5,
        boxShadow: "0 10px 30px rgba(0,0,0,0.3)"
      }}
    >
      <h3>{title}</h3>
      <p>{content}</p>
    </motion.div>
  );
}
```

### Example 3: Volume Slider
```jsx
function VolumeSlider() {
  return (
    <motion.div 
      className="slider-track"
      style={{ width: 200, height: 40, position: "relative" }}
    >
      <motion.div 
        className="slider-handle"
        drag="x"
        dragConstraints={{left: 0, right: 160}}
        dragElastic={0}  // No elasticity for precise control
        style={{ 
          width: 40, 
          height: 40, 
          borderRadius: "50%",
          background: "blue"
        }}
      />
    </motion.div>
  );
}
```

### Example 4: Constrained to Parent Container
```jsx
function ConstrainedDrag() {
  const constraintsRef = useRef(null);
  
  return (
    <div ref={constraintsRef} className="container">
      <motion.div 
        drag
        dragConstraints={constraintsRef}  // Stay inside parent!
        dragElastic={0.2}
      >
        Can't leave the box
      </motion.div>
    </div>
  );
}
```

---

## Advanced Drag Features

### Feature 1: Drag Momentum
```jsx
<motion.div 
  drag
  dragConstraints={{...}}
  dragMomentum={true}  // Default: continues moving after release
  dragTransition={{ 
    bounceStiffness: 600, 
    bounceDamping: 20 
  }}
>
  Throw me!
</motion.div>
```

### Feature 2: Disable Momentum
```jsx
<motion.div 
  drag
  dragMomentum={false}  // Stops immediately on release
>
  No momentum
</motion.div>
```

### Feature 3: Drag Event Listeners
```jsx
<motion.div 
  drag
  onDragStart={() => console.log("Started dragging")}
  onDrag={(event, info) => {
    console.log("Dragging at:", info.point.x, info.point.y);
  }}
  onDragEnd={(event, info) => {
    console.log("Released at:", info.point.x, info.point.y);
  }}
>
  Track my movement
</motion.div>
```

### Feature 4: Programmatic Position
```jsx
function ControlledDrag() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  return (
    <>
      <motion.div 
        drag
        style={{ x: position.x, y: position.y }}
        onDrag={(event, info) => setPosition(info.point)}
      >
        Drag me
      </motion.div>
      
      <button onClick={() => setPosition({ x: 0, y: 0 })}>
        Reset Position
      </button>
    </>
  );
}
```

---

## Combining Drag with Other Animations

### Drag + Hover Effects
```jsx
<motion.div 
  drag
  dragConstraints={{left: -100, right: 100, top: -100, bottom: 100}}
  dragElastic={0.2}
  whileHover={{ scale: 1.1 }}
  whileTap={{ scale: 0.95 }}
  whileDrag={{ scale: 1.2, rotate: 10 }}
>
  Interactive card
</motion.div>
```

### Drag + Variants
```jsx
const cardVariants = {
  idle: { scale: 1, rotate: 0 },
  dragging: { scale: 1.1, rotate: 5 },
  released: { scale: 1, rotate: 0 }
};

<motion.div 
  drag
  variants={cardVariants}
  initial="idle"
  whileDrag="dragging"
  animate="idle"
>
  Smooth transitions
</motion.div>
```

---

## Common Patterns and Best Practices

### Pattern 1: Snap Back to Center
```jsx
<motion.div 
  drag
  dragConstraints={{left: -150, right: 150, top: -150, bottom: 150}}
  dragElastic={0.2}
  dragSnapToOrigin={true}  // Returns to original position
>
  I snap back!
</motion.div>
```

### Pattern 2: Cursor Changes
```jsx
<motion.div 
  drag
  style={{ cursor: "grab" }}
  whileDrag={{ cursor: "grabbing" }}
>
  Visual feedback
</motion.div>
```

### Pattern 3: Directional Constraints Based on Content
```jsx
// Horizontal carousel item
<motion.div drag="x" dragConstraints={{left: -500, right: 0}}>
  Swipe to see more
</motion.div>

// Vertical scroll
<motion.div drag="y" dragConstraints={{top: -1000, bottom: 0}}>
  Scroll down
</motion.div>
```

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Wrong Sign on Constraints
```jsx
// WRONG - positive left, negative right
dragConstraints={{left: 100, right: -100, top: 50, bottom: -50}}

// CORRECT
dragConstraints={{left: -100, right: 100, top: -50, bottom: 50}}
```

### ❌ Mistake 2: Forgetting Constraints on Single-Axis Drag
```jsx
// WRONG - drag="x" but constraints include top/bottom
<motion.div drag="x" dragConstraints={{left: -100, right: 100, top: -50, bottom: 50}}>

// CORRECT - only x-axis constraints
<motion.div drag="x" dragConstraints={{left: -100, right: 100}}>
```

### ❌ Mistake 3: Too Much Elasticity
```jsx
// WRONG - too bouncy for serious apps
<motion.div dragElastic={1}>

// CORRECT - subtle and professional
<motion.div dragElastic={0.2}>
```

### ❌ Mistake 4: No Constraints (Element Gets Lost)
```jsx
// RISKY - user can drag element off screen
<motion.div drag>
  I might disappear!
</motion.div>

// SAFE - element stays visible
<motion.div drag dragConstraints={{left: -200, right: 200, top: -200, bottom: 200}}>
  I stay in view!
</motion.div>
```

---

## Performance Tips

### 1. Use `dragMomentum={false}` for Many Elements
```jsx
// Better performance with many draggable items
{items.map(item => (
  <motion.div 
    key={item.id} 
    drag 
    dragMomentum={false}
  />
))}
```

### 2. Limit Constraint Calculations
```jsx
// Use fixed constraints instead of calculating on every render
const CONSTRAINTS = {left: -100, right: 100, top: -100, bottom: 100};

<motion.div drag dragConstraints={CONSTRAINTS} />
```

### 3. Debounce Drag Events
```jsx
import { debounce } from 'lodash';

const handleDrag = debounce((event, info) => {
  // Expensive operation
  updatePosition(info.point);
}, 100);

<motion.div drag onDrag={handleDrag} />
```

---

## Practice Exercises

### Exercise 1: Create a Photo Frame
```jsx
// Make an image that can be dragged within a frame
// Requirements:
// - Image is 200x200px
// - Frame is 300x300px
// - Image can move 50px in any direction
// - Subtle elasticity (0.15)

function PhotoFrame() {
  return (
    <div className="frame" style={{ width: 300, height: 300 }}>
      <motion.img 
        src="photo.jpg"
        // Add drag properties here
      />
    </div>
  );
}
```

### Exercise 2: Horizontal Slider
```jsx
// Create a slider that only moves horizontally
// Range: -200px to +200px
// No elasticity (precise control)
// Reset button to center
```

### Exercise 3: Draggable Cards with Stack Effect
```jsx
// Create 3 stacked cards
// Each card slightly offset
// Only top card is draggable
// When dragged, it scales up and rotates
```

---

## Key Takeaways

1. **`drag` prop** - Enables dragging (`true`, `"x"`, or `"y"`)
2. **`dragConstraints`** - Defines boundaries (left/top negative, right/bottom positive)
3. **`dragElastic`** - Controls bounce (0 = rigid, 0.2 recommended, 1 = very elastic)
4. **Coordinate system** - Original position is (0,0), negative for left/up
5. **Always use constraints** - Prevents elements from being lost off-screen
6. **Combine with other props** - `whileDrag`, `whileHover`, etc. for rich interactions
7. **Professional feel** - Use `dragElastic={0.2}` for natural, non-cartoonish feel

---

## Next Steps

Now that you understand drag interactions, explore:
- **Drag with Gestures** - Combining drag with tap, hover, pan
- **LayoutGroup** - Shared layout animations with draggable elements
- **Reordering Lists** - Build Trello-style drag-to-reorder
- **Drag Events** - Advanced tracking and custom behaviors
- **Physics-based Dragging** - Custom spring configurations

Drag interactions make your interfaces feel alive and responsive! 🎯✨
