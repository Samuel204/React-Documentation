# JavaScript Array Methods - Map, Filter, Reduce

## Introduction

**Map**, **filter**, and **reduce** are three powerful array methods in JavaScript. They are like `forEach` "on steroids" - they iterate through arrays but with a crucial difference: **they return new values without modifying the original array**.

### The Core Principle: Immutability

```jsx
const original = [1, 2, 3];

// ❌ forEach - mutates, returns nothing
original.forEach(num => num * 2);
console.log(original); // [1, 2, 3] - unchanged, no return value

// ✅ map - doesn't mutate, returns new array
const doubled = original.map(num => num * 2);
console.log(original); // [1, 2, 3] - unchanged
console.log(doubled);  // [2, 4, 6] - new array!
```

**Key benefit:** Your original data stays safe, making code more predictable and easier to debug.

---

## Quick Reference Table

| Method | Purpose | Input | Output | Size Change |
|--------|---------|-------|--------|-------------|
| **map** | Transform each element | Array | Array | Same size |
| **filter** | Select elements by condition | Array | Array | Smaller or equal |
| **reduce** | Aggregate to single value | Array | Any type | Single value |

---

# 1. Map - Transform Data

## What is Map?

**Map** transforms every element in an array into something else, returning a new array of the **same length**.

### The Pattern

```jsx
const newArray = array.map((element, index) => {
  // Transform element
  return transformedElement;
});
```

### Visual Representation

```
Original: [1,    2,    3,    4]
           ↓     ↓     ↓     ↓
Transform: *2    *2    *2    *2
           ↓     ↓     ↓     ↓
Result:   [2,    4,    6,    8]

Same length! Each element transformed.
```

---

## Map - Basic Examples

### Example 1: Double Numbers

```jsx
const prices = [10, 20, 30, 40];

const doubled = prices.map(price => price * 2);

console.log(prices);  // [10, 20, 30, 40] - original unchanged
console.log(doubled); // [20, 40, 60, 80] - new array
```

### Example 2: Apply 50% Discount

```jsx
const prices = [100, 200, 300];

const discounted = prices.map(price => price * 0.5);

console.log(discounted); // [50, 100, 150]
```

**What happens:**
- Each price is multiplied by 0.5
- New array with discounted prices
- Original prices array unchanged

### Example 3: Convert Temperatures (Celsius to Fahrenheit)

```jsx
const celsius = [0, 10, 20, 30];

const fahrenheit = celsius.map(temp => (temp * 9/5) + 32);

console.log(fahrenheit); // [32, 50, 68, 86]
```

### Example 4: Add Strings

```jsx
const names = ['Alice', 'Bob', 'Charlie'];

const greetings = names.map(name => `Hello, ${name}!`);

console.log(greetings);
// ['Hello, Alice!', 'Hello, Bob!', 'Hello, Charlie!']
```

---

## Map with Objects

### Example 5: Extract Property (Objects to Strings)

```jsx
const products = [
  { name: 'Laptop', price: 1000 },
  { name: 'Phone', price: 500 },
  { name: 'Tablet', price: 300 }
];

// Extract just the names
const productNames = products.map(product => product.name);

console.log(productNames); // ['Laptop', 'Phone', 'Tablet']
```

**What happens:**
- Array of objects → Array of strings
- Each object transformed to just its name property

### Example 6: Transform Objects (Apply Discount)

```jsx
const products = [
  { name: 'Laptop', price: 1000 },
  { name: 'Phone', price: 500 }
];

// Apply 10% discount to all products
const discounted = products.map(product => ({
  ...product,              // Keep all original properties
  price: product.price * 0.9  // Overwrite price with discount
}));

console.log(discounted);
// [
//   { name: 'Laptop', price: 900 },
//   { name: 'Phone', price: 450 }
// ]
```

**What happens:**
- Spread operator `...product` copies all properties
- Then we overwrite the `price` property
- Original objects unchanged

### Example 7: Add New Property

```jsx
const users = [
  { firstName: 'John', lastName: 'Doe' },
  { firstName: 'Jane', lastName: 'Smith' }
];

const withFullName = users.map(user => ({
  ...user,
  fullName: `${user.firstName} ${user.lastName}`
}));

console.log(withFullName);
// [
//   { firstName: 'John', lastName: 'Doe', fullName: 'John Doe' },
//   { firstName: 'Jane', lastName: 'Smith', fullName: 'Jane Smith' }
// ]
```

