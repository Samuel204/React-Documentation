# React State Hook: Arrays in State

## Introduction

JavaScript arrays are the ideal data structure for managing and rendering JSX lists in React. This guide teaches you how to properly work with arrays in state, including adding, removing, and updating items.

---

## Complete Example: Pizza Topping Selector

Let's examine a pizza ordering application that manages an array of selected toppings:

```jsx
import React, { useState } from 'react';

// Static array of pizza options offered
const options = ['Bell Pepper', 'Sausage', 'Pepperoni', 'Pineapple'];

export default function PersonalPizza() {
  const [selected, setSelected] = useState([]);

  const toggleTopping = ({target}) => {
    const clickedTopping = target.value;
    setSelected((prev) => {
      // check if clicked topping is already selected
      if (prev.includes(clickedTopping)) {
        // filter the clicked topping out of state
        return prev.filter(t => t !== clickedTopping);
      } else {
        // add the clicked topping to our state
        return [clickedTopping, ...prev];
      }
    });
  };

  return (
    <div>
      {options.map(option => (
        <button value={option} onClick={toggleTopping} key={option}>
          {selected.includes(option) ? 'Remove ' : 'Add '}
          {option}
        </button>
      ))}
      <p>Order a {selected.join(', ')} pizza</p>
    </div>
  );
}
```

---

## Understanding the Two Arrays

### 1. Static Array: `options`

```jsx
const options = ['Bell Pepper', 'Sausage', 'Pepperoni', 'Pineapple'];
```

**Characteristics:**
- Contains fixed data that never changes
- Defined outside the component function
- Not part of state

**Why outside the component?**
- Prevents recreation on every render
- Better performance
- No need to track changes

**Use case:** Reference data, configuration, menu items, categories

### 2. Dynamic Array: `selected`

```jsx
const [selected, setSelected] = useState([]);
```

**Characteristics:**
- Contains data that changes based on user interaction
- Part of component state
- Initialized as an empty array `[]`
- Updates trigger re-renders

**Use case:** User selections, shopping carts, form data, filtered lists

---

## Breaking Down the Component

### Step 1: Initialize State

```jsx
const [selected, setSelected] = useState([]);
```

- Creates `selected` state variable
- Starts as empty array `[]` (no toppings selected)
- `setSelected` updates the array

### Step 2: Render Buttons from Static Array

```jsx
{options.map(option => (
  <button value={option} onClick={toggleTopping} key={option}>
    {selected.includes(option) ? 'Remove ' : 'Add '}
    {option}
  </button>
))}
```

**How `.map()` works:**
- Loops through each item in `options` array
- Creates a button for each topping
- Returns array of JSX elements

**Important details:**
- `key={option}` - Required for React to track each element efficiently
- `value={option}` - Stores which topping this button represents
- Button text changes based on whether topping is selected

### Step 3: Display Selected Toppings

```jsx
<p>Order a {selected.join(', ')} pizza</p>
```

**How `.join()` works:**
- Converts array to string
- Inserts `', '` between items
- Example: `['Pepperoni', 'Sausage']` → `"Pepperoni, Sausage"`

---

## The Toggle Logic: Adding and Removing Items

```jsx
const toggleTopping = ({target}) => {
  const clickedTopping = target.value;
  setSelected((prev) => {
    if (prev.includes(clickedTopping)) {
      // Remove: filter out the clicked topping
      return prev.filter(t => t !== clickedTopping);
    } else {
      // Add: create new array with clicked topping
      return [clickedTopping, ...prev];
    }
  });
};
```

### Understanding Each Part

#### 1. Extract Clicked Topping

```jsx
const clickedTopping = target.value;
```

- Gets the value from the button that was clicked
- Example: If "Pepperoni" button clicked, `clickedTopping = "Pepperoni"`

#### 2. Check if Already Selected

```jsx
if (prev.includes(clickedTopping)) {
```

**`.includes()` method:**
- Returns `true` if the item exists in the array
- Returns `false` if it doesn't exist

**Examples:**
```jsx
['Pepperoni', 'Sausage'].includes('Pepperoni')  // true
['Pepperoni', 'Sausage'].includes('Pineapple')  // false
```

#### 3. Remove Item (if already selected)

```jsx
return prev.filter(t => t !== clickedTopping);
```

**`.filter()` method:**
- Creates a new array
- Keeps only items that pass the test
- `t !== clickedTopping` means "keep everything except the clicked topping"

**Example:**
```jsx
// Before: ['Pepperoni', 'Sausage', 'Bell Pepper']
// Click 'Sausage' to remove
// After: ['Pepperoni', 'Bell Pepper']
```

#### 4. Add Item (if not selected)

```jsx
return [clickedTopping, ...prev];
```

**Spread syntax `...prev`:**
- Copies all items from previous array
- Creates a new array with clicked item at the start

