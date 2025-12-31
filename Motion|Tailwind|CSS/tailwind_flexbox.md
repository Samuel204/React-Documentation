# Tailwind CSS: Flexbox

## Understanding Flexbox

Flexbox is a powerful one-dimensional layout system that makes it easy to distribute space and align items within a container. It's perfect for navigation bars, cards, and any layout where you need flexible, responsive alignment.

---

## 🎯 Creating a Flex Container

To use Flexbox, first create a flex container:

```html
<div class="flex">
  <!-- Child elements become flex items -->
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

### What Happens?

When you use `display: flex` (or `class="flex"`):
- The container becomes a **flex container**
- Direct children become **flex items**
- Items arrange according to flex properties
- A **main axis** and **cross axis** are established

---

## 🧭 Flex Direction (`flex-direction`)

Controls the direction items flow along the **main axis**.

### Row (Default)

```html
<!-- flex-direction: row -->
<div class="flex flex-row">
  <div class="bg-blue-100 p-4">1</div>
  <div class="bg-blue-200 p-4">2</div>
  <div class="bg-blue-300 p-4">3</div>
</div>
<!-- Result: [1][2][3] → Left to right -->
```

**Behavior:**
- Items flow **left to right** (in LTR languages)
- Main axis is horizontal →
- Cross axis is vertical ↓

### Row Reverse

```html
<!-- flex-direction: row-reverse -->
<div class="flex flex-row-reverse">
  <div class="bg-green-100 p-4">1</div>
  <div class="bg-green-200 p-4">2</div>
  <div class="bg-green-300 p-4">3</div>
</div>
<!-- Result: [3][2][1] → Right to left -->
```

**Behavior:**
- Items flow **right to left**
- Order is reversed

### Column

```html
<!-- flex-direction: column -->
<div class="flex flex-col">
  <div class="bg-purple-100 p-4">1</div>
  <div class="bg-purple-200 p-4">2</div>
  <div class="bg-purple-300 p-4">3</div>
</div>
<!-- Result: 
[1]
[2]
[3]
↓ Top to bottom
-->
```

**Behavior:**
- Items stack **top to bottom**
- Main axis is vertical ↓
- Cross axis is horizontal →

### Column Reverse

```html
<!-- flex-direction: column-reverse -->
<div class="flex flex-col-reverse">
  <div class="bg-red-100 p-4">1</div>
  <div class="bg-red-200 p-4">2</div>
  <div class="bg-red-300 p-4">3</div>
</div>
<!-- Result: 
[3]
[2]
[1]
↑ Bottom to top
-->
```

**Behavior:**
- Items stack **bottom to top**
- Order is reversed

---

## 📦 Flex Wrap (`flex-wrap`)

Controls whether items should wrap to multiple lines when there's not enough space.

### No Wrap (Default)

```html
<!-- flex-wrap: nowrap -->
<div class="flex flex-nowrap">
  <div class="bg-blue-100 p-4 w-64">Wide Item 1</div>
  <div class="bg-blue-200 p-4 w-64">Wide Item 2</div>
  <div class="bg-blue-300 p-4 w-64">Wide Item 3</div>
  <div class="bg-blue-400 p-4 w-64">Wide Item 4</div>
</div>
<!-- Items will shrink to fit on one line -->
```

**Behavior:**
- Single line layout
- Items may shrink to fit
- May overflow container

### Wrap

```html
<!-- flex-wrap: wrap -->
<div class="flex flex-wrap">
  <div class="bg-green-100 p-4 w-64">Wide Item 1</div>
  <div class="bg-green-200 p-4 w-64">Wide Item 2</div>
  <div class="bg-green-300 p-4 w-64">Wide Item 3</div>
  <div class="bg-green-400 p-4 w-64">Wide Item 4</div>
</div>
<!-- Items wrap to next line when needed -->
```

**Behavior:**
- Multi-line layout
- Items maintain their size
- Wraps to next line when needed

### Wrap Reverse

```html
<!-- flex-wrap: wrap-reverse -->
<div class="flex flex-wrap-reverse">
  <div class="bg-purple-100 p-4 w-64">Item 1</div>
  <div class="bg-purple-200 p-4 w-64">Item 2</div>
  <div class="bg-purple-300 p-4 w-64">Item 3</div>
  <div class="bg-purple-400 p-4 w-64">Item 4</div>
</div>
<!-- Wraps lines in reverse order (bottom to top) -->
```

---

## 🎯 Justify Content (Main Axis Alignment)

Controls alignment and spacing of items along the **main axis**.

### Flex Start (Default)

```html
<div class="flex justify-start">
  <div class="bg-blue-100 p-4">1</div>
  <div class="bg-blue-200 p-4">2</div>
  <div class="bg-blue-300 p-4">3</div>
