# Hook Lifecycle

> **Goal:** Understand Hook Lifecycle from scratch, React Render Lifecycle, Mounting, Updating, Unmounting, Render Phase, Commit Phase, Effect Execution Order, useEffect Lifecycle, useLayoutEffect Lifecycle, Cleanup Functions, React Fiber Internals, React 18 Behavior, Strict Mode, Real-World Examples, and Interview Questions.

---

# 1. What is Hook Lifecycle?

Before Hooks existed, React had:

```jsx
componentDidMount()

componentDidUpdate()

componentWillUnmount()
```

---

inside:

```jsx
Class Components
```

---

Example:

```jsx
class User extends React.Component {

}
```

---

When Hooks were introduced:

```jsx
useEffect()

useLayoutEffect()
```

---

React still needed:

```text
Mount

Update

Unmount
```

---

These lifecycle phases still exist.

---

Hooks simply provide a different way to work with them.

---

# 2. What is Lifecycle?

Lifecycle means:

```text
Life Of A Component
```

---

From:

```text
Creation
```

to:

```text
Destruction
```

---

Visualization:

```text
Mount
  ↓

Update
  ↓

Update
  ↓

Update
  ↓

Unmount
```

---

This is:

```text
Component Lifecycle
```

---

Hooks participate in this lifecycle.

---

# 3. Real Life Example

Imagine:

```text
Employee Joins Company
```

---

Day 1:

```text
Employee Created
```

---

This is:

```text
Mount
```

---

Later:

```text
Employee Gets Promotion
```

---

This is:

```text
Update
```

---

Employee Leaves:

```text
Resignation
```

---

This is:

```text
Unmount
```

---

React components behave similarly.

---

# 4. Three Lifecycle Phases

React component lifecycle:

```text
1. Mount

2. Update

3. Unmount
```

---

# Phase 1

```text
Mount
```

---

Component appears for first time.

---

Example:

```jsx
<UserProfile />
```

---

Created.

---

# Phase 2

```text
Update
```

---

Occurs when:

```text
State Changes

Props Change

Context Changes
```

---

Component re-renders.

---

# Phase 3

```text
Unmount
```

---

Component removed.

---

Example:

```jsx
<UserProfile />
```

---

Deleted from DOM.

---

# 5. Lifecycle Visualization

```text
Mount
 ↓

Render
 ↓

Commit
 ↓

Effects

----------------

Update
 ↓

Render
 ↓

Commit
 ↓

Effects

----------------

Unmount
 ↓

Cleanup
```

---

Very important.

---

# 6. React Rendering Pipeline

Most interview candidates miss this.

---

React lifecycle actually contains:

```text
Render Phase

Commit Phase
```

---

Visualization:

```text
State Change
      ↓

Render Phase
      ↓

Commit Phase
      ↓

Effects Run
```

---

Understanding this is critical.

---

# 7. Render Phase

Purpose:

```text
Calculate UI
```

---

React executes:

```jsx
function App() {

}
```

---

Creates:

```text
Virtual DOM
```

---

No DOM updates yet.

---

Only calculation.

---

# Example

```jsx
function App() {

 return <h1>Hello</h1>;

}
```

---

React calculates:

```text
Virtual DOM Tree
```

---

Render phase complete.

---

# 8. Commit Phase

After render finishes:

```text
React Updates Real DOM
```

---

Example:

```html
<h1>Hello</h1>
```

---

Inserted into browser.

---

Commit phase complete.

---

# Important

Render:

```text
Calculate UI
```

---

Commit:

```text
Apply UI
```

---

Most hook behavior depends on this distinction.

---

# Mount Lifecycle

---

# 9. Mount Process

Initial render:

```jsx
<App />
```

---

Flow:

```text
Component Created
       ↓

Render Phase
       ↓

Commit Phase
       ↓

Effects Run
```

---

Visualization:

```text
Mount

 ↓

Render

 ↓

DOM Created

 ↓

useEffect

 ↓

Done
```

---

# Example

```jsx
function App() {

 useEffect(() => {

  console.log(
   "Mounted"
  );

 }, []);

 return <h1>Hello</h1>;

}
```

---

Output:

```text
Mounted
```

---

Runs after first mount.

---

# Update Lifecycle

---

# 10. What Triggers Update?

Three major causes:

```text
State Change

Props Change

Context Change
```

---

Example:

```jsx
setCount(5);
```

---

Triggers:

```text
Re-render
```

---

# Update Flow

```text
State Change
      ↓

Render Phase
      ↓

Diffing
      ↓

Commit Phase
      ↓

Effects
```

---

# Example

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

React performs:

```text
Update Lifecycle
```

---

