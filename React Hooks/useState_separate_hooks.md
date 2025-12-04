# React State Hook: Separate Hooks for Separate States

## Introduction

When managing state in React, you have the flexibility to organize your data in the way that makes the most sense. While grouping related data can be helpful, keeping separate state variables for data that changes independently makes your code simpler, more maintainable, and less error-prone.

---

## The Problem: One Large State Object

### Example: Complex Nested State

```jsx
function Subject() {
  const [state, setState] = useState({
    currentGrade: 'B',
    classmates: ['Hasan', 'Sam', 'Emma'],
    classDetails: {topic: 'Math', teacher: 'Ms. Barry', room: 201},
    exams: [{unit: 1, score: 91}, {unit: 2, score: 88}]
  });
}
```

**What's the problem?**
- Everything is bundled together in one object
- Updating one piece of data requires copying all the others
- Complex nested structure
- Easy to make mistakes

### Updating Nested State: The Messy Way

Let's say we need to update an exam score. Here's what we'd have to do:

```jsx
// Update exam score for unit 2
setState((prev) => ({
  ...prev,  // Copy currentGrade, classmates, classDetails, exams
  exams: prev.exams.map((exam) => {
    if (exam.unit === updatedExam.unit) {
      return { 
        ...exam,  // Copy unit
        score: updatedExam.score  // Update score
      };
    } else {
      return exam;  // Keep other exams unchanged
    }
  }),
}));
```

**Why this is problematic:**
- **Complex code** - Hard to read and understand
- **Error-prone** - Easy to forget spreading something
- **Inefficient** - Copying data that hasn't changed
- **Hard to maintain** - Changes require touching lots of code
- **Difficult to test** - Complex logic needs many test cases
- **Poor reusability** - Can't easily reuse this logic elsewhere

---

## The Solution: Separate State Variables

### Refactored Example: Multiple State Variables

```jsx
function Subject() {
  // Separate state for each independent piece of data
  const [currentGrade, setGrade] = useState('B');
  const [classmates, setClassmates] = useState(['Hasan', 'Sam', 'Emma']);
  const [classDetails, setClassDetails] = useState({
    topic: 'Math', 
    teacher: 'Ms. Barry', 
    room: 201
  });
  const [exams, setExams] = useState([
    {unit: 1, score: 91}, 
    {unit: 2, score: 88}
  ]);
}
```

**Benefits:**
- Each piece of data has its own state variable
- Clear, descriptive names for each state
- Easy to update one without affecting others
- Simple, readable code

### Updating with Separate State: The Clean Way

Now updating an exam score is much simpler:

```jsx
// Update exam score - much cleaner!
setExams(prev => prev.map(exam => 
  exam.unit === updatedExam.unit 
    ? { ...exam, score: updatedExam.score }
    : exam
));
```

**Why this is better:**
- **Simpler code** - Only touches what needs to change
- **Less error-prone** - No need to copy unrelated data
- **More efficient** - Only updates the relevant state
- **Easier to maintain** - Changes are localized
- **Easier to test** - Each update is independent
- **More reusable** - Logic is focused and portable

---

## Complete Comparison Example

Let's see the difference in a complete component:

### ❌ Before: Single State Object (Complex)