</div>
<!-- [1][2][3]________________ -->
<!-- Items at start, extra space at end -->
```

### Flex End

```html
<div class="flex justify-end">
  <div class="bg-green-100 p-4">1</div>
  <div class="bg-green-200 p-4">2</div>
  <div class="bg-green-300 p-4">3</div>
</div>
<!-- ________________[1][2][3] -->
<!-- Items at end, extra space at start -->
```

### Center

```html
<div class="flex justify-center">
  <div class="bg-purple-100 p-4">1</div>
  <div class="bg-purple-200 p-4">2</div>
  <div class="bg-purple-300 p-4">3</div>
</div>
<!-- ________[1][2][3]________ -->
<!-- Items centered, space on both sides -->
```

### Space Between

```html
<div class="flex justify-between">
  <div class="bg-red-100 p-4">1</div>
  <div class="bg-red-200 p-4">2</div>
  <div class="bg-red-300 p-4">3</div>
</div>
<!-- [1]________[2]________[3] -->
<!-- Equal space BETWEEN items -->
<!-- No space at edges -->
```

### Space Around

```html
<div class="flex justify-around">
  <div class="bg-yellow-100 p-4">1</div>
  <div class="bg-yellow-200 p-4">2</div>
  <div class="bg-yellow-300 p-4">3</div>
</div>
<!-- __[1]____[2]____[3]__ -->
<!-- Equal space around each item -->
<!-- Half space at edges -->
```

### Space Evenly

```html
<div class="flex justify-evenly">
  <div class="bg-pink-100 p-4">1</div>
  <div class="bg-pink-200 p-4">2</div>
  <div class="bg-pink-300 p-4">3</div>
</div>
<!-- ___[1]___[2]___[3]___ -->
<!-- Equal space everywhere -->
<!-- Including edges -->
```

---

## ⬆️ Align Items (Cross Axis Alignment)

Controls how items align along the **cross axis** (perpendicular to main axis).

### Stretch (Default)

```html
<div class="flex items-stretch h-32">
  <div class="bg-blue-100 p-4">1</div>
  <div class="bg-blue-200 p-4">2</div>
  <div class="bg-blue-300 p-4">3</div>
</div>
<!-- Items stretch to fill container height -->
```

### Flex Start

```html
<div class="flex items-start h-32">
  <div class="bg-green-100 p-4">Short</div>
  <div class="bg-green-200 p-4">Tall<br>Item</div>
  <div class="bg-green-300 p-4">Short</div>
</div>
<!-- Items align to top of container -->
```

### Center

```html
<div class="flex items-center h-32">
  <div class="bg-purple-100 p-4">Short</div>
  <div class="bg-purple-200 p-4">Tall<br>Item</div>
  <div class="bg-purple-300 p-4">Short</div>
</div>
<!-- Items vertically centered -->
```

### Flex End

```html
<div class="flex items-end h-32">
  <div class="bg-red-100 p-4">Short</div>
  <div class="bg-red-200 p-4">Tall<br>Item</div>
  <div class="bg-red-300 p-4">Short</div>
</div>
<!-- Items align to bottom -->
```

### Baseline

```html
<div class="flex items-baseline h-32">
  <div class="bg-yellow-100 p-4 text-sm">Small</div>
  <div class="bg-yellow-200 p-4 text-2xl">Large</div>
  <div class="bg-yellow-300 p-4 text-base">Normal</div>
</div>
<!-- Items align by text baseline -->
```

---

## 🎯 Align Self (Individual Item Alignment)

Override alignment for a single flex item:

```html
<div class="flex items-start h-32">
  <div class="bg-blue-100 p-4">Normal</div>
  <div class="bg-blue-200 p-4">Normal</div>
  <div class="bg-blue-300 p-4 self-end">I'm at the bottom!</div>
  <div class="bg-blue-400 p-4">Normal</div>
</div>
```

**Available values:**
- `self-auto` - Inherit from container
- `self-start` - Align to start
- `self-center` - Center alignment
- `self-end` - Align to end
- `self-stretch` - Stretch to fill

### Practical Example

```html
<div class="flex justify-between items-center p-4 bg-gray-100">
  <div class="font-bold">Title</div>
  <div>Content</div>
  <button class="self-end px-4 py-2 bg-blue-500 text-white rounded">
    Button aligned to bottom
  </button>
</div>
```

---

## 📏 Flex Grow, Shrink, and Basis

### Flex Grow (`flex-grow`)

Controls how much a flex item should **grow** to fill available space.

```html
<div class="flex">
  <div class="bg-blue-100 p-4">Fixed</div>
  <div class="bg-blue-200 p-4 flex-grow">I grow to fill space!</div>
  <div class="bg-blue-300 p-4">Fixed</div>
