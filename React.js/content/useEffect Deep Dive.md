# useEffect Deep Dive

> **Goal:** Master `useEffect` completely — understand why it exists, how it works internally, relationship with component lifecycle, dependency arrays, cleanup functions, API calls, timers, event listeners, memory leak prevention, multiple effects, React Fiber behavior, React 18 Strict Mode, performance optimization, common mistakes, and interview questions.

---

# 1. Why Does useEffect Exist?

React components should ideally be:

```text
Pure Functions
```

---

Meaning:

```jsx
function App() {

  return <h1>Hello</h1>;

}
```

---

Given same input:

```text
Same Output
```

---

However, real applications need:

```text
API Calls

Database Calls

Timers

WebSockets

DOM Manipulation

Analytics

Browser Events
```

---

These are called:

```text
Side Effects
```

---

React created:

```jsx
useEffect()
```

---

To handle side effects separately from rendering.

---

# 2. What is a Side Effect?

A side effect is:

```text
Anything That Affects
Something Outside React Rendering
```

---

Examples:

```text
Fetching Data

Setting Timers

Adding Event Listeners

Updating Document Title

LocalStorage Access

Socket Connections
```

---

Example:

```jsx
document.title =
"Dashboard";
```

---

This affects browser state.

---

Therefore:

```text
Side Effect
```

---

# 3. Definition

> useEffect allows functional components to perform side effects after rendering.

---

Simple Meaning:

```text
Run Code
After React Updates UI
```

---

# 4. Basic Syntax

```jsx
useEffect(() => {

  // Effect Logic

});
```

---

Example:

```jsx
useEffect(() => {

  console.log("Rendered");

});
```

---

Runs after every render.

---

# 5. Internal Flow

```text
State Change
      ↓

Render Phase
      ↓

Virtual DOM
      ↓

Commit Phase
      ↓

Browser Paint
      ↓

useEffect Runs
```

---

Important:

```text
useEffect NEVER Runs
During Render
```

---

It always runs:

```text
After Rendering
```

---

# Component Lifecycle

---

# 6. Lifecycle Before Hooks

Class Components:

```jsx
componentDidMount()

componentDidUpdate()

componentWillUnmount()
```

---

Functional Components:

```jsx
useEffect()
```

---

Replaces all three.

---

# 7. Mount Lifecycle

Component appears first time.

---

Example:

```jsx
useEffect(() => {

  console.log("Mounted");

}, []);
```

---

Output:

```text
Mounted
```

---

Runs:

```text
Once
```

---

After first render.

---

Visualization:

```text
Mount
 ↓

Render
 ↓

Commit
 ↓

Effect
```

---

# 8. Update Lifecycle

State changes.

---

Example:

```jsx
const [count,
setCount]
=
useState(0);
```

---

Button click:

```jsx
setCount(1);
```

---

Flow:

```text
Render
 ↓

Commit
 ↓

Effect
```

---

Effect runs again.

---

# 9. Unmount Lifecycle

Component removed.

---

Example:

```jsx
{show &&
 <User />}
```

---

When:

```jsx
show = false
```

---

Component disappears.

---

Cleanup runs.

---

# Dependency Array

---

# 10. What is Dependency Array?

Second parameter:

```jsx
useEffect(() => {

}, []);
```

---

Controls:

```text
When Effect Runs
```

---

Most important part of useEffect.

---

# 11. No Dependency Array

```jsx
useEffect(() => {

 console.log("Hello");

});
```

---

Runs:

```text
After Every Render
```

---

Mount:

```text
Yes
```

---

Update:

```text
Yes
```

---

Always.

---

Visualization:

```text
Render
 ↓

Effect

Render
 ↓

Effect

Render
 ↓

Effect
```

---

# 12. Empty Dependency Array

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

Equivalent to:

```jsx
componentDidMount()
```

---

Visualization:

```text
Mount
 ↓

Effect

Update
 ↓

Nothing
```

---

# 13. Specific Dependencies

```jsx
useEffect(() => {

 console.log(count);

}, [count]);
```

---

Runs only when:

```text
count Changes
```

---

Example:

```jsx
count = 0
```

---

Effect executes.

---

Later:

```jsx
count = 5
```

---

Effect executes again.

---

# Visualization

```text
count changed
      ↓

Render
      ↓

Effect
```

---

# 14. Multiple Dependencies

```jsx
useEffect(() => {

}, [count, name]);
```

---

Runs when:

```text
count changes

OR

name changes
```

---

# Dependency Comparison

---

# 15. React Uses Object.is()

React compares:

```text
Old Dependency

vs

New Dependency
```

---

Using:

```js
Object.is()
```

---

Example:

```jsx
count = 5
```

---

New value:

```jsx
count = 5
```

---

No change.

---

Effect does not run.

---

# Common Dependency Mistake

---

# 16. Object Dependency

```jsx
const user = {

 name: "John"

};
```

---

Effect:

```jsx
useEffect(() => {

}, [user]);
```

---

Problem:

Every render creates:

```text
New Object Reference
```

---

Effect runs repeatedly.

---

# Wrong

```jsx
const user = {

 name: "John"

};
```

---

New object each render.

---

# Better

```jsx
const user =
useMemo(() => {

 return {

  name: "John"

 };

}, []);
```

---

Stable reference.

---

# Cleanup Functions

---

# 17. Why Cleanup Exists?

Suppose:

```jsx
window.addEventListener();
```

---

Component removed.

---

Listener remains.

---

Problem:

```text
Memory Leak
```

---

Need cleanup.

---

# 18. Cleanup Syntax

```jsx
useEffect(() => {

 return () => {

 };

});
```

---

Returned function:

```text
Cleanup Function
```

---

# Example

```jsx
useEffect(() => {

 console.log("Mounted");

 return () => {

  console.log(
   "Cleanup"
  );

 };

}, []);
```

---

Output:

```text
Mounted

Cleanup
```

---

# 19. Cleanup Execution Order

React executes:

```text
Cleanup
```

before:

```text
New Effect
```

---

and before:

```text
Unmount
```

---

Flow:

```text
Cleanup
 ↓

Effect
```

---

# Example

```jsx
useEffect(() => {

 console.log("Effect");

 return () => {

  console.log("Cleanup");

 };

}, [count]);
```

---

Count changes:

```text
Cleanup

Effect
```

---

Always.

---

# API Calls

---

# 20. Most Common useEffect Use Case

Fetching data.

---

Example:

```jsx
useEffect(() => {

 fetch("/users")
   .then(res => res.json())
   .then(data => {

     setUsers(data);

   });

}, []);
```

---

Runs once.

---

Loads data.

---

# API Flow

```text
Component Mount
      ↓

Effect
      ↓

API Call
      ↓

Response
      ↓

State Update
      ↓

Re-render
```

---

# Async Function Pattern

---

# 21. Wrong

```jsx
useEffect(async () => {

});
```

---

React expects:

```text
Function
```

or:

```text
Cleanup Function
```

---

Not Promise.

---

# Correct

```jsx
useEffect(() => {

 async function load() {

 }

 load();

}, []);
```

---

# API Error Handling

---

# 22. Example

```jsx
useEffect(() => {

 async function load() {

  try {

   const response =
   await fetch("/users");

   const data =
   await response.json();

   setUsers(data);

  }
  catch(error) {

   console.log(error);

  }

 }

 load();

}, []);
```

---

Production-ready pattern.

---

# Timers

---

# 23. setInterval Example

```jsx
useEffect(() => {

 const timer =
 setInterval(() => {

  console.log("Tick");

 }, 1000);

 return () => {

  clearInterval(timer);

 };

}, []);
```

---

Without cleanup:

```text
Interval Keeps Running
```

---

Memory leak.

---

# 24. setTimeout Example

```jsx
useEffect(() => {

 const timer =
 setTimeout(() => {

  console.log("Done");

 }, 3000);

 return () => {

  clearTimeout(timer);

 };

}, []);
```

---

Always clean timers.

---

# Event Listeners

---

# 25. Adding Listener

```jsx
useEffect(() => {

 const resizeHandler =
 () => {

  console.log(
   window.innerWidth
  );

 };

 window.addEventListener(
  "resize",
  resizeHandler
 );

 return () => {

  window.removeEventListener(
   "resize",
   resizeHandler
  );

 };

}, []);
```

---

Perfect pattern.

---

# Flow

```text
Mount
 ↓

Add Listener

Unmount
 ↓

Remove Listener
```

---

# WebSocket Example

---

# 26. Real Production Example

```jsx
useEffect(() => {

 const socket =
 new WebSocket(url);

 return () => {

  socket.close();

 };

}, []);
```

---

Cleanup prevents:

```text
Open Connections
```

---

# Memory Leak Prevention

---

# 27. What Is Memory Leak?

Unused resources remain alive.

---

Examples:

```text
Timers

Listeners

Sockets

Subscriptions
```

---

Consume memory.

---

Performance drops.

---

# 28. Leak Example

Wrong:

```jsx
useEffect(() => {

 setInterval(() => {

 }, 1000);

}, []);
```

---

Component removed.

---

Interval still running.

---

Memory leak.

---

# Correct

```jsx
useEffect(() => {

 const timer =
 setInterval(() => {

 }, 1000);

 return () => {

  clearInterval(timer);

 };

}, []);
```

---

# Multiple useEffects

---

# 29. Why Multiple Effects?

Many developers do:

```jsx
useEffect(() => {

 fetchUsers();

 updateTitle();

 addListener();

}, []);
```

---

Huge effect.

---

Hard to maintain.

---

# Better

Separate concerns.

---

# 30. Example

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

```jsx
useEffect(() => {

 document.title =
 count;

}, [count]);
```

---