**Example:**
```jsx
// Before: ['Pepperoni', 'Sausage']
// Click 'Bell Pepper' to add
// After: ['Bell Pepper', 'Pepperoni', 'Sausage']
```

---

## Critical Rule: Always Create New Arrays

### ❌ Wrong: Mutating the Array

```jsx
// DON'T DO THIS!
const addTopping = (topping) => {
  selected.push(topping);  // Mutates the array
  setSelected(selected);   // React won't detect the change!
};
```

**Why this fails:**
- `.push()` modifies the original array
- React compares array references
- Same reference = React thinks nothing changed
- Component doesn't re-render

### ✅ Correct: Creating New Arrays

```jsx
// DO THIS!
const addTopping = (topping) => {
  setSelected([topping, ...selected]);  // New array
};
```

**Why this works:**
- Creates a completely new array
- Different reference from the old array
- React detects the change
- Component re-renders

---

## Common Array Operations in State

### 1. Adding Items to the End

```jsx
// Add to end of array
setItems(prev => [...prev, newItem]);
```

**Example:**
```jsx
const addTopping = (topping) => {
  setSelected(prev => [...prev, topping]);
};
// ['Pepperoni', 'Sausage'] → ['Pepperoni', 'Sausage', 'Bell Pepper']
```

### 2. Adding Items to the Beginning

```jsx
// Add to beginning of array
setItems(prev => [newItem, ...prev]);
```

**Example:**
```jsx
const addTopping = (topping) => {
  setSelected(prev => [topping, ...prev]);
};
// ['Pepperoni', 'Sausage'] → ['Bell Pepper', 'Pepperoni', 'Sausage']
```

### 3. Removing Items

```jsx
// Remove specific item
setItems(prev => prev.filter(item => item !== itemToRemove));
```

**Example:**
```jsx
const removeTopping = (topping) => {
  setSelected(prev => prev.filter(t => t !== topping));
};
// ['Pepperoni', 'Sausage', 'Bell Pepper'] → ['Pepperoni', 'Bell Pepper']
```

### 4. Removing by Index

```jsx
// Remove item at specific index
setItems(prev => prev.filter((item, index) => index !== indexToRemove));
```

**Example:**
```jsx
const removeAtIndex = (idx) => {
  setSelected(prev => prev.filter((_, index) => index !== idx));
};
// Remove index 1: ['Pepperoni', 'Sausage', 'Bell Pepper'] → ['Pepperoni', 'Bell Pepper']
```

### 5. Updating Items

```jsx
// Update item at specific index
setItems(prev => prev.map((item, index) => 
  index === indexToUpdate ? newValue : item
));
```

**Example:**
```jsx
const updateTopping = (index, newTopping) => {
  setSelected(prev => prev.map((item, i) => 
    i === index ? newTopping : item
  ));
};
// Update index 1: ['Pepperoni', 'Sausage'] → ['Pepperoni', 'Mushroom']
```

### 6. Replacing Entire Array

```jsx
// Replace with completely new array
setItems(newArray);
```

**Example:**
```jsx
const clearAll = () => setSelected([]);
const selectAll = () => setSelected([...options]);
```

---

## Essential Array Methods for React

### `.map()` - Transform and Render

**Purpose:** Create JSX elements from array items

```jsx
{items.map(item => (
  <div key={item.id}>{item.name}</div>
))}
```

**Returns:** New array with transformed items

### `.filter()` - Remove Items

**Purpose:** Create new array without certain items

```jsx
const activeItems = items.filter(item => item.active);
```

**Returns:** New array with items that pass the test

### `.includes()` - Check Existence

**Purpose:** Check if item exists in array

```jsx
if (selected.includes('Pepperoni')) {
  // Pepperoni is selected
}
```

**Returns:** `true` or `false`

### `.join()` - Convert to String

**Purpose:** Create a string from array items

```jsx
const list = ['Apple', 'Banana', 'Orange'];
list.join(', ');  // "Apple, Banana, Orange"
```

**Returns:** String

### `.find()` - Find First Match

**Purpose:** Find the first item that matches a condition

```jsx
const item = items.find(item => item.id === 5);
```

**Returns:** First matching item or `undefined`

### `.some()` - Check if Any Match

**Purpose:** Check if at least one item passes a test

```jsx
const hasExpensive = items.some(item => item.price > 100);
```

**Returns:** `true` or `false`

---

## Practical Examples

### Example 1: Shopping Cart

```jsx
function ShoppingCart() {
  const [cart, setCart] = useState([]);

  const addItem = (item) => {
    setCart(prev => [...prev, item]);
  };

  const removeItem = (itemId) => {
    setCart(prev => prev.filter(item => item.id !== itemId));
  };

  const clearCart = () => {
    setCart([]);
  };

  return (
    <div>
      <h2>Cart ({cart.length} items)</h2>
      {cart.map(item => (
        <div key={item.id}>
          {item.name} - ${item.price}
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <button onClick={clearCart}>Clear Cart</button>
    </div>
  );
}
```

