# useState Hook

> **Goal:** Understand useState from scratch, why React created it, how state works internally, React Fiber storage, state updates, batching, functional updates, object state, array state, re-rendering, common mistakes, best practices, real-world examples, interview questions, and React internals.

---

# 1. What Problem Does useState Solve?

Before Hooks existed, Functional Components were:

```text
Stateless
```

---

Example:

```jsx
function Counter() {

 return <h1>0</h1>;
}
```

---

Problem:

```text
Cannot Store Data

Cannot Update UI

Cannot Remember Values
```

---

Suppose:

```text
Button Click
```

should increase:

```text
Count
```

---

Without state:

```jsx
let count = 0;

function increment() {

 count++;
}
```

---

Problem:

```text
UI Does Not Update
```

---

Why?

Because React does not track:

```text
Normal Variables
```

---

Need:

```text
Data Storage
+
Automatic UI Updates
```

---

React introduced:

```text
useState()
```

---

# 2. What is State?

Definition:

> State is data managed by React that can change over time and trigger UI updates.

---

Simple Meaning:

```text
State = Memory Of Component
```

---

Examples:

```text
Counter Value

User Name

Theme

Cart Items

Form Data

Loading Status
```

---

# 3. Real World Example

Instagram Like Button

---

Before Like:

```text
❤️ 0 Likes
```

---

After Like:

```text
❤️ 1 Like
```

---

Data changed.

---

UI changed.

---

This is:

```text
State
```

---

# 4. Why Normal Variables Don't Work?

Example:

```jsx
function Counter() {

 let count = 0;

 function increment() {

  count++;
 }

 return (
  <>
   <h1>{count}</h1>

   <button
    onClick={increment}
   >
    Increment
   </button>
  </>
 );
}
```

---

Click button.

---

Count changes:

```text
0 → 1
```

---

But UI:

```text
Still Shows 0
```

---

Reason:

React doesn't know:

```text
Variable Changed
```

---

Need:

```text
State Management
```

---

# 5. What is useState?

Definition:

> useState is a React Hook that allows functional components to store and update state.

---

Syntax:

```jsx
const [

 state,

 setState

] = useState(initialValue);
```

---

Example:

```jsx
const [

 count,

 setCount

] = useState(0);
```

---

# 6. Understanding the Syntax

```jsx
const [

 count,

 setCount

] = useState(0);
```

---

React returns:

```jsx
[
 currentState,

 updateFunction
]
```

---

Example:

```jsx
[
 0,

 setCount
]
```

---

Destructuring:

```jsx
const [

 count,

 setCount

]
```

---

Now:

```text
count
```

contains value.

---

```text
setCount
```

updates value.

---

# 7. First useState Example

```jsx
import { useState }
from "react";

function Counter() {

 const [

  count,

  setCount

 ] = useState(0);

 return (

  <>
   <h1>{count}</h1>

   <button

    onClick={() =>

     setCount(
      count + 1
     )

    }

   >

    Increment

   </button>

  </>

 );
}
```

---

Output:

```text
0
```

---

Click:

```text
Increment
```

---

Output:

```text
1
```

---

# 8. How useState Works Internally

Very important interview topic.

---

When component renders:

```jsx
useState(0)
```

---

React stores:

```text
0
```

inside:

```text
Fiber Node
```

---

React creates:

```text
State Slot
```

---

Visualization:

```text
Component Fiber

State[0] = 0
```

---

React remembers:

```text
Current State
```

between renders.

---

# 9. Internal Storage

Imagine:

```jsx
const [

 count,

 setCount

] = useState(0);
```

---

React internally:

```text
Fiber

 └── Hooks

      └── State

            0
```

---

Not actual code.

---

Conceptual representation.

---

# 10. What Happens During setState?

Example:

```jsx
setCount(1);
```

---

React:

### Step 1

Stores new value.

---

```text
1
```

---

### Step 2

Marks component dirty.

---

```text
Needs Re-render
```

---

### Step 3

Runs component again.

---

### Step 4

Updates Virtual DOM.

---

### Step 5

Updates Real DOM.

---

Flow:

```text
setState
    ↓

State Updated
    ↓

Re-render
    ↓

Virtual DOM
    ↓

Diffing
    ↓

Real DOM Update
```

