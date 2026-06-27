# State Management Basics

> **Goal:** Understand State from scratch, why React needs State, how State differs from Props, useState Hook, Updating State, State Immutability, Functional State Updates, Managing Complex State Objects, Lifting State Up, internal working, real-world examples, best practices, and interview questions.

---

# 1. What is State?

State is data that belongs to a component and can change over time.

---

Think of State as:

```text
Component Memory
```

---

Example:

```text
Counter Value

User Input

Theme

Cart Items

Login Status
```

---

All of these change during application execution.

---

React stores such changing data in:

```text
State
```

---

# 2. Why Do We Need State?

Without State:

```jsx
function Counter() {

 let count = 0;

 return (

  <div>

   <h1>{count}</h1>

   <button>

    Increment

   </button>

  </div>

 );
}
```

---

Problem:

```text
count changes

UI does not update
```

---

React doesn't track normal variables.

---

Need:

```text
Reactive Data
```

---

That is:

```text
State
```

---

# 3. State Creates Reactive UI

Example:

```jsx
const [count, setCount] =
useState(0);
```

---

When:

```jsx
setCount(1);
```

---

React automatically:

```text
Re-renders Component
```

---

UI updates.

---

# 4. Real Life Example

Imagine:

```text
Instagram Likes
```

---

When user clicks:

```text
❤️ Like
```

---

Likes increase.

---

UI updates instantly.

---

Possible because:

```text
Likes Stored In State
```

---

# 5. State Characteristics

State is:

```text
Mutable Through React APIs

Private To Component

Triggers Re-render

Managed By React
```

---

# 6. State Flow

```text
State Changes
       ↓

React Re-renders
       ↓

Virtual DOM Updated
       ↓

Diffing
       ↓

Real DOM Updated
```

---

# State vs Props

---

# 7. State vs Props Overview

One of the most important React interview topics.

---

Both store data.

---

But purpose differs.

---

# 8. Props

Props are:

```text
Passed From Parent

Read Only

Immutable
```

---

Example:

```jsx
<User name="John" />
```

---

# 9. State

State is:

```text
Managed By Component

Can Change

Triggers Re-render
```

---

Example:

```jsx
const [count, setCount] =
useState(0);
```

---

# 10. State vs Props Comparison

| Feature | Props | State |
|----------|----------|----------|
| Ownership | Parent | Component |
| Mutable | No | Yes |
| Read Only | Yes | No |
| Causes Re-render | When Parent Changes | When State Changes |
| Purpose | Pass Data | Store Dynamic Data |
| Updated By | Parent | Component |

---

# 11. Visualization

Props:

```text
Parent
  ↓

Child
```

---

State:

```text
Component

Own Data
```

---

# 12. Example

Props:

```jsx
<User name="John" />
```

---

State:

```jsx
const [name, setName]
= useState("John");
```

---

Difference:

```text
Props → External Data

State → Internal Data
```

---

# useState Hook

---

# 13. What is useState?

useState is a React Hook used to create State in Functional Components.

---

Before Hooks:

```text
Only Class Components
Could Have State
```

---

After Hooks:

```text
Functional Components
Can Have State
```

---

# 14. Importing useState

```jsx
import { useState }
from "react";
```

---

# 15. Basic Syntax

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

# 16. What Does useState Return?

Returns:

```javascript
[
 currentValue,
 updateFunction
]
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

Equivalent:

```text
count = 0

setCount = updater
```

---

# 17. Counter Example

```jsx
import { useState }
from "react";

function Counter() {

 const [

  count,

  setCount

 ] = useState(0);

 return (

  <div>

   <h1>{count}</h1>

   <button

    onClick={() =>

      setCount(count + 1)
    }

   >

    Increment

   </button>

  </div>

 );
}
```

---

Output:

```text
0
1
2
3
...
```

---

# 18. Multiple State Variables

```jsx
const [name, setName]
= useState("");

const [age, setAge]
= useState(25);

const [city, setCity]
= useState("Bangalore");
```

---

Perfectly valid.

---

# Updating State

---

# 19. How State Updates Work

Wrong:

```jsx
count = count + 1;
```

---

React won't track this.

---

Correct:

```jsx
setCount(count + 1);
```

---

Always use:

```text
Setter Function
```

---

# 20. State Update Flow

```text
setCount(5)
      ↓