```jsx
function Subject() {
  const [state, setState] = useState({
    currentGrade: 'B',
    classmates: ['Hasan', 'Sam', 'Emma'],
    classDetails: {topic: 'Math', teacher: 'Ms. Barry', room: 201},
    exams: [{unit: 1, score: 91}, {unit: 2, score: 88}]
  });

  // Complex: Update grade
  const updateGrade = (newGrade) => {
    setState(prev => ({
      ...prev,
      currentGrade: newGrade
    }));
  };

  // Complex: Add classmate
  const addClassmate = (name) => {
    setState(prev => ({
      ...prev,
      classmates: [...prev.classmates, name]
    }));
  };

  // Complex: Change room
  const changeRoom = (newRoom) => {
    setState(prev => ({
      ...prev,
      classDetails: {
        ...prev.classDetails,
        room: newRoom
      }
    }));
  };

  // Complex: Update exam score
  const updateExamScore = (unit, newScore) => {
    setState(prev => ({
      ...prev,
      exams: prev.exams.map(exam =>
        exam.unit === unit ? { ...exam, score: newScore } : exam
      )
    }));
  };

  return (
    <div>
      <h2>Current Grade: {state.currentGrade}</h2>
      <p>Classmates: {state.classmates.join(', ')}</p>
      <p>Topic: {state.classDetails.topic}</p>
      <p>Teacher: {state.classDetails.teacher}</p>
      <p>Room: {state.classDetails.room}</p>
      {state.exams.map(exam => (
        <p key={exam.unit}>Unit {exam.unit}: {exam.score}</p>
      ))}
    </div>
  );
}
```

### ✅ After: Separate State Variables (Simple)

```jsx
function Subject() {
  const [currentGrade, setGrade] = useState('B');
  const [classmates, setClassmates] = useState(['Hasan', 'Sam', 'Emma']);
  const [classDetails, setClassDetails] = useState({
    topic: 'Math', 
    teacher: 'Ms. Barry', 
    room: 201
  });
  const [exams, setExams] = useState([
    {unit: 1, score: 91}, 
    {unit: 2, score: 88}
  ]);

  // Simple: Update grade
  const updateGrade = (newGrade) => {
    setGrade(newGrade);  // That's it!
  };

  // Simple: Add classmate
  const addClassmate = (name) => {
    setClassmates(prev => [...prev, name]);  // Clean and focused
  };

  // Simple: Change room
  const changeRoom = (newRoom) => {
    setClassDetails(prev => ({
      ...prev,
      room: newRoom
    }));
  };

  // Simple: Update exam score
  const updateExamScore = (unit, newScore) => {
    setExams(prev => prev.map(exam =>
      exam.unit === unit ? { ...exam, score: newScore } : exam
    ));
  };

  return (
    <div>
      <h2>Current Grade: {currentGrade}</h2>
      <p>Classmates: {classmates.join(', ')}</p>
      <p>Topic: {classDetails.topic}</p>
      <p>Teacher: {classDetails.teacher}</p>
      <p>Room: {classDetails.room}</p>
      {exams.map(exam => (
        <p key={exam.unit}>Unit {exam.unit}: {exam.score}</p>
      ))}
    </div>
  );
}
```

**Key differences:**
- Simpler update functions
- No need to spread unrelated state
- Clearer what each function does
- Easier to read and understand

---

## When to Use Separate State Variables

### ✅ Use Separate State Variables When:

1. **Data changes independently**
   ```jsx
   const [name, setName] = useState('');
   const [email, setEmail] = useState('');
   const [age, setAge] = useState(0);
   // These change separately based on different user actions
   ```

2. **Different update patterns**
   ```jsx
   const [count, setCount] = useState(0);        // Increments
   const [items, setItems] = useState([]);       // Adds/removes items
   const [isOpen, setIsOpen] = useState(false);  // Toggles
   // Each has its own update logic
   ```

3. **Data has different purposes**
   ```jsx
   const [userData, setUserData] = useState({});    // User information
   const [settings, setSettings] = useState({});    // App settings
   const [preferences, setPreferences] = useState({}); // UI preferences
   // Different concerns, separate state
   ```

4. **Simplifies component logic**
   ```jsx
   // Separate states make intent clear
   const [loading, setLoading] = useState(false);
   const [error, setError] = useState(null);
   const [data, setData] = useState([]);
   ```

5. **State used in different parts of component**
   ```jsx
   const [searchQuery, setSearchQuery] = useState('');  // Used in header
   const [results, setResults] = useState([]);          // Used in body
   const [selectedItem, setSelectedItem] = useState(null); // Used in sidebar
   ```