```jsx
useEffect(() => {

 addListener();

 return removeListener;

}, []);
```

---

Much cleaner.

---

# Effect Execution Order

---

# 31. Multiple Effects Order

```jsx
useEffect(() => {

 console.log("A");

});

useEffect(() => {

 console.log("B");

});
```

---

Output:

```text
A

B
```

---

Order:

```text
Top To Bottom
```

---

# Cleanup Order

---

# 32. Example

```jsx
useEffect(() => {

 return () => {

  console.log("A");

 };

});

useEffect(() => {

 return () => {

  console.log("B");

 };

});
```

---

Cleanup order:

```text
A

B
```

---

Same sequence.

---

# Infinite Loop Problem

---

# 33. Dangerous Example

```jsx
useEffect(() => {

 setCount(
  count + 1
 );

});
```

---

Flow:

```text
Render
 ↓

Effect
 ↓

setCount
 ↓

Render
 ↓

Effect
 ↓

setCount
```

---

Infinite loop.

---

# Fix

```jsx
useEffect(() => {

 setCount(
  count + 1
 );

}, []);
```

---

Runs once.

---

# Stale Closure Problem

---

# 34. Example

```jsx
useEffect(() => {

 console.log(count);

}, []);
```

---

Suppose:

```jsx
count = 0
```

---

Later:

```jsx
count = 10
```

---

Effect still remembers:

```text
0
```

---

Because dependency missing.

---

# Fix

```jsx
useEffect(() => {

 console.log(count);

}, [count]);
```

---

Always latest value.

---

# React 18 Strict Mode

---

# 35. Why Effect Runs Twice?

Development mode:

```text
Strict Mode
```

---

React performs:

```text
Mount

Cleanup

Mount Again
```

---

Purpose:

```text
Find Side Effects
```

---

Example:

```jsx
useEffect(() => {

 console.log("Mounted");

}, []);
```

---

May log:

```text
Mounted

Mounted
```

---

Only development.

---

Not production.

---

# React Fiber Internals

---

# 36. Effect Storage

During render:

```text
Effects Are Registered
```

---

Inside:

```text
Fiber Node
```

---

Visualization:

```text
Fiber
 ↓

State Hook
 ↓

Effect Hook
 ↓

Memo Hook
```

---

# 37. Effect Queue

React stores:

```text
Pending Effects
```

---

After commit:

```text
Queue Executes
```

---

This is why:

```jsx
useEffect()
```

runs later.

---

# Best Practices

---

# 38. Use Multiple Effects

Separate concerns.

---

Good:

```text
One Effect
One Responsibility
```

---

# 39. Always Cleanup Resources

Cleanup:

```text
Timers

Listeners

Sockets

Subscriptions
```

---

# 40. Include Dependencies

Don't ignore ESLint.

---

Missing dependencies cause:

```text
Stale Data
```

---

# 41. Prefer useEffect For Side Effects

Examples:

```text
API Calls

Analytics

Storage

Timers

Events
```

---

# Interview Questions

---

### Q1. What is useEffect?

```text
Hook For Side Effects
```

---

### Q2. When does useEffect run?

```text
After Commit Phase
After Browser Paint
```

---

### Q3. What does [] mean?

```text
Run Once On Mount
```

---

### Q4. What happens without dependency array?

```text
Runs After Every Render
```

---

### Q5. Why do we need cleanup?

```text
Prevent Memory Leaks
```

---

### Q6. When does cleanup run?

```text
Before Next Effect

Before Unmount
```

---

### Q7. Can useEffect be async?

```text
No
```

---

Wrap async function inside effect.

---

### Q8. Why use multiple useEffects?

```text
Separate Responsibilities
```

---

### Q9. Why does useEffect run twice in development?

```text
React Strict Mode
```

---

### Q10. What causes infinite loops?

```text
State Updates
Inside Effect
Without Proper Dependencies
```

---

# Memory Tricks

### No Dependency Array

```text
Every Render
```

---

### Empty Dependency Array

```text
Mount Only
```

---

### [value]

```text
Run When Value Changes
```

---

### Cleanup

```text
Before Next Effect

Before Unmount
```

---

### useEffect

```text
After Paint
```

---

### Main Purpose

```text
Side Effects
```

---

# Quick Revision

```text
useEffect Deep Dive
------------------

Purpose:

Side Effects

Runs:

After Render
After Paint

Dependency Array:

No Array
 ↓
Every Render

[]
 ↓
Mount Only

[value]
 ↓
When Value Changes

Cleanup:

✔ Timers

✔ Event Listeners

✔ WebSockets

✔ Subscriptions

Common Uses:

✔ API Calls

✔ Timers

✔ Events

✔ LocalStorage

✔ Analytics

Best Practices:

✔ Multiple Effects

✔ Cleanup Resources

✔ Include Dependencies

✔ Avoid Infinite Loops

Remember:

Render
 ↓
Commit
 ↓
Paint
 ↓
useEffect
```

---