---

## Map - Using Index Parameter

```jsx
const items = ['apple', 'banana', 'cherry'];

const numbered = items.map((item, index) => {
  return `${index + 1}. ${item}`;
});

console.log(numbered);
// ['1. apple', '2. banana', '3. cherry']
```

**Map signature:**
```jsx
array.map((element, index, originalArray) => {
  // element: current item
  // index: current position (0, 1, 2...)
  // originalArray: the full array (rarely used)
})
```

---

# 2. Filter - Select Data

## What is Filter?

**Filter** creates a new array containing only elements that pass a test (condition). The result is usually **smaller** than the original.

### The Pattern

```jsx
const newArray = array.filter((element) => {
  // Return true to KEEP element
  // Return false to REMOVE element
  return condition;
});
```

### Visual Representation

```
Original: [10,   25,   15,   30,   5]
           ↓     ↓     ↓     ↓    ↓
Test:     < 20  < 20  < 20  < 20  < 20
           ✓     ✗     ✓     ✗    ✓
           ↓           ↓           ↓
Result:   [10,        15,         5]

Smaller array! Only elements that passed test.
```

---

## Filter - Basic Examples

### Example 1: Find Cheap Products

```jsx
const products = [
  { name: 'Laptop', price: 1000 },
  { name: 'Mouse', price: 25 },
  { name: 'Keyboard', price: 75 },
  { name: 'Monitor', price: 300 }
];

// Find products under $200
const affordable = products.filter(product => product.price < 200);

console.log(affordable);
// [
//   { name: 'Mouse', price: 25 },
//   { name: 'Keyboard', price: 75 }
// ]
```

**What happens:**
- Checks each product: `price < 200`?
- Mouse (25 < 200) → ✓ Keep
- Keyboard (75 < 200) → ✓ Keep
- Laptop (1000 < 200) → ✗ Remove
- Monitor (300 < 200) → ✗ Remove

### Example 2: Filter by Multiple Conditions

```jsx
const products = [
  { name: 'Laptop', price: 1000, color: 'silver' },
  { name: 'Phone', price: 500, color: 'black' },
  { name: 'Tablet', price: 150, color: 'white' },
  { name: 'Watch', price: 80, color: 'white' }
];

// Find white products under $200
const whiteAffordable = products.filter(product => 
  product.color === 'white' && product.price < 200
);

console.log(whiteAffordable);
// [
//   { name: 'Tablet', price: 150, color: 'white' },
//   { name: 'Watch', price: 80, color: 'white' }
// ]
```

### Example 3: Remove Falsy Values

```jsx
const mixed = [0, 'hello', '', null, 42, undefined, 'world', false];

const truthyOnly = mixed.filter(item => item);

console.log(truthyOnly); // ['hello', 42, 'world']
```

**What happens:**
- `filter(item => item)` is shorthand for "keep truthy values"
- 0, '', null, undefined, false are all falsy → removed

### Example 4: Find Even Numbers

```jsx
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const evens = numbers.filter(num => num % 2 === 0);

console.log(evens); // [2, 4, 6, 8, 10]
```

### Example 5: Search Functionality

```jsx
const products = [
  { name: 'Laptop', category: 'Electronics' },
  { name: 'Shirt', category: 'Clothing' },
  { name: 'Phone', category: 'Electronics' },
  { name: 'Pants', category: 'Clothing' }
];

const searchTerm = 'lap';

const searchResults = products.filter(product => 
  product.name.toLowerCase().includes(searchTerm.toLowerCase())
);

console.log(searchResults); // [{ name: 'Laptop', category: 'Electronics' }]
```

**Real-world use:** Search bars, filtering lists

### Example 6: Filter by Category

```jsx
const products = [
  { name: 'Laptop', category: 'Electronics' },
  { name: 'Shirt', category: 'Clothing' },
  { name: 'Phone', category: 'Electronics' }
];

const electronics = products.filter(product => 
  product.category === 'Electronics'
);

console.log(electronics);
// [
//   { name: 'Laptop', category: 'Electronics' },
//   { name: 'Phone', category: 'Electronics' }
// ]
```

---

# 3. Reduce - Aggregate Data

## What is Reduce?

**Reduce** processes an array and returns a **single value**. It "reduces" many items to one result (number, string, object, etc.).

### The Pattern

```jsx
const result = array.reduce((accumulator, element) => {
  // Update and return accumulator
  return updatedAccumulator;
}, initialValue);
```