### Example 2: Todo List

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const addTodo = () => {
    if (input.trim()) {
      const newTodo = {
        id: Date.now(),
        text: input,
        completed: false
      };
      setTodos(prev => [...prev, newTodo]);
      setInput('');
    }
  };

  const toggleTodo = (id) => {
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  };

  return (
    <div>
      <input 
        value={input} 
        onChange={e => setInput(e.target.value)}
        placeholder="Add todo..."
      />
      <button onClick={addTodo}>Add</button>
      
      {todos.map(todo => (
        <div key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => toggleTodo(todo.id)}
          />
          <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
            {todo.text}
          </span>
          <button onClick={() => deleteTodo(todo.id)}>Delete</button>
        </div>
      ))}
    </div>
  );
}
```

### Example 3: Tag Selector

```jsx
function TagSelector() {
  const availableTags = ['React', 'JavaScript', 'CSS', 'HTML', 'Node.js'];
  const [selectedTags, setSelectedTags] = useState([]);

  const toggleTag = (tag) => {
    setSelectedTags(prev => {
      if (prev.includes(tag)) {
        return prev.filter(t => t !== tag);
      } else {
        return [...prev, tag];
      }
    });
  };

  return (
    <div>
      <h3>Select Tags:</h3>
      {availableTags.map(tag => (
        <button
          key={tag}
          onClick={() => toggleTag(tag)}
          style={{
            backgroundColor: selectedTags.includes(tag) ? 'blue' : 'gray',
            color: 'white'
          }}
        >
          {tag}
        </button>
      ))}
      <p>Selected: {selectedTags.join(', ') || 'None'}</p>
    </div>
  );
}
```

---

## Why the `key` Prop Matters

```jsx
{options.map(option => (
  <button key={option}>
    {option}
  </button>
))}
```

**What `key` does:**
- Helps React identify which items changed, were added, or removed
- Improves rendering performance
- Prevents bugs with component state

**Rules for keys:**
- Must be unique among siblings
- Should be stable (don't use array index if order changes)
- Best practice: use unique IDs from your data

**Good keys:**
```jsx
// ✅ Unique ID
<div key={item.id}>{item.name}</div>

// ✅ Stable unique value
<div key={option}>{option}</div>
```

**Bad keys:**
```jsx
// ❌ Array index (if order can change)
<div key={index}>{item.name}</div>

// ❌ Random value (changes every render)
<div key={Math.random()}>{item.name}</div>
```

---

## Common Mistakes to Avoid

### 1. Mutating State Directly

```jsx
// ❌ Wrong
selected.push(newItem);
setSelected(selected);

// ✅ Correct
setSelected(prev => [...prev, newItem]);
```

### 2. Forgetting the `key` Prop

```jsx
// ❌ Wrong
{items.map(item => <div>{item}</div>)}

// ✅ Correct
{items.map(item => <div key={item.id}>{item}</div>)}
```

### 3. Using Array Index as Key (when order changes)

```jsx
// ⚠️ Problematic if items can be reordered
{items.map((item, index) => <div key={index}>{item}</div>)}

// ✅ Better - use unique ID
{items.map(item => <div key={item.id}>{item}</div>)}
```

### 4. Not Using Callback Functions

```jsx
// ⚠️ Can cause issues with rapid updates
setSelected([...selected, newItem]);

// ✅ Safer
setSelected(prev => [...prev, newItem]);
```

---

## Best Practices for Arrays in State

1. **Always create new arrays** - Never mutate state directly
2. **Use spread syntax** - `[...prev, newItem]` to copy arrays
3. **Use array methods** - `.filter()`, `.map()`, `.includes()` for updates
4. **Define static arrays outside components** - Prevents recreation
5. **Use callback functions** - When new state depends on previous state
6. **Provide unique keys** - For `.map()` rendered elements
7. **Keep arrays flat** - Simpler to update than nested arrays

---

## Key Takeaways

1. **Arrays are perfect** for managing lists in React
2. **Static arrays go outside components** - Don't recreate them
3. **Dynamic arrays go in state** - Track changing data
4. **Never mutate arrays** - Always create new ones
5. **Spread syntax copies arrays** - `[...prev]`
6. **Use `.filter()` to remove** items from arrays
7. **Use `.map()` to render** lists in JSX
8. **Use `.includes()` to check** if items exist
9. **Always provide `key` prop** when rendering arrays
10. **Strengthen JavaScript skills** - Array methods are essential

---

## Practice Exercises

Build these components to master arrays in state:

1. **Favorite Movies List** - Add, remove, and display movies
2. **Multi-Select Dropdown** - Select multiple options from a list
3. **Shopping List** - Add items with quantities
4. **Skill Tracker** - Add/remove skills with proficiency levels
5. **Filter System** - Show/hide items based on selected filters

---

## Next Steps

- Learn about working with objects in state
- Understand nested state structures
- Explore state management patterns for complex data
- Study performance optimization for large lists