# Unmount Lifecycle

---

# 11. What is Unmount?

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

Unmount occurs.

---

# Flow

```text
Unmount
 ↓

Cleanup Functions
 ↓

Component Removed
```

---

# Example

```jsx
useEffect(() => {

 console.log(
  "Mounted"
 );

 return () => {

  console.log(
   "Unmounted"
  );

 };

}, []);
```

---

Output:

```text
Mounted

Unmounted
```

---

# useEffect Lifecycle

---

# 12. useEffect Overview

Most important hook lifecycle topic.

---

Runs:

```text
After Commit Phase
```

---

Never during render.

---

# Visualization

```text
Render
 ↓

Commit
 ↓

useEffect
```

---

# Example

```jsx
useEffect(() => {

 console.log(
  "Effect"
 );

});
```

---

Runs:

```text
After DOM Update
```

---

Always.

---

# 13. Mount Effect

Example:

```jsx
useEffect(() => {

 console.log(
  "Mounted"
 );

}, []);
```

---

Dependency:

```jsx
[]
```

---

Runs:

```text
Only Once
```

---

After first mount.

---

Visualization:

```text
Mount
 ↓

Effect
```

---

# 14. Update Effect

Example:

```jsx
useEffect(() => {

 console.log(
  count
 );

}, [count]);
```

---

Runs when:

```text
count Changes
```

---

Visualization:

```text
count changes
      ↓

render
      ↓

commit
      ↓

effect
```

---

# 15. Every Render Effect

Example:

```jsx
useEffect(() => {

 console.log(
  "Every Render"
 );

});
```

---

No dependency array.

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

Every time.

---

# Cleanup Functions

---

# 16. Why Cleanup Exists?

Suppose:

```jsx
window.addEventListener()
```

---

Without cleanup:

```text
Memory Leaks
```

---

Need cleanup.

---

React provides:

```jsx
return () => {}
```

---

inside effect.

---

# Example

```jsx
useEffect(() => {

 const timer =
 setInterval(() => {

 }, 1000);

 return () => {

  clearInterval(
   timer
  );

 };

}, []);
```

---

Timer removed.

---

Safe.

---

# 17. Cleanup Lifecycle

Important.

---

React executes:

```text
Cleanup
```

before:

```text
Next Effect
```

---

and before:

```text
Unmount
```

---

Flow:

```text
Old Cleanup
      ↓

New Effect
```

---

# Example

```jsx
useEffect(() => {

 console.log(
  "Effect"
 );

 return () => {

  console.log(
   "Cleanup"
  );

 };

}, [count]);
```

---

Count changes:

```text
Cleanup
 ↓

Effect
```

---

Always.

---

# useEffect Complete Lifecycle

---

# 18. Initial Mount

```text
Render
 ↓

Commit
 ↓

Effect
```

---

# 19. Update

```text
Render
 ↓

Commit
 ↓

Cleanup
 ↓

Effect
```

---

# 20. Unmount

```text
Cleanup
 ↓

Component Removed
```

---

Very important interview topic.

---

# useLayoutEffect Lifecycle

---

# 21. What is Different?

Runs:

```text
Before Browser Paint
```

---

Flow:

```text
Render
 ↓

Commit
 ↓

useLayoutEffect
 ↓

Paint
```

---

Unlike:

```jsx
useEffect()
```

which runs after paint.

---

# Comparison

---

# 22. useEffect

```text
Render
 ↓

Commit
 ↓

Paint
 ↓

Effect
```

---

User sees UI first.

---

Effect later.

---

# 23. useLayoutEffect

```text
Render
 ↓

Commit
 ↓

Layout Effect
 ↓

Paint
```

---

Effect executes first.

---

Then browser paints.

---

# Example

```jsx
useLayoutEffect(() => {

 measureElement();

});
```

---

Used for:

```text
DOM Measurements
```

---

# Effect Execution Order

---

# 24. Multiple Effects

Example:

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

Execution order:

```text
Top To Bottom
```

---

Always.

---

# Cleanup Order

---

# 25. Example

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

# React Fiber Internals

---

# 26. During Render

React builds:

```text
Fiber Tree
```

---

Contains:

```text
State Hooks

Effect Hooks

Memo Hooks
```

---

Stored inside:

```text
Fiber Node
```

---

# Visualization

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

# 27. Effect Queue

React stores effects:

```text
Effect List
```

---

During render.

---

After commit:

```text
Effect Queue Executes
```

---

This is why:

```jsx
useEffect()
```

runs later.

---

# React 18 Strict Mode

---

# 28. Why Effects Run Twice?

Development mode:

```text
Strict Mode
```

---

May execute:

```text
Mount

Cleanup

Mount Again
```

---

Purpose:

```text
Detect Side Effects
```

---

Not production behavior.

---

# Example

```jsx
useEffect(() => {

 console.log(
  "Mounted"
 );

}, []);
```

---

May print:

```text
Mounted

Mounted
```

---

In development.

---

Expected.

---

# Lifecycle Comparison Table

| Phase | Render | Commit | Effect | Cleanup |
|---------|---------|---------|---------|---------|
| Mount | Yes | Yes | Yes | No |
| Update | Yes | Yes | Yes | Previous Cleanup First |
| Unmount | No | No | No | Yes |

---

# Common Mistakes

---

# 29. Updating State During Render

Wrong:

```jsx
function App() {

 setCount(5);

}
```

---

Causes:

```text
Infinite Re-renders
```

---

# 30. Forgetting Cleanup

Wrong:

```jsx
useEffect(() => {

 window.addEventListener();

});
```

---

Listener never removed.

---

Memory leak.

---

# 31. Using useLayoutEffect Everywhere

Wrong.

---

Blocks painting.

---

Can hurt performance.

---

Use:

```jsx
useEffect
```

by default.

---

# Best Practices

---

# 32. Use useEffect For Most Side Effects

Examples:

```text
API Calls

Subscriptions

Timers

Analytics
```

---

# 33. Cleanup Resources

Always clean:

```text
Timers

Listeners

Sockets

Subscriptions
```

---

# 34. Use useLayoutEffect Only For DOM Work

Examples:

```text
Measurements

Animations

Scroll Position
```

---

# Interview Questions

---

### Q1. What are the lifecycle phases of a React component?

```text
Mount

Update

Unmount
```

---

### Q2. When does useEffect run?

```text
After Commit Phase
```

---

### Q3. When does useLayoutEffect run?

```text
Before Browser Paint
```

---

### Q4. What triggers component updates?

```text
State

Props

Context
```

---

### Q5. When does cleanup run?

```text
Before Next Effect

Before Unmount
```

---

### Q6. Why does useEffect run twice in development?

```text
React Strict Mode
```

---

### Q7. What is the Render Phase?

```text
Calculate UI
```

---

### Q8. What is the Commit Phase?

```text
Apply Changes To DOM
```

---

### Q9. Which hook is better for API calls?

```text
useEffect
```

---

### Q10. Which hook is better for DOM measurement?

```text
useLayoutEffect
```

---

# Memory Tricks

### Mount

```text
Component Created
```

---

### Update

```text
State/Props Changed
```

---

### Unmount

```text
Component Removed
```

---

### Render Phase

```text
Calculate UI
```

---

### Commit Phase

```text
Update DOM
```

---

### useEffect

```text
After Paint
```

---

### useLayoutEffect

```text
Before Paint
```

---

### Cleanup

```text
Before Next Effect
And Before Unmount
```

---

# Complete Lifecycle Flow

```text
MOUNT
------

Render
 ↓

Commit
 ↓

Paint
 ↓

useEffect


UPDATE
-------

Render
 ↓

Commit
 ↓

Cleanup
 ↓

Paint
 ↓

useEffect


UNMOUNT
--------

Cleanup
 ↓

Remove Component
```

---

# Quick Revision

```text
Hook Lifecycle
--------------

Lifecycle Phases:

1. Mount
2. Update
3. Unmount

Render Phase:
Calculate UI

Commit Phase:
Update DOM

useEffect:
After Paint

useLayoutEffect:
Before Paint

Cleanup Runs:

✔ Before Next Effect

✔ Before Unmount

Triggers Update:

✔ State Change

✔ Props Change

✔ Context Change

Remember:

Render
 ↓
Commit
 ↓
Paint
 ↓
useEffect

Render
 ↓
Commit
 ↓
useLayoutEffect
 ↓
Paint
```

---

# Complete React Hooks Roadmap

```text
Basic Hooks
-----------

useState
 ↓
State

useEffect
 ↓
Side Effects

useContext
 ↓
Global Data


Additional Hooks
----------------

useReducer
 ↓
Complex State

useRef
 ↓
Mutable References

useMemo
 ↓
Memoized Values

useCallback
 ↓
Memoized Functions

useLayoutEffect
 ↓
Before Paint

useImperativeHandle
 ↓
Custom Ref API

useDebugValue
 ↓
Debug Hooks

useId
 ↓
Unique Stable IDs


Advanced Hooks
--------------

Custom Hooks
 ↓
Reusable Logic

Hook Rules
 ↓
Hook Execution Rules

Hook Lifecycle
 ↓
How Hooks Behave During
Mount, Update, Unmount
```

---