---

# 11. Why Component Re-renders?

Example:

```jsx
setCount(5);
```

---

React sees:

```text
State Changed
```

---

Must regenerate UI.

---

So component executes again.

---

This is called:

```text
Re-render
```

---

# 12. Render Cycle Visualization

Initial Render:

```text
count = 0
```

---

Click:

```jsx
setCount(1)
```

---

New Render:

```text
count = 1
```

---

UI updates.

---

# 13. Multiple State Variables

Example:

```jsx
const [

 name,

 setName

] = useState("");

const [

 age,

 setAge

] = useState(0);
```

---

React creates:

```text
State Slot 1

State Slot 2
```

---

Visualization:

```text
State[0] = ""

State[1] = 0
```

---

# 14. Updating State

Example:

```jsx
setCount(10);
```

---

State:

```text
0 → 10
```

---

React re-renders.

---

# 15. Increment Example

```jsx
setCount(
 count + 1
);
```

---

Current:

```text
5
```

---

New:

```text
6
```

---

# 16. Decrement Example

```jsx
setCount(
 count - 1
);
```

---

Current:

```text
5
```

---

New:

```text
4
```

---

# Functional State Updates

---

# 17. Problem

Example:

```jsx
setCount(
 count + 1
);

setCount(
 count + 1
);
```

---

Expected:

```text
+2
```

---

Actual:

```text
+1
```

---

Why?

Because both updates use:

```text
Same Old State
```

---

# 18. Correct Solution

Use:

```text
Functional Update
```

---

Example:

```jsx
setCount(prev =>

 prev + 1

);

setCount(prev =>

 prev + 1

);
```

---

Result:

```text
+2
```

---

# 19. How Functional Updates Work

First:

```text
0 → 1
```

---

Second:

```text
1 → 2
```

---

Uses latest value.

---

# 20. When to Use Functional Updates?

Use when:

```text
New State Depends
On Previous State
```

---

Examples:

```text
Counter

Likes

Votes

Cart Quantity
```

---

# Object State

---

# 21. Storing Objects

Example:

```jsx
const [

 user,

 setUser

]
=
useState({

 name: "John",

 age: 25
});
```

---

# 22. Wrong Update

```jsx
setUser({

 name: "David"
});
```

---

Result:

```text
age removed
```

---

Because entire object replaced.

---

# 23. Correct Update

```jsx
setUser({

 ...user,

 name: "David"

});
```

---

Result:

```javascript
{
 name: "David",

 age: 25
}
```

---

# 24. Why Spread Operator?

React does NOT merge objects automatically.

---

Unlike old class components.

---

Need:

```jsx
...user
```

---

# Array State

---

# 25. Storing Arrays

```jsx
const [

 users,

 setUsers

]
=
useState([]);
```

---

# 26. Adding Item

```jsx
setUsers([

 ...users,

 "John"

]);
```

---

Result:

```javascript
["John"]
```

---

# 27. Adding Multiple Items

```jsx
setUsers([

 ...users,

 "John",

 "David"

]);
```

---

# 28. Removing Item

```jsx
setUsers(

 users.filter(

  user =>

  user !== "John"

 )

);
```

---

# 29. Updating Item

```jsx
setUsers(

 users.map(user =>

  user === "John"

   ? "David"

   : user

 )

);
```

---

# State Immutability

---

# 30. What is Immutability?

Definition:

```text
Never Modify Existing State
Create New State
```

---

# 31. Wrong Example

```jsx
users.push("John");
```

---

Bad.

---

Mutates state directly.

---

# 32. Correct Example

```jsx
setUsers([

 ...users,

 "John"

]);
```

---

Creates new array.

---

Good.

---

# 33. Why Immutability Matters?

React detects:

```text
Reference Changes
```

---

New object:

```text
Re-render
```

---

Same object:

```text
No Change Detected
```

---

# Lazy Initialization

---

# 34. Problem

Expensive computation:

```jsx
useState(

 heavyCalculation()

);
```

---

Runs every render.

---

Bad.

---

# 35. Solution

```jsx
useState(() =>

 heavyCalculation()

);
```

---

Runs:

```text
Only Once
```

---

Initial render only.

---

# State Batching

---

