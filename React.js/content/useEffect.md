# useEffect Hook

> **Goal:** Understand useEffect from scratch, why React created it, Side Effects, Dependency Array, Cleanup Functions, API Calls, Timers, Event Listeners, Component Lifecycle Mapping, React Fiber Internals, Common Mistakes, Best Practices, Real-World Examples, Interview Questions, and React Rendering Flow.

---

# 1. What Problem Does useEffect Solve?

React components are primarily responsible for:

```text
Rendering UI
```

---

Example:

```jsx
function App() {

 return <h1>Hello</h1>;
}
```

---

Rendering UI is simple.

---

But real applications need more.

---

Examples:

```text
API Calls

Database Requests

Timers

Subscriptions

Socket Connections

DOM Manipulation

Browser Events
```

---

These operations are called:

```text
Side Effects
```

---

Need a way to execute them.

---

React provides:

```text
useEffect()
```

---

# 2. What is a Side Effect?

Definition:

> Any operation that affects something outside the component rendering process.

---

Simple Meaning:

```text
Anything Other Than Returning JSX
```

---

# 3. Examples of Side Effects

---

### API Request

```jsx
fetch("/users")
```

---

### Timer

```jsx
setInterval()
```

---

### Local Storage

```jsx
localStorage.setItem()
```

---

### Event Listener

```jsx
window.addEventListener()
```

---

### WebSocket Connection

```jsx
socket.connect()
```

---

All are:

```text
Side Effects
```

---

# 4. Why Can't We Put Side Effects Directly Inside Component?

Wrong Example:

```jsx
function App() {

 fetch("/users");

 return <h1>Hello</h1>;
}
```

---

Problem:

Every render executes:

```text
fetch()
```

---

Result:

```text
Infinite API Calls

Performance Issues

Unexpected Behavior
```

---

Need:

```text
Controlled Execution
```

---

React created:

```text
useEffect()
```

---

# 5. What is useEffect?

Definition:

> useEffect is a React Hook used to perform side effects after rendering.

---

Simple Meaning:

```text
Run Code
After UI Render
```

---

# 6. Syntax

```jsx
useEffect(() => {

 // Side Effect

}, []);
```

---

Structure:

```jsx
useEffect(

 callback,

 dependencies

);
```

---

# 7. Understanding Parameters

### Callback Function

```jsx
() => {

 // Effect Logic
}
```

---

Contains:

```text
API Calls

Timers

Subscriptions

Etc.
```

---

### Dependency Array

```jsx
[]
```

---

Controls:

```text
When Effect Runs
```

---

# 8. First useEffect Example

```jsx
import {

 useEffect

} from "react";
```

---

```jsx
function App() {

 useEffect(() => {

  console.log(

   "Component Mounted"

  );

 }, []);

 return <h1>Hello</h1>;
}
```

---

Output:

```text
Component Mounted
```

---

Runs once.

---

# 9. Render Flow

```text
Component Render
        ↓

DOM Updated
        ↓

useEffect Runs
```

---

Very important.

---

# 10. Why "Effect"?

Because React performs:

```text
Render
```

first.

---

Then performs:

```text
Effect
```

after rendering.

---

# Dependency Array

---

# 11. What is Dependency Array?

One of the most important React interview topics.

---

Example:

```jsx
useEffect(() => {

 console.log("Run");

}, []);
```

---

Dependency array controls:

```text
Effect Execution
```

---

# 12. Three Possible Variations

```jsx
useEffect(() => {});
```

---

```jsx
useEffect(() => {}, []);
```

---

```jsx
useEffect(() => {}, [value]);
```

---

Each behaves differently.

---

# 13. No Dependency Array

```jsx
useEffect(() => {

 console.log("Run");

});
```

---

Runs:

```text
After Every Render
```

---

Flow:

```text
Render
 ↓

Effect

 ↓

Render

 ↓

Effect
```

---

# 14. Example

```jsx
function App() {

 const [

  count,

  setCount

 ] = useState(0);

 useEffect(() => {

  console.log("Run");

 });

 return (
  <button

   onClick={() =>

    setCount(

     count + 1

    )

   }
  >
   {count}
  </button>
 );
}
```

---

Every click:

```text
Effect Executes
```

---

# 15. Empty Dependency Array

```jsx
useEffect(() => {

 console.log("Mounted");

}, []);
```

---

Runs:

```text
Only Once
```

---

When component mounts.

---

Flow:

```text
Mount
 ↓

Effect
```

---

No further execution.

---

# 16. Dependency Array with Variables

```jsx
useEffect(() => {

 console.log(

  "Count Changed"

 );

}, [count]);
```

---

Runs only when:

```text
count changes
```

---

# 17. Example