### Visual Representation

```
Array:  [10,    20,    30,    40]
         ↓      ↓      ↓      ↓
Acc:    0  →  10  →  30  →  60  → 100
       (init) (+10) (+20) (+30) (+40)

Result: 100 (single value!)
```

---

## Reduce - How It Works Step by Step

```jsx
const numbers = [1, 2, 3, 4];

const sum = numbers.reduce((total, num) => {
  return total + num;
}, 0);

console.log(sum); // 10
```

**Step-by-step execution:**

```
Iteration 1:
  total = 0 (initial value)
  num = 1
  return 0 + 1 = 1

Iteration 2:
  total = 1 (previous return)
  num = 2
  return 1 + 2 = 3

Iteration 3:
  total = 3
  num = 3
  return 3 + 3 = 6

Iteration 4:
  total = 6
  num = 4
  return 6 + 4 = 10

Final result: 10
```

---

## Reduce - Basic Examples

### Example 1: Sum Numbers

```jsx
const prices = [10, 20, 30, 40];

const total = prices.reduce((sum, price) => sum + price, 0);

console.log(total); // 100
```

**Shorthand version:**
```jsx
const total = prices.reduce((sum, price) => sum + price, 0);
```

### Example 2: Shopping Cart Total

```jsx
const cart = [
  { name: 'Laptop', price: 1000, quantity: 1 },
  { name: 'Mouse', price: 25, quantity: 2 },
  { name: 'Keyboard', price: 75, quantity: 1 }
];

const cartTotal = cart.reduce((total, item) => {
  return total + (item.price * item.quantity);
}, 0);

console.log(cartTotal); // 1125
// (1000*1) + (25*2) + (75*1) = 1000 + 50 + 75 = 1125
```

**What happens:**
- Start with total = 0
- Add Laptop: 0 + 1000 = 1000
- Add Mice: 1000 + 50 = 1050
- Add Keyboard: 1050 + 75 = 1125

### Example 3: Find Maximum Value

```jsx
const numbers = [5, 12, 8, 130, 44];

const max = numbers.reduce((highest, num) => {
  return num > highest ? num : highest;
}, numbers[0]);

console.log(max); // 130
```

**Alternative (cleaner):**
```jsx
const max = numbers.reduce((highest, num) => 
  Math.max(highest, num)
);
```

### Example 4: Count Occurrences

```jsx
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];

const count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});

console.log(count);
// { apple: 3, banana: 2, orange: 1 }
```

**Step-by-step:**
```
Start: acc = {}
'apple': acc = { apple: 1 }
'banana': acc = { apple: 1, banana: 1 }
'apple': acc = { apple: 2, banana: 1 }
'orange': acc = { apple: 2, banana: 1, orange: 1 }
'banana': acc = { apple: 2, banana: 2, orange: 1 }
'apple': acc = { apple: 3, banana: 2, orange: 1 }
```

### Example 5: Concatenate Strings

```jsx
const words = ['Hello', 'world', 'from', 'JavaScript'];

const sentence = words.reduce((text, word) => {
  return text + ' ' + word;
});

console.log(sentence); // 'Hello world from JavaScript'
```

### Example 6: Flatten Array

```jsx
const nested = [[1, 2], [3, 4], [5, 6]];

const flattened = nested.reduce((flat, arr) => {
  return flat.concat(arr);
}, []);

console.log(flattened); // [1, 2, 3, 4, 5, 6]
```

### Example 7: Group by Property

```jsx
const products = [
  { name: 'Laptop', category: 'Electronics' },
  { name: 'Shirt', category: 'Clothing' },
  { name: 'Phone', category: 'Electronics' },
  { name: 'Pants', category: 'Clothing' }
];

const grouped = products.reduce((acc, product) => {
  const category = product.category;
  
  if (!acc[category]) {
    acc[category] = [];
  }
  
  acc[category].push(product);
  return acc;
}, {});

console.log(grouped);
// {
//   Electronics: [
//     { name: 'Laptop', category: 'Electronics' },
//     { name: 'Phone', category: 'Electronics' }
//   ],
//   Clothing: [
//     { name: 'Shirt', category: 'Clothing' },
//     { name: 'Pants', category: 'Clothing' }
//   ]
// }
```

---

# Chaining Methods - Combining Map, Filter, Reduce

## The Power of Chaining