---

## When to Group State Together

### ✅ Use a Single State Object When:

1. **Data always changes together**
   ```jsx
   // These coordinates always update together
   const [position, setPosition] = useState({ x: 0, y: 0 });
   ```

2. **Data represents a single entity**
   ```jsx
   // All properties describe one user
   const [user, setUser] = useState({
     id: 1,
     name: 'John',
     email: 'john@example.com'
   });
   ```

3. **Data passes between components as a unit**
   ```jsx
   // Form data sent together to API
   const [formData, setFormData] = useState({
     firstName: '',
     lastName: '',
     email: ''
   });
   ```

4. **Related fields for validation**
   ```jsx
   // Password fields validated together
   const [passwords, setPasswords] = useState({
     password: '',
     confirmPassword: ''
   });
   ```

---

## Practical Examples

### Example 1: User Profile vs Settings

```jsx
function UserDashboard() {
  // ❌ Bad: Everything in one object
  const [state, setState] = useState({
    name: 'John',
    email: 'john@example.com',
    theme: 'dark',
    notifications: true,
    language: 'en'
  });

  // ✅ Good: Separate by purpose
  const [profile, setProfile] = useState({
    name: 'John',
    email: 'john@example.com'
  });
  
  const [settings, setSettings] = useState({
    theme: 'dark',
    notifications: true,
    language: 'en'
  });

  // Now updates are cleaner
  const updateProfile = (field, value) => {
    setProfile(prev => ({ ...prev, [field]: value }));
  };

  const updateSettings = (field, value) => {
    setSettings(prev => ({ ...prev, [field]: value }));
  };
}
```

### Example 2: Shopping Cart

```jsx
function ShoppingCart() {
  // Separate states for independent concerns
  const [items, setItems] = useState([]);           // Cart items
  const [discount, setDiscount] = useState(0);      // Discount code
  const [shipping, setShipping] = useState(null);   // Shipping method
  const [address, setAddress] = useState({          // Shipping address
    street: '',
    city: '',
    zipCode: ''
  });

  // Each update function is simple and focused
  const addItem = (item) => {
    setItems(prev => [...prev, item]);
  };

  const applyDiscount = (code) => {
    setDiscount(code);
  };

  const selectShipping = (method) => {
    setShipping(method);
  };

  const updateAddress = (field, value) => {
    setAddress(prev => ({ ...prev, [field]: value }));
  };
}
```

### Example 3: Todo App

```jsx
function TodoApp() {
  // ✅ Good: Separate states
  const [todos, setTodos] = useState([]);       // Todo list
  const [filter, setFilter] = useState('all'); // Current filter
  const [input, setInput] = useState('');       // Input field value

  // Each function has one clear purpose
  const addTodo = () => {
    setTodos(prev => [...prev, { id: Date.now(), text: input, completed: false }]);
    setInput(''); // Clear input after adding
  };

  const toggleTodo = (id) => {
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const changeFilter = (newFilter) => {
    setFilter(newFilter);
  };

  // vs. ❌ Bad: Everything in one state
  // const [state, setState] = useState({ todos: [], filter: 'all', input: '' });
  // Updates would require spreading entire state object every time
}
```

### Example 4: Form with Validation

```jsx
function RegistrationForm() {
  // Form data grouped together (changes as a unit)
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: ''
  });

  // Separate state for validation (different concern)
  const [errors, setErrors] = useState({});
  
  // Separate state for submission status (different concern)
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = ({ target }) => {
    const { name, value } = target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const validateField = (field, value) => {
    // Validation logic...
    setErrors(prev => ({ ...prev, [field]: errorMessage }));
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);
    // Submit logic...
    setIsSubmitting(false);
  };
}
```

---

## Guidelines for Organizing State

### Ask Yourself:

1. **Does this data change together?**
   - Yes → Group in one object
   - No → Separate state variables