```jsx
const [

 count,

 setCount

]
=
useState(0);
```

---

```jsx
useEffect(() => {

 console.log(

  count

 );

}, [count]);
```

---

Count changes:

```text
Effect Runs
```

---

# 18. Multiple Dependencies

```jsx
useEffect(() => {

 console.log("Run");

}, [

 count,

 name

]);
```

---

Runs when:

```text
count changes

OR

name changes
```

---

# Lifecycle Mapping

---

# 19. React Class Component Lifecycle

Old React:

```text
componentDidMount()

componentDidUpdate()

componentWillUnmount()
```

---

Hooks replaced them.

---

# 20. Component Did Mount

Equivalent:

```jsx
useEffect(() => {

 console.log("Mount");

}, []);
```

---

Runs once.

---

# 21. Component Did Update

Equivalent:

```jsx
useEffect(() => {

 console.log(

  "Updated"

 );

}, [count]);
```

---

Runs on updates.

---

# 22. Component Will Unmount

Equivalent:

```jsx
useEffect(() => {

 return () => {

  console.log(

   "Unmount"
  );

 };

}, []);
```

---

Cleanup function.

---

# Cleanup Function

---

# 23. What is Cleanup?

Definition:

> A function executed before effect re-runs or component unmounts.

---

Very important.

---

# 24. Syntax

```jsx
useEffect(() => {

 return () => {

  // Cleanup
 };

}, []);
```

---

# 25. Why Cleanup Needed?

Suppose:

```text
Timer Started
```

---

Component removed.

---

Timer still running.

---

Memory leak.

---

Need:

```text
Cleanup
```

---

# 26. Timer Example

```jsx
useEffect(() => {

 const timer =

 setInterval(() => {

  console.log("Run");

 }, 1000);

 return () => {

  clearInterval(
   timer
  );

 };

}, []);
```

---

Without cleanup:

```text
Timer Continues Forever
```

---

# 27. Event Listener Example

```jsx
useEffect(() => {

 window.addEventListener(

  "resize",

  handleResize

 );

 return () => {

  window.removeEventListener(

   "resize",

   handleResize

  );

 };

}, []);
```

---

Proper cleanup.

---

# API Calls

---

# 28. Most Common useEffect Usage

Fetching data.

---

Example:

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

# 29. Async API Call

```jsx
useEffect(() => {

 async function loadUsers() {

  const response =

   await fetch(

    "/users"

   );

  const data =

   await response.json();

  console.log(data);
 }

 loadUsers();

}, []);
```

---

Runs once.

---

# 30. Why Not Make Effect Async?

Wrong:

```jsx
useEffect(async () => {

});
```

---

React expects:

```text
Callback

OR

Cleanup Function
```

---

Not:

```text
Promise
```

---

Use inner async function.

---

# State + Effect

---

# 31. Fetch Data Into State

```jsx
const [

 users,

 setUsers

]
=
useState([]);
```

---

```jsx
useEffect(() => {

 async function loadUsers() {

  const response =

   await fetch(
    "/users"
   );

  const data =

   await response.json();

  setUsers(data);
 }

 loadUsers();

}, []);
```

---

Very common.

---

# 32. Effect Flow

```text
Component Mount
       ↓

API Call
       ↓

Data Received
       ↓

setUsers()
       ↓

Re-render
```

---

# useEffect with State Changes

---

# 33. Example

```jsx
const [

 count,

 setCount

]
=
useState(0);
```

---

```jsx
useEffect(() => {

 document.title =

 "Count: " + count;

}, [count]);
```

---

Every count change:

```text
Browser Title Updates
```

---

# 34. Real World Example

Shopping Cart:

```jsx
useEffect(() => {

 localStorage.setItem(

  "cart",

  JSON.stringify(cart)

 );

}, [cart]);
```

---

Cart changes:

```text
Storage Updated
```

---

# Infinite Loop Problem

---

# 35. Common Mistake

```jsx
useEffect(() => {

 setCount(

  count + 1

 );

});
```

---

Result:

```text
Infinite Loop
```

---

Why?

```text
Effect Runs
     ↓

setCount()
     ↓

Re-render
     ↓

Effect Runs Again
```

---

Forever.

---

# 36. Fix

Use:

```jsx
[]
```

or proper dependencies.

---

# Missing Dependency Problem

---

# 37. Example

```jsx
useEffect(() => {

 console.log(count);

}, []);
```

---

Problem:

```text
count changes

Effect Doesn't Run
```

---

Because:

```text
count not in dependencies
```

---

# 38. Correct

```jsx
useEffect(() => {

 console.log(count);

}, [count]);
```

---

# React Strict Mode Behavior

---

# 39. Development Surprise