You can chain these methods together to create powerful data transformations!

### Example 1: Filter, Map, Reduce

```jsx
const products = [
  { name: 'Laptop', price: 1000, inStock: true },
  { name: 'Phone', price: 500, inStock: true },
  { name: 'Tablet', price: 300, inStock: false },
  { name: 'Watch', price: 200, inStock: true }
];

const totalInStock = products
  .filter(product => product.inStock)        // Keep only in-stock items
  .map(product => product.price * 0.9)       // Apply 10% discount
  .reduce((total, price) => total + price, 0); // Sum total

console.log(totalInStock); // 1530
// (1000 * 0.9) + (500 * 0.9) + (200 * 0.9)
// = 900 + 450 + 180 = 1530
```

**Step-by-step:**
1. **Filter:** Remove out-of-stock items
   ```
   [Laptop, Phone, Tablet, Watch] → [Laptop, Phone, Watch]
   ```

2. **Map:** Apply discount
   ```
   [1000, 500, 200] → [900, 450, 180]
   ```

3. **Reduce:** Sum total
   ```
   [900, 450, 180] → 1530
   ```

### Example 2: Cart Total with Discount and Tax

```jsx
const cart = [
  { name: 'Laptop', price: 1000, quantity: 1 },
  { name: 'Mouse', price: 50, quantity: 2 },
  { name: 'Keyboard', price: 100, quantity: 1 }
];

const discountPercent = 0.1; // 10% discount
const taxRate = 0.08;        // 8% tax

const finalTotal = cart
  .map(item => ({
    ...item,
    subtotal: item.price * item.quantity
  }))
  .map(item => ({
    ...item,
    discounted: item.subtotal * (1 - discountPercent)
  }))
  .reduce((total, item) => total + item.discounted, 0) * (1 + taxRate);

console.log(finalTotal.toFixed(2));
```

### Example 3: Find Expensive Items and Get Names

```jsx
const products = [
  { name: 'Laptop', price: 1000 },
  { name: 'Mouse', price: 25 },
  { name: 'Monitor', price: 300 },
  { name: 'Keyboard', price: 75 }
];

const expensiveNames = products
  .filter(product => product.price > 100)  // Filter expensive
  .map(product => product.name)            // Extract names
  .join(', ');                             // Join to string

console.log(expensiveNames); // 'Laptop, Monitor'
```

---

# Comparison Summary

## When to Use Each Method

### Use **Map** when:
- ✅ You want to transform every element
- ✅ Output array same size as input
- ✅ Converting data types (objects to strings, etc.)
- ✅ Applying calculations to all items

**Example use cases:**
- Apply discount to all prices
- Format all dates
- Extract property from all objects
- Convert Celsius to Fahrenheit

### Use **Filter** when:
- ✅ You want a subset of elements
- ✅ Removing unwanted items
- ✅ Finding items that match criteria
- ✅ Search functionality

**Example use cases:**
- Find products in price range
- Get completed tasks
- Search by name
- Remove duplicates

### Use **Reduce** when:
- ✅ You need a single value from array
- ✅ Summing, averaging, counting
- ✅ Building a new structure (object, string)
- ✅ Complex aggregations

**Example use cases:**
- Calculate shopping cart total
- Count occurrences
- Find min/max
- Group items by category

---

## Visual Comparison

```jsx
const numbers = [1, 2, 3, 4, 5];

// MAP - Transform all
const doubled = numbers.map(n => n * 2);
// [1, 2, 3, 4, 5] → [2, 4, 6, 8, 10]
// Same length, all transformed

// FILTER - Select some
const evens = numbers.filter(n => n % 2 === 0);
// [1, 2, 3, 4, 5] → [2, 4]
// Smaller, only matching items

// REDUCE - Aggregate to one
const sum = numbers.reduce((total, n) => total + n, 0);
// [1, 2, 3, 4, 5] → 15
// Single value
```

---

# Common Patterns and Best Practices

## Pattern 1: Extract and Transform

```jsx
// Get discounted prices of affordable products
const affordableDiscounted = products
  .filter(p => p.price < 200)
  .map(p => p.price * 0.9);
```

## Pattern 2: Count by Condition

```jsx
// Count how many products are expensive
const expensiveCount = products
  .filter(p => p.price > 500)
  .length;

// Or with reduce:
const expensiveCount = products.reduce((count, p) => 
  p.price > 500 ? count + 1 : count
, 0);
```