# 36. What is Batching?

React groups multiple updates.

---

Example:

```jsx
setCount(1);

setAge(25);
```

---

React:

```text
Single Re-render
```

---

Instead of:

```text
Two Re-renders
```

---

Performance improvement.

---

# 37. Automatic Batching

React 18 introduced:

```text
Automatic Batching
```

---

Works for:

```text
Events

Promises

Timeouts

Async Calls
```

---

# Common Mistakes

---

# 38. Direct State Mutation

Wrong:

```jsx
user.name = "John";
```

---

Correct:

```jsx
setUser({

 ...user,

 name: "John"
});
```

---

# 39. Expecting Immediate Updates

Wrong:

```jsx
setCount(1);

console.log(count);
```

---

May print:

```text
Old Value
```

---

State updates are scheduled.

---

# 40. Using State for Constants

Wrong:

```jsx
const [

 pi

] = useState(3.14);
```

---

Use:

```jsx
const pi = 3.14;
```

---

# Real World Example

# Shopping Cart

---

State:

```jsx
const [

 cart,

 setCart

]
=
useState([]);
```

---

Add Product:

```jsx
setCart([

 ...cart,

 product

]);
```

---

Remove Product:

```jsx
setCart(

 cart.filter(

  item =>

  item.id !== id

 )

);
```

---

Update Quantity:

```jsx
setCart(

 cart.map(item =>

  item.id === id

   ? {

      ...item,

      quantity:
      item.quantity + 1

     }

   : item

 )

);
```

---

# React Fiber and useState

Advanced Interview Topic.

---

# 41. What is Fiber?

React's internal architecture.

---

Each component:

```text
Fiber Node
```

---

Stores:

```text
State

Props

Effects

Hooks
```

---

# 42. useState Storage

Internally:

```text
Fiber
   ↓

Hook List
   ↓

State Values
```

---

React remembers:

```text
Hook Order
```

---

Very important.

---

# 43. Why Hooks Must Stay in Same Order?

React identifies hooks by:

```text
Position
```

---

Example:

```jsx
useState()

useEffect()
```

---

Changing order breaks mapping.

---

Reason for Hook Rules.

---

# Interview Questions

---

### Q1. What is useState?

```text
Hook used to manage
state in functional
components.
```

---

### Q2. Why not use variables?

```text
Variables do not
trigger re-renders.
```

---

### Q3. What does useState return?

```jsx
[
 state,

 setState
]
```

---

### Q4. What triggers re-render?

```text
State Update
```

---

### Q5. Why use functional updates?

```text
When new state depends
on previous state.
```

---

### Q6. Can useState store objects?

```text
Yes
```

---

### Q7. Can useState store arrays?

```text
Yes
```

---

### Q8. Why avoid mutation?

```text
React relies on
reference changes.
```

---

### Q9. What is lazy initialization?

```text
Initialize state
only once.
```

---

### Q10. What is batching?

```text
Multiple updates
combined into one
re-render.
```

---

# Memory Tricks

### useState

```text
Component Memory
```

---

### setState

```text
Update Memory
```

---

### Re-render

```text
State Change
      ↓
UI Change
```

---

### Functional Update

```text
Previous State
      ↓
New State
```

---

### Object Update

```text
Spread Operator
```

---

### Array Update

```text
Create New Array
```

---

### Immutability

```text
Never Modify

Always Replace
```

---

### Batching

```text
Many Updates

One Render
```

---

# Quick Revision

```text
useState
--------

Purpose:

Store Component State

Syntax:

const [

 state,

 setState

] = useState(value);

Returns:

1. Current State

2. Update Function

State Update Flow:

setState()
     ↓

State Change
     ↓

Re-render
     ↓

Virtual DOM
     ↓

UI Update

Supports:

✔ String
✔ Number
✔ Boolean
✔ Object
✔ Array

Important Topics:

✔ Functional Updates
✔ Immutability
✔ Lazy Initialization
✔ Batching
✔ Re-rendering
✔ Fiber Storage

Common Mistakes:

✘ Direct Mutation
✘ Expect Immediate Update
✘ Changing Hook Order

Remember:

State
 ↓
Component Memory

setState
 ↓
Update Memory

Re-render
 ↓
Refresh UI
```

---