</div>
```

**Common values:**
- `flex-grow-0` - Don't grow (default)
- `flex-grow` - Grow to fill space (flex-grow: 1)

### Flex Shrink (`flex-shrink`)

Controls how much a flex item should **shrink** when space is limited.

```html
<div class="flex">
  <div class="bg-green-100 p-4 flex-shrink-0 w-64">I don't shrink</div>
  <div class="bg-green-200 p-4 flex-shrink">I can shrink if needed</div>
</div>
```

**Common values:**
- `flex-shrink-0` - Don't shrink
- `flex-shrink` - Can shrink (flex-shrink: 1, default)

### Flex Basis

Sets the initial size of a flex item before space distribution.

```html
<div class="flex">
  <div class="bg-purple-100 p-4 basis-1/4">25%</div>
  <div class="bg-purple-200 p-4 basis-1/2">50%</div>
  <div class="bg-purple-300 p-4 basis-1/4">25%</div>
</div>
```

### Flex Shorthand

The `flex` utility combines grow, shrink, and basis:

```html
<!-- flex-1 = flex: 1 1 0% (grow, shrink, basis: 0) -->
<div class="flex">
  <div class="bg-red-100 p-4 flex-1">Equal</div>
  <div class="bg-red-200 p-4 flex-1">Equal</div>
  <div class="bg-red-300 p-4 flex-1">Equal</div>
</div>
<!-- All items get equal width -->

<!-- flex-auto = flex: 1 1 auto (grow, shrink, basis: auto) -->
<div class="flex">
  <div class="bg-blue-100 p-4 flex-auto">Auto</div>
  <div class="bg-blue-200 p-4 flex-auto">Auto</div>
</div>