## Pattern 3: Average Value

```jsx
const prices = [10, 20, 30, 40];

const average = prices.reduce((sum, price) => sum + price, 0) / prices.length;
// 25
```

## Pattern 4: Remove Duplicates

```jsx
const numbers = [1, 2, 2, 3, 3, 3, 4];

const unique = numbers.filter((num, index, arr) => 
  arr.indexOf(num) === index
);

console.log(unique); // [1, 2, 3, 4]
```

---

# Performance Considerations

## Chaining Creates Multiple Loops

```jsx
// ⚠️ Three separate iterations
products
  .filter(p => p.inStock)    // Loop 1
  .map(p => p.price)         // Loop 2
  .reduce((sum, p) => sum + p, 0); // Loop 3
```

## Single Reduce Can Be More Efficient

```jsx
// ✅ Single iteration
products.reduce((total, p) => {
  if (p.inStock) {
    return total + p.price;
  }
  return total;
}, 0);
```

**Trade-off:**
- Chaining: More readable, less performant
- Single reduce: More performant, less readable

**Recommendation:** Use chaining for clarity unless performance is critical.

---

# Common Mistakes to Avoid

## Mistake 1: Forgetting to Return

```jsx
// ❌ WRONG - no return statement
const doubled = numbers.map(n => {
  n * 2; // Missing return!
});
// Result: [undefined, undefined, undefined]

// ✅ CORRECT
const doubled = numbers.map(n => {
  return n * 2;
});

// ✅ ALSO CORRECT - implicit return
const doubled = numbers.map(n => n * 2);
```

## Mistake 2: Mutating Original Array

```jsx
// ❌ WRONG - mutates original
const doubled = numbers.map(n => {
  numbers[0] = 999; // Don't do this!
  return n * 2;
});

// ✅ CORRECT - pure function
const doubled = numbers.map(n => n * 2);
```

## Mistake 3: Not Providing Initial Value to Reduce

```jsx
// ⚠️ RISKY - no initial value
const sum = numbers.reduce((total, n) => total + n);
// Works for numbers, but breaks on empty array!

// ✅ SAFE - always provide initial value
const sum = numbers.reduce((total, n) => total + n, 0);
```

## Mistake 4: Wrong Return in Filter

```jsx
// ❌ WRONG - returns price, not boolean
const cheap = products.filter(p => p.price); 
// Keeps all products with truthy prices

// ✅ CORRECT - returns boolean
const cheap = products.filter(p => p.price < 100);
```

---

# Practice Exercises

## Exercise 1: Student Grades

```jsx
const students = [
  { name: 'Alice', grade: 85 },
  { name: 'Bob', grade: 92 },
  { name: 'Charlie', grade: 78 },
  { name: 'David', grade: 95 }
];

// 1. Get names of students with grade > 80
// 2. Calculate average grade
// 3. Add 5 bonus points to all grades
```

## Exercise 2: E-commerce

```jsx
const products = [
  { name: 'Laptop', price: 1000, category: 'Electronics', inStock: true },
  { name: 'Shirt', price: 50, category: 'Clothing', inStock: true },
  { name: 'Phone', price: 800, category: 'Electronics', inStock: false },
  { name: 'Shoes', price: 120, category: 'Clothing', inStock: true }
];

// 1. Total value of all in-stock electronics
// 2. Names of all clothing items under $100
// 3. Average price by category
```

## Exercise 3: Word Analysis

```jsx
const sentence = "the quick brown fox jumps over the lazy dog";

// 1. Count how many times each word appears
// 2. Find words longer than 4 characters
// 3. Create acronym from first letters
```

---

# Key Takeaways

1. **Map** → Transform all elements → Same size array
2. **Filter** → Select matching elements → Smaller/equal array
3. **Reduce** → Aggregate to single value → Any type
4. **All are immutable** → Original array unchanged
5. **Can chain them** → `filter().map().reduce()`
6. **Always return** → Otherwise undefined results
7. **Reduce needs initial value** → Safer, more predictable

---

## Quick Decision Tree

```
Do you need ALL elements transformed?
  → Use MAP

Do you need SOME elements that match a condition?
  → Use FILTER

Do you need ONE value from all elements?
  → Use REDUCE

Do you need multiple operations?
  → CHAIN them: filter().map().reduce()
```

---

These three methods are the foundation of functional programming in JavaScript. Master them, and you'll write cleaner, more maintainable code! 🚀