2. **Do I need to copy lots of data to update one field?**
   - Yes → Consider splitting the state
   - No → Current structure is good

3. **Is the update logic getting complex?**
   - Yes → Split into smaller state pieces
   - No → Keep as is

4. **Will this data be passed to another component together?**
   - Yes → Keep grouped
   - No → Can be separate

5. **Does separating state make the code clearer?**
   - Yes → Separate it
   - No → Keep grouped

---

## The Advantages of Separate State Variables

### 1. Simpler Code

```jsx
// Complex
setState(prev => ({ ...prev, name: newName }));

// Simple
setName(newName);
```

### 2. Easier to Read

```jsx
// Clear what's being updated
setEmail('newemail@example.com');
setAge(30);
setTheme('dark');
```

### 3. Better Testing

```jsx
// Each update can be tested independently
test('updates email', () => {
  // Only test setEmail
});

test('updates age', () => {
  // Only test setAge
});
```

### 4. More Reusable

```jsx
// Logic for managing a counter
const [count, setCount] = useState(0);
const increment = () => setCount(prev => prev + 1);

// Can be extracted to a custom hook
function useCounter() {
  const [count, setCount] = useState(0);
  const increment = () => setCount(prev => prev + 1);
  return { count, increment };
}
```

### 5. Less Error-Prone

```jsx
// No risk of forgetting to spread
setGrade('A');  // Just works

// vs.
setState(prev => ({
  ...prev,  // Easy to forget this!
  grade: 'A'
}));
```

---

## Common Patterns

### Pattern 1: UI State vs Data State

```jsx
function DataTable() {
  // Data
  const [data, setData] = useState([]);
  
  // UI state (separate concerns)
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [sortBy, setSortBy] = useState('name');
  const [selectedRows, setSelectedRows] = useState([]);
}
```

### Pattern 2: Temporary vs Persistent State

```jsx
function SearchBox() {
  // Temporary (changes constantly)
  const [searchInput, setSearchInput] = useState('');
  
  // Persistent (only changes on submit)
  const [activeSearch, setActiveSearch] = useState('');
  const [results, setResults] = useState([]);
}
```

### Pattern 3: Local vs Shared State

```jsx
function Component() {
  // Local to this component
  const [isOpen, setIsOpen] = useState(false);
  const [selectedTab, setSelectedTab] = useState(0);
  
  // Shared with other components (might be lifted up later)
  const [sharedData, setSharedData] = useState([]);
}
```

---

## Freedom to Organize

**The wonderful thing about working with Hooks:**

You have the **freedom to organize your data** in the way that makes the most sense to you and your application!

React doesn't force you to structure your state in any particular way. You can:
- Start with separate states and combine them later if needed
- Start with grouped state and split it if it gets complex
- Mix and match approaches in the same component
- Refactor as your component evolves

**The key principle:** Keep your data models as **simple as possible** for your use case.

---

## Key Takeaways

1. **Keep data models simple** - Simplest structure that works
2. **Separate what changes separately** - Independent updates, independent state
3. **Group what changes together** - Related data, single state
4. **Simpler code is better** - Fewer bugs, easier maintenance
5. **Multiple useState calls are fine** - Use as many as you need
6. **Avoid deep nesting** - Complex updates indicate need to split state
7. **You have freedom** - Organize in the way that makes sense
8. **Refactor as needed** - State organization can evolve

---

## Practice Exercises

Try refactoring these scenarios:

1. **Blog Post Editor** - Separate post content, metadata, and UI state
2. **Multi-Step Form** - Organize form data, step state, and validation
3. **Dashboard** - Split user info, settings, notifications, and data
4. **Game Component** - Separate game state, player data, and UI controls
5. **Chat Application** - Organize messages, users, and UI state

---

## Next Steps

- Learn about custom hooks for organizing related state logic
- Explore state management patterns for larger applications
- Study when to lift state up vs keep it local
- Understand context API for sharing state across components