State Updated
      ↓

Component Re-rendered
      ↓

UI Updated
```

---

# 21. String State Example

```jsx
const [

 name,

 setName

] = useState("John");
```

---

Update:

```jsx
setName("David");
```

---

Output:

```text
David
```

---

# 22. Boolean State Example

```jsx
const [

 isLoggedIn,

 setIsLoggedIn

] = useState(false);
```

---

Update:

```jsx
setIsLoggedIn(true);
```

---

# 23. Toggle Example

```jsx
<button

 onClick={() =>

  setIsLoggedIn(
   !isLoggedIn
  )
 }

>

 Toggle

</button>
```

---

Switches:

```text
true ↔ false
```

---

# State Immutability

---

# 24. What is Immutability?

Immutability means:

```text
Do Not Modify Existing State

Create New State
```

---

Very important React concept.

---

# 25. Wrong Approach

```jsx
user.name = "David";
```

---

Direct mutation.

---

Avoid.

---

# 26. Correct Approach

```jsx
setUser({

 ...user,

 name: "David"
});
```

---

Creates:

```text
New Object
```

---

React detects change.

---

# 27. Why React Requires Immutability?

React compares:

```text
Old State

vs

New State
```

---

If reference changes:

```text
Re-render
```

---

If same reference:

```text
No Change Detected
```

---

# 28. Example

Wrong:

```jsx
const user = {

 name: "John"
};

user.name = "David";
```

---

Reference:

```text
Same Object
```

---

React may skip update.

---

# 29. Correct

```jsx
setUser({

 ...user,

 name: "David"
});
```

---

Reference:

```text
New Object
```

---

React re-renders.

---

# Functional State Updates

---

# 30. Problem With Direct Updates

Example:

```jsx
setCount(count + 1);

setCount(count + 1);
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

React batches updates.

---

# 31. Functional Update Solution

```jsx
setCount(

 previous =>

 previous + 1

);
```

---

# 32. Multiple Updates

```jsx
setCount(prev => prev + 1);

setCount(prev => prev + 1);
```

---

Result:

```text
+2
```

---

Correct.

---

# 33. Why Functional Updates Work?

React provides:

```text
Latest State Value
```

---

Example:

```jsx
setCount(prev => {

 return prev + 1;
});
```

---

No stale values.

---

# 34. Interview Rule

Whenever next state depends on previous state:

Use:

```jsx
setState(prev => ...)
```

---

Recommended.

---

# Managing Complex State Objects

---

# 35. State as Object

Example:

```jsx
const [

 user,

 setUser

] = useState({

 name: "John",

 age: 25
});
```

---

# 36. Updating One Property

Wrong:

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

Because object replaced.

---

# 37. Correct Approach

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

# 38. Nested Object Example

```jsx
const [

 employee,

 setEmployee

] = useState({

  name: "John",

  address: {

   city: "Bangalore"
  }
});
```

---

# 39. Updating Nested State

```jsx
setEmployee({

 ...employee,

 address: {

  ...employee.address,

  city: "Mumbai"
 }
});
```

---

Creates:

```text
New Nested Objects
```

---

# 40. State as Array

Example:

```jsx
const [

 users,

 setUsers

] = useState([]);
```

---

# 41. Adding Item

```jsx
setUsers([

 ...users,

 "John"
]);
```

---

Result:

```javascript
[
 "John"
]
```

---

# 42. Removing Item

```jsx
setUsers(

 users.filter(

  user =>

  user !== "John"
 )
);
```

---

Creates new array.

---

# 43. Updating Array Item

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

Immutable update.

---

# Lifting State Up

---

# 44. What is Lifting State Up?

One of the most important React interview topics.

---

Definition:

> Moving State to the nearest common parent component so multiple child components can share the same data.

---

# 45. Why Is It Needed?

Suppose:

```text
Sibling Components
Need Same Data
```

---

Structure:

```text
App
│
├── Search
│
└── ProductList
```

---

Need:

```text
Search Text
```

in both.

---

# 46. Wrong Approach

State inside:

```jsx
Search
```

---

ProductList cannot access it.

---

Because:

```text
State Is Local
```

---

# 47. Solution

Move state to parent.

---

Parent:

```jsx
App
```

---

Contains:

```jsx
const [

 search,

 setSearch

]
= useState("");
```

---

# 48. Pass State Down

```jsx
<Search

 search={search}

 setSearch={setSearch}

/>
```

---

```jsx
<ProductList

 search={search}

/>
```

---

Now both share same data.

---

# 49. Visualization

Before:

```text
Search

Own State
```

---

```text
ProductList

No Access
```

---

After:

```text
App

State
 ↓

Search

 ↓

ProductList
```

---

Shared successfully.

---

# 50. Real World Example

Shopping Cart.

---

Components:

```text
Navbar

ProductList

Cart
```

---

Need:

```text
Cart Count
```

everywhere.

---

Store state in:

```text
Common Parent
```

---

Pass via props.

---

# 51. Benefits of Lifting State Up

```text
Single Source Of Truth

Shared State

Consistent Data

Predictable Behavior
```

---

# 52. Single Source of Truth

Very important concept.

---

Meaning:

```text
One Place

Owns State
```

---

Avoid:

```text
Duplicate State
```

---

Across components.

---

# Interview Questions

---

### Q1. What is State?

```text
Data managed by a component
that can change over time.
```

---

### Q2. Difference between State and Props?

Props:

```text
External
Read Only
```

---

State:

```text
Internal
Mutable
```

---

### Q3. What is useState?

```text
Hook used to create
state in functional components.
```

---

### Q4. What does useState return?

```javascript
[
 state,
 setState
]
```

---

### Q5. Why should State be immutable?

```text
React detects changes
through new references.
```

---

### Q6. What is Functional State Update?

```jsx
setCount(

 prev =>

 prev + 1
)
```

---

Used when next state depends on previous state.

---

### Q7. Why not directly mutate state?

```text
React may not detect changes.
```

---

### Q8. How do you update an object state?

```jsx
setUser({

 ...user,

 name: "David"
});
```

---

### Q9. How do you update an array state?

```jsx
setUsers([

 ...users,

 newUser
]);
```

---

### Q10. What is Lifting State Up?

```text
Moving state to a common parent
so multiple components can share it.
```

---

### Q11. What is Single Source of Truth?

```text
One component owns state.
```

---

# Real World Example

### Shopping Cart

```text
App
│
├── Navbar
│
├── ProductList
│
└── Cart
```

---

State:

```jsx
const [

 cartItems,

 setCartItems

] = useState([]);
```

---

Stored in:

```text
App Component
```

---

Shared with:

```text
Navbar

ProductList

Cart
```

---

Using props.

---

# Memory Tricks

### State

```text
Component Memory
```

---

### Props

```text
Parent Data
```

---

### State

```text
Component Data
```

---

### useState

```text
Create State
```

---

### Immutability

```text
Create New State
Don't Modify Old State
```

---

### Functional Update

```text
Use Previous State
```

---

### Lifting State Up

```text
Move State To Parent
```

---

### Single Source Of Truth

```text
One Owner Of State
```

---

# Quick Revision

```text
State Management Basics
-----------------------

State
-----

Component Memory

Can Change

Triggers Re-render

Props vs State
--------------

Props
 ↓
Read Only

State
 ↓
Mutable

useState
--------

const [

 state,

 setState

]
=
useState(value)

State Update
------------

setState(newValue)

Immutability
------------

❌ Mutate

user.name = "David"

✔ Create New Object

setUser({

 ...user,

 name: "David"
})

Functional Updates
------------------

setCount(

 prev =>

 prev + 1
)

Use When:
Next State Depends
On Previous State

Complex State
-------------

Objects

Arrays

Nested Objects

Use:

Spread Operator

Lifting State Up
----------------

Move State
To Common Parent

Benefits
---------

✔ Shared State

✔ Single Source Of Truth

✔ Predictable Data Flow

Remember
---------

Props
 ↓
Parent → Child

State
 ↓
Component Memory

useState
 ↓
Create State

Immutability
 ↓
New Reference

Lifting State Up
 ↓
Shared State
```