<!-- flex-initial = flex: 0 1 auto (don't grow, can shrink) -->
<div class="flex">
  <div class="bg-green-100 p-4 flex-initial">Initial</div>
</div>

<!-- flex-none = flex: 0 0 auto (don't grow or shrink) -->
<div class="flex">
  <div class="bg-purple-100 p-4 flex-none">Fixed</div>
</div>
```

---

## 💡 Practical Examples

### Example 1: Navigation Bar

```html
<nav class="flex items-center justify-between bg-white shadow-lg p-4">
  <!-- Logo -->
  <div class="flex items-center">
    <img src="logo.png" class="h-8 mr-4" alt="Logo">
    <span class="font-bold text-xl">Brand</span>
  </div>
  
  <!-- Navigation Links -->
  <div class="flex gap-6">
    <a href="#" class="text-gray-700 hover:text-blue-600">Home</a>
    <a href="#" class="text-gray-700 hover:text-blue-600">About</a>
    <a href="#" class="text-gray-700 hover:text-blue-600">Services</a>
    <a href="#" class="text-gray-700 hover:text-blue-600">Contact</a>
  </div>
  
  <!-- CTA Button -->
  <button class="px-6 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">
    Sign Up
  </button>
</nav>
```

### Example 2: Card Layout

```html
<div class="flex flex-col md:flex-row gap-6 max-w-6xl mx-auto p-6">
  <!-- Card 1 -->
  <div class="flex-1 bg-white rounded-lg shadow-lg p-6">
    <h3 class="text-xl font-bold mb-2">Feature 1</h3>
    <p class="text-gray-600">Description of feature one with flexible sizing.</p>
  </div>
  
  <!-- Card 2 -->
  <div class="flex-1 bg-white rounded-lg shadow-lg p-6">
    <h3 class="text-xl font-bold mb-2">Feature 2</h3>
    <p class="text-gray-600">Description of feature two with flexible sizing.</p>
  </div>
  
  <!-- Card 3 -->
  <div class="flex-1 bg-white rounded-lg shadow-lg p-6">
    <h3 class="text-xl font-bold mb-2">Feature 3</h3>
    <p class="text-gray-600">Description of feature three with flexible sizing.</p>
  </div>
</div>
```

### Example 3: Centered Hero Section

```html
<section class="flex items-center justify-center h-screen bg-gradient-to-r from-blue-500 to-purple-600">
  <div class="text-center text-white">
    <h1 class="text-6xl font-bold mb-4">Welcome</h1>
    <p class="text-xl mb-8">Build amazing things with Tailwind CSS</p>
    <button class="px-8 py-3 bg-white text-blue-600 rounded-lg font-semibold hover:bg-gray-100">
      Get Started
    </button>
  </div>
</section>
```

### Example 4: Sidebar Layout

```html
<div class="flex h-screen">
  <!-- Sidebar -->
  <aside class="flex-none w-64 bg-gray-800 text-white p-6">
    <h2 class="text-2xl font-bold mb-6">Sidebar</h2>
    <nav class="flex flex-col gap-4">
      <a href="#" class="hover:bg-gray-700 p-2 rounded">Dashboard</a>
      <a href="#" class="hover:bg-gray-700 p-2 rounded">Profile</a>
      <a href="#" class="hover:bg-gray-700 p-2 rounded">Settings</a>
    </nav>
  </aside>
  
  <!-- Main Content -->
  <main class="flex-1 bg-gray-50 p-8 overflow-auto">
    <h1 class="text-3xl font-bold mb-4">Main Content</h1>
    <p>This area grows to fill remaining space.</p>
  </main>
</div>
```

### Example 5: Footer with Multiple Sections

```html
<footer class="bg-gray-900 text-white py-12">
  <div class="max-w-7xl mx-auto px-4">
    <div class="flex flex-col md:flex-row justify-between gap-8">
      <!-- Column 1 -->
      <div class="flex-1">
        <h3 class="text-xl font-bold mb-4">Company</h3>
        <ul class="flex flex-col gap-2">
          <li><a href="#" class="hover:text-blue-400">About Us</a></li>
          <li><a href="#" class="hover:text-blue-400">Careers</a></li>
          <li><a href="#" class="hover:text-blue-400">Blog</a></li>
        </ul>
      </div>
      
      <!-- Column 2 -->
      <div class="flex-1">
        <h3 class="text-xl font-bold mb-4">Support</h3>
        <ul class="flex flex-col gap-2">
          <li><a href="#" class="hover:text-blue-400">Help Center</a></li>
          <li><a href="#" class="hover:text-blue-400">Contact</a></li>
          <li><a href="#" class="hover:text-blue-400">FAQ</a></li>
        </ul>
      </div>
      
      <!-- Column 3 -->
      <div class="flex-1">
        <h3 class="text-xl font-bold mb-4">Legal</h3>
        <ul class="flex flex-col gap-2">
          <li><a href="#" class="hover:text-blue-400">Privacy</a></li>
          <li><a href="#" class="hover:text-blue-400">Terms</a></li>
          <li><a href="#" class="hover:text-blue-400">Cookies</a></li>
        </ul>
      </div>
    </div>
  </div>
</footer>
```

---

## 🎨 Combining Properties

```html
<!-- Centered card with space-between content -->
<div class="flex flex-col justify-between items-center h-64 bg-white rounded-lg shadow-lg p-6">
  <h2 class="text-2xl font-bold">Title at Top</h2>
  <p class="text-gray-600">Content in the middle</p>
  <button class="px-6 py-2 bg-blue-500 text-white rounded-lg">Button at Bottom</button>
</div>

<!-- Responsive navigation -->
<nav class="flex flex-col md:flex-row items-center justify-between gap-4 p-4">
  <div>Logo</div>
  <div class="flex flex-col md:flex-row gap-4">
    <a href="#">Link 1</a>
    <a href="#">Link 2</a>
    <a href="#">Link 3</a>
  </div>
</nav>
```

---

## ⚠️ Common Mistakes

1. **Forgetting to add `flex` to the container**
   ```html
   <!-- ❌ Won't work - missing flex -->
   <div class="justify-center">
     <div>Item</div>
   </div>
   
   <!-- ✅ Correct -->
   <div class="flex justify-center">
     <div>Item</div>
   </div>
   ```

2. **Mixing up main and cross axis**
   - `justify-*` = main axis (horizontal in flex-row)
   - `items-*` = cross axis (vertical in flex-row)

3. **Not using gap instead of margins**
   ```html
   <!-- ❌ Harder to maintain -->
   <div class="flex">
     <div class="mr-4">Item 1</div>
     <div class="mr-4">Item 2</div>
     <div>Item 3</div>
   </div>
   
   <!-- ✅ Better approach -->
   <div class="flex gap-4">
     <div>Item 1</div>
     <div>Item 2</div>
     <div>Item 3</div>
   </div>
   ```

---

## 🎓 Quick Reference

```html
<!-- Container -->
<div class="flex">...</div>

<!-- Direction -->
<div class="flex flex-row">→</div>
<div class="flex flex-col">↓</div>

<!-- Main axis (justify) -->
<div class="flex justify-start">Items</div>
<div class="flex justify-center">Items</div>
<div class="flex justify-end">Items</div>
<div class="flex justify-between">Items</div>

<!-- Cross axis (align) -->
<div class="flex items-start">Items</div>
<div class="flex items-center">Items</div>
<div class="flex items-end">Items</div>

<!-- Flex sizing -->
<div class="flex-1">Equal width</div>
<div class="flex-grow">Grows</div>
<div class="flex-shrink-0">No shrink</div>

<!-- Wrap -->
<div class="flex flex-wrap">Items</div>
```

---

## Next Steps

Now that you understand Flexbox, explore:
- **Grid Layout** for 2D layouts
- **Responsive Design** with breakpoints
- **Positioning** utilities
- **Typography** for better text styling

Master Flexbox to build professional, responsive layouts with ease!