React Strict Mode:

```text
Runs Effects Twice
```

in development.

---

Example:

```jsx
useEffect(() => {

 console.log("Run");

}, []);
```

---

May print:

```text
Run

Run
```

---

# 40. Why?

React verifies:

```text
Cleanup Logic

Side Effects

Component Safety
```

---

Production:

```text
Runs Once
```

---

# Internal Working

---

# 41. What Happens Internally?

Component renders.

---

React Fiber stores:

```text
Effect Callback

Dependencies
```

---

Example:

```jsx
useEffect(

 callback,

 [count]
);
```

---

Fiber stores:

```text
Previous Dependency

Current Dependency
```

---

# 42. Dependency Comparison

Old:

```text
count = 5
```

---

New:

```text
count = 6
```

---

React detects:

```text
Changed
```

---

Runs effect.

---

# 43. If Dependency Unchanged

Old:

```text
5
```

---

New:

```text
5
```

---

React skips effect.

---

Performance optimization.

---

# Real World Examples

---

# 44. Authentication Check

```jsx
useEffect(() => {

 checkLogin();

}, []);
```

---

Runs on page load.

---

# 45. Live Clock

```jsx
useEffect(() => {

 const timer =

 setInterval(() => {

  setTime(

   new Date()

  );

 }, 1000);

 return () =>

  clearInterval(timer);

}, []);
```

---

Updates every second.

---

# 46. Window Resize

```jsx
useEffect(() => {

 window.addEventListener(

  "resize",

  handleResize

 );

 return () =>

  window.removeEventListener(

   "resize",

   handleResize

 );

}, []);
```

---

Very common.

---

# Best Practices

---

# 47. Keep Effects Focused

Bad:

```text
Huge Effect
Doing Everything
```

---

Good:

```text
Multiple Small Effects
```

---

# 48. Always Cleanup

For:

```text
Timers

Subscriptions

Listeners

Sockets
```

---

# 49. Include Dependencies

Avoid:

```text
Missing Dependencies
```

---

Can create bugs.

---

# 50. Avoid Unnecessary Effects

Bad:

```jsx
useEffect(() => {

 setFullName(

  firstName +
  lastName

 );

}, [

 firstName,

 lastName

]);
```

---

Better:

```jsx
const fullName =

 firstName +

 lastName;
```

---

No effect needed.

---

# Interview Questions

---

### Q1. What is useEffect?

```text
Hook used for
side effects.
```

---

### Q2. What is a Side Effect?

```text
Operation outside
rendering process.
```

---

Examples:

```text
API Calls

Timers

Storage

Events
```

---

### Q3. When does useEffect run?

```text
After Rendering
```

---

### Q4. Difference Between

```jsx
useEffect(() => {})
```

and

```jsx
useEffect(() => {}, [])
```

---

First:

```text
Every Render
```

---

Second:

```text
Only Once
```

---

### Q5. What is Dependency Array?

```text
Controls when
effect executes.
```

---

### Q6. What is Cleanup Function?

```text
Function executed
before unmount
or re-run.
```

---

### Q7. Why Cleanup Important?

```text
Prevents Memory Leaks
```

---

### Q8. Can useEffect be async?

```text
No
```

---

Use async function inside effect.

---

### Q9. What causes Infinite Loops?

```text
State Update
Inside Effect
Without Proper Dependencies
```

---

### Q10. Why Does Strict Mode Run Effects Twice?

```text
Development Safety Checks
```

---

# Memory Tricks

### useEffect

```text
Run After Render
```

---

### Empty Dependency Array

```text
Run Once
```

---

### No Dependency Array

```text
Run Always
```

---

### Dependency Variable

```text
Run When Changed
```

---

### Cleanup

```text
Stop Side Effect
```

---

### API Calls

```text
Mount
 ↓

Fetch
```

---

### Timers

```text
Start
 ↓

Cleanup
```

---

### Dependency Array

```text
Watch Variables
```

---

# Quick Revision

```text
useEffect
---------

Purpose:

Handle Side Effects

Examples:

API Calls

Timers

Events

Storage

Syntax:

useEffect(() => {

}, []);

Runs:

After Render

Dependency Rules
----------------

No Array
 ↓

Every Render

[]

 ↓

Only Once

[count]

 ↓

When Count Changes

Cleanup
-------

return () => {

 cleanup
}

Used For:

✔ Timers

✔ Listeners

✔ Sockets

✔ Subscriptions

Common Mistakes
---------------

✘ Infinite Loops

✘ Missing Dependencies

✘ No Cleanup

Remember
--------

Render
 ↓

Effect

Dependency Array
 ↓

Controls Execution

Cleanup
 ↓

Prevents Leaks
```

---