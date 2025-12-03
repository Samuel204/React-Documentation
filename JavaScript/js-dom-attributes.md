# JavaScript DOM Attributes Guide

A comprehensive guide to manipulating HTML elements using JavaScript attributes and properties.

---

## Table of Contents
1. [Selecting Elements](#selecting-elements)
2. [Modifying Content](#modifying-content)
3. [Changing Styles](#changing-styles)
4. [Working with Attributes](#working-with-attributes)
5. [Modifying Classes](#modifying-classes)
6. [Event Attributes](#event-attributes)

---

## 1. Selecting Elements

Before you can manipulate elements, you need to select them from the DOM.

### `getElementById()`
Selects an element by its ID attribute.

```javascript
// HTML: <div id="myDiv">Hello</div>
let element = document.getElementById('myDiv');
console.log(element); // <div id="myDiv">Hello</div>
```

### `getElementsByClassName()`
Selects all elements with a specific class (returns a collection).

```javascript
// HTML: <p class="text">First</p> <p class="text">Second</p>
let elements = document.getElementsByClassName('text');
console.log(elements.length); // 2
console.log(elements[0]); // First paragraph
```

### `querySelector()`
Selects the first element that matches a CSS selector.

```javascript
// HTML: <button class="btn primary">Click</button>
let button = document.querySelector('.btn.primary');
let firstDiv = document.querySelector('div');
```

### `querySelectorAll()`
Selects all elements that match a CSS selector.

```javascript
// HTML: <li>Item 1</li> <li>Item 2</li> <li>Item 3</li>
let allItems = document.querySelectorAll('li');
allItems.forEach(item => console.log(item));
```

---

## 2. Modifying Content

### `innerHTML`
Gets or sets the HTML content inside an element.

```javascript
// HTML: <div id="container"><p>Old content</p></div>
let container = document.getElementById('container');

// Get current content
console.log(container.innerHTML); // "<p>Old content</p>"

// Set new content (can include HTML tags)
container.innerHTML = '<h2>New Title</h2><p>New paragraph</p>';
```

### `textContent`
Gets or sets the text content (ignores HTML tags).

```javascript
// HTML: <p id="text">Hello <strong>World</strong></p>
let paragraph = document.getElementById('text');

console.log(paragraph.textContent); // "Hello World" (no tags)
console.log(paragraph.innerHTML);   // "Hello <strong>World</strong>"

// Set new text (HTML tags are treated as plain text)
paragraph.textContent = '<b>Bold?</b>'; // Displays: <b>Bold?</b>
```

### `innerText`
Similar to textContent but respects CSS styling (hidden elements are excluded).

```javascript
// HTML: <div id="box">Visible <span style="display:none">Hidden</span></div>
let box = document.getElementById('box');

console.log(box.textContent); // "Visible Hidden"
console.log(box.innerText);   // "Visible" (hidden text excluded)
```

---

## 3. Changing Styles

### `style` Property
Directly modifies inline CSS styles.

```javascript
// HTML: <div id="box">Styled Box</div>
let box = document.getElementById('box');

// Change individual styles
box.style.color = 'red';
box.style.backgroundColor = 'yellow';
box.style.fontSize = '20px';
box.style.border = '2px solid black';
box.style.padding = '10px';

// CSS property names become camelCase:
// background-color → backgroundColor
// font-size → fontSize
// margin-top → marginTop
```

### Multiple Style Changes Example

```javascript
let element = document.querySelector('.card');

element.style.width = '300px';
element.style.height = '200px';
element.style.borderRadius = '10px';
element.style.boxShadow = '0 4px 6px rgba(0,0,0,0.1)';
element.style.transition = 'all 0.3s ease';
```

---

## 4. Working with Attributes

### `getAttribute()`
Gets the value of an attribute.

```javascript
// HTML: <img id="photo" src="image.jpg" alt="Photo">
let img = document.getElementById('photo');

let source = img.getAttribute('src');     // "image.jpg"
let altText = img.getAttribute('alt');    // "Photo"
```

### `setAttribute()`
Sets or changes an attribute value.

```javascript
// HTML: <a id="link" href="page1.html">Link</a>
let link = document.getElementById('link');

// Change the href attribute
link.setAttribute('href', 'page2.html');

// Add a new attribute
link.setAttribute('target', '_blank');
link.setAttribute('title', 'Opens in new tab');
```

### `removeAttribute()`
Removes an attribute from an element.

```javascript
// HTML: <button id="btn" disabled>Click</button>
let button = document.getElementById('btn');

button.removeAttribute('disabled'); // Button is now enabled
```

### `hasAttribute()`
Checks if an element has a specific attribute.

```javascript
// HTML: <input id="email" type="email" required>
let input = document.getElementById('email');

if (input.hasAttribute('required')) {
  console.log('This field is required');
}
```

### Direct Property Access
Many attributes can be accessed directly as properties.

```javascript
// HTML: <img id="photo" src="old.jpg" alt="Photo">
let img = document.getElementById('photo');

// Direct property access (preferred for common attributes)
img.src = 'new.jpg';
img.alt = 'New photo';
img.id = 'newPhoto';

// HTML: <input id="check" type="checkbox">
let checkbox = document.getElementById('check');
checkbox.checked = true;  // Check the box
checkbox.disabled = true; // Disable it
```

---

## 5. Modifying Classes

### `className`
Gets or sets the entire class attribute as a string.

```javascript
// HTML: <div id="box" class="container blue">Box</div>
let box = document.getElementById('box');

console.log(box.className); // "container blue"

// Replace all classes
box.className = 'new-class another-class';
```

### `classList` Property
Provides methods to manipulate classes easily.

#### `classList.add()`
Adds one or more classes.

```javascript
// HTML: <div id="box" class="container">Box</div>
let box = document.getElementById('box');

box.classList.add('active');
box.classList.add('large', 'highlighted'); // Multiple classes
// Result: <div class="container active large highlighted">
```

#### `classList.remove()`
Removes one or more classes.

```javascript
box.classList.remove('active');
box.classList.remove('large', 'highlighted');
```

#### `classList.toggle()`
Adds the class if it doesn't exist, removes it if it does.

```javascript
// HTML: <button id="btn" class="button">Toggle</button>
let btn = document.getElementById('btn');

btn.classList.toggle('active'); // Adds 'active'
btn.classList.toggle('active'); // Removes 'active'
btn.classList.toggle('active'); // Adds 'active' again

// Useful for show/hide functionality
let menu = document.querySelector('.menu');
menu.classList.toggle('open'); // Opens or closes menu
```

#### `classList.contains()`
Checks if an element has a specific class.

```javascript
if (box.classList.contains('active')) {
  console.log('Box is active!');
}
```

#### `classList.replace()`
Replaces one class with another.

```javascript
box.classList.replace('old-class', 'new-class');
```

---

## 6. Event Attributes

### Common Event Properties

```javascript
let button = document.getElementById('myButton');

// Click event
button.onclick = function() {
  alert('Button clicked!');
};

// Mouse events
button.onmouseover = function() {
  this.style.backgroundColor = 'yellow';
};

button.onmouseout = function() {
  this.style.backgroundColor = '';
};

// Better way: addEventListener
button.addEventListener('click', function() {
  console.log('Clicked!');
});
```

---

## Complete Practical Example

```javascript
// HTML:
// <div id="card" class="card">
//   <h2 id="title">Product Name</h2>
//   <img id="image" src="default.jpg" alt="Product">
//   <p id="description">Description here</p>
//   <button id="buyBtn">Buy Now</button>
// </div>

// Select elements
let card = document.getElementById('card');
let title = document.getElementById('title');
let image = document.getElementById('image');
let description = document.getElementById('description');
let button = document.getElementById('buyBtn');

// Modify content
title.textContent = 'Premium Headphones';
description.innerHTML = '<strong>Amazing sound quality!</strong> Now on sale.';

// Change attributes
image.setAttribute('src', 'headphones.jpg');
image.setAttribute('alt', 'Premium Headphones');

// Modify styles
card.style.border = '2px solid #333';
card.style.padding = '20px';
card.style.borderRadius = '10px';
card.style.boxShadow = '0 4px 8px rgba(0,0,0,0.2)';

// Add classes
card.classList.add('featured', 'highlighted');

// Add event listener
button.addEventListener('click', function() {
  // Toggle active state
  card.classList.toggle('active');
  
  // Change button text
  if (card.classList.contains('active')) {
    button.textContent = 'Added to Cart!';
    button.style.backgroundColor = 'green';
  } else {
    button.textContent = 'Buy Now';
    button.style.backgroundColor = '';
  }
});
```

---

## CSS Property Name Conversions

When using `element.style`, CSS properties with hyphens become camelCase:

| CSS Property | JavaScript Property |
|--------------|-------------------|
| `background-color` | `backgroundColor` |
| `font-size` | `fontSize` |
| `margin-top` | `marginTop` |
| `border-radius` | `borderRadius` |
| `box-shadow` | `boxShadow` |
| `text-align` | `textAlign` |
| `padding-left` | `paddingLeft` |
| `z-index` | `zIndex` |

---

## Quick Reference Cheat Sheet

```javascript
// SELECTING
document.getElementById('id')
document.getElementsByClassName('class')
document.querySelector('.class')
document.querySelectorAll('div')

// CONTENT
element.innerHTML = '<p>HTML content</p>'
element.textContent = 'Plain text'
element.innerText = 'Visible text'

// STYLES
element.style.color = 'red'
element.style.fontSize = '20px'

// ATTRIBUTES
element.getAttribute('src')
element.setAttribute('href', 'url')
element.removeAttribute('disabled')
element.hasAttribute('required')

// DIRECT PROPERTIES
element.src = 'image.jpg'
element.href = 'page.html'
element.value = 'text'
element.checked = true

// CLASSES
element.className = 'class1 class2'
element.classList.add('new-class')
element.classList.remove('old-class')
element.classList.toggle('active')
element.classList.contains('active')

// EVENTS
element.onclick = function() {}
element.addEventListener('click', function() {})
```

---

## Practice Exercise

Try creating a color theme switcher:

```javascript
// HTML:
// <button id="themeBtn">Switch Theme</button>
// <div id="content" class="light-theme">
//   <h1 id="heading">My Website</h1>
//   <p>Some content here...</p>
// </div>

let themeBtn = document.getElementById('themeBtn');
let content = document.getElementById('content');

themeBtn.addEventListener('click', function() {
  // Toggle between light and dark theme
  if (content.classList.contains('light-theme')) {
    content.classList.replace('light-theme', 'dark-theme');
    content.style.backgroundColor = '#222';
    content.style.color = 'white';
    themeBtn.textContent = 'Light Mode';
  } else {
    content.classList.replace('dark-theme', 'light-theme');
    content.style.backgroundColor = 'white';
    content.style.color = 'black';
    themeBtn.textContent = 'Dark Mode';
  }
});
```

Happy coding! 🚀