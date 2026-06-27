# useRef Hook

> **Goal:** Understand useRef from scratch, why React created it, DOM References, Mutable Values, Re-render Behavior, Previous State Tracking, Timer Storage, Imperative DOM Manipulation, Internal Working, Real-World Examples, Performance Benefits, Best Practices, Common Mistakes, Interview Questions, and React Internals.

---

# 1. What Problem Does useRef Solve?

React applications are built using:

```text
State
```

using:

```jsx
useState()
```

---

Example:

```jsx
const [count, setCount] =
useState(0);
```

---

Whenever state changes:

```jsx
setCount(1);
```

---

React:

```text
Re-renders Component
```

---

Sometimes this is exactly what we want.

---

But sometimes we need:

```text
Store Data
Without Re-rendering
```

---

Examples:

```text
Input References

Timer IDs

Previous Values

Scroll Positions

DOM Elements

External Library Instances
```

---

Need a solution.

---

React provides:

```jsx
useRef()
```

---

# 2. What is useRef?

Definition:

> useRef is a React Hook that allows you to persist values between renders without causing re-renders.

---

Simple Meaning:

```text
Store Mutable Data

Without Re-rendering
```

---

# 3. Why is it Called Ref?

Ref means:

```text
Reference
```

---

Instead of storing:

```text
Value
```

---

React stores:

```text
Reference To Value
```

---

Similar to:

```java
Object Reference
```

in Java.

---

# 4. Syntax

```jsx
const ref =
useRef(initialValue);
```

---

Example:

```jsx
const countRef =
useRef(0);
```

---

React returns:

```jsx
{
 current: 0
}
```

---

Important:

```jsx
ref.current
```

contains actual value.

---

# 5. Structure of useRef

When React executes:

```jsx
const myRef =
useRef("Hello");
```

---

Internally:

```jsx
{
 current: "Hello"
}
```

---

React keeps same object across renders.

---

Very important.

---

# 6. Accessing Value

```jsx
const myRef =
useRef("Hello");
```

---

Read:

```jsx
console.log(
 myRef.current
);
```

---

Output:

```text
Hello
```

---

Update:

```jsx
myRef.current =
"React";
```

---

Output:

```text
React
```

---

# 7. Most Important Difference

State:

```jsx
setCount(5);
```

---

Triggers:

```text
Re-render
```

---

Ref:

```jsx
countRef.current = 5;
```

---

Triggers:

```text
Nothing
```

---

No re-render.

---

# 8. Visualization

## useState

```text
Update State
      ↓

Re-render
      ↓

UI Updates
```

---

## useRef

```text
Update Ref
      ↓

No Re-render
```

---

# 9. Example

```jsx
function App() {

 const countRef =
 useRef(0);

 function increase() {

  countRef.current++;

  console.log(
   countRef.current
  );
 }

 return (

  <button
   onClick={increase}
  >
   Increase
  </button>

 );
}
```

---

Click:

```text
1
2
3
4
```

---

UI:

```text
Never Updates
```

---

Because:

```text
No Re-render
```

---

# 10. Why?

React does NOT track:

```jsx
ref.current
```

changes.

---

React only tracks:

```text
State Changes
```

---

# DOM References

---

# 11. Most Common useRef Usage

Access DOM elements.

---

Example:

```jsx
<input />
```

---

Need:

```text
Focus Input
```

---

React provides refs.

---

# 12. Input Ref Example

```jsx
const inputRef =
useRef();
```

---

Attach:

```jsx
<input
 ref={inputRef}
/>
```

---

React stores:

```text
Actual DOM Element
```

---

Inside:

```jsx
inputRef.current
```

---

# 13. Visualization

```text
inputRef
      ↓

HTML Input Element
```

---

# 14. Focus Example

```jsx
function App() {

 const inputRef =
 useRef();

 function focusInput() {

  inputRef.current.focus();

 }

 return (

  <>
   <input
    ref={inputRef}
   />

   <button
    onClick={
     focusInput
    }
   >
    Focus
   </button>
  </>

 );
}
```

---

Click:

```text
Focus
```

---

Cursor moves into input.

---

Very common interview question.

---

# 15. What Happens Internally?

React renders:

```jsx
<input ref={inputRef}/>
```

---

After DOM creation:

```text
Actual DOM Node
```

stored in:

```jsx
inputRef.current
```

---

Now:

```jsx
inputRef.current.focus()
```

works.

---

# Auto Focus Example

---

# 16. Focus On Mount

```jsx
const inputRef =
useRef();
```

---

```jsx
useEffect(() => {

 inputRef.current.focus();

}, []);
```

---

Input focused automatically.

---

# Previous Value Tracking

---

# 17. Another Major Use Case

Store previous state.

---

Example:

```jsx
const [count,
setCount]
=
useState(0);
```

---

Need:

```text
Previous Count
```

---

useRef solves this.

---

# 18. Example

```jsx
const previousCount =
useRef();
```

---

```jsx
useEffect(() => {

 previousCount.current =
 count;

}, [count]);
```

---

Render:

```jsx
Current:
{count}
```

---

```jsx
Previous:
{previousCount.current}
```

---

Output:

```text
Current: 5

Previous: 4
```

---

Very popular interview question.

---

# 19. Why useRef?

If previous value stored in state:

```text
Extra Re-render
```

---

Ref avoids that.

---

# Storing Timers

---

# 20. Timer Example

```jsx
const timerRef =
useRef();
```

---

Store interval:

```jsx
timerRef.current =
setInterval(...);
```

---

Stop timer:

```jsx
clearInterval(
 timerRef.current
);
```

---

# 21. Why Not State?

Timer ID:

```text
Not UI Data
```

---

Should not cause:

```text
Re-render
```

---

Perfect for refs.

---

# Storing Mutable Data

---

# 22. Example

```jsx
const renderCount =
useRef(0);
```

---

```jsx
renderCount.current++;
```

---

Tracks:

```text
Number Of Renders
```

---

Without re-render.

---

# 23. Render Counter Example

```jsx
function App() {

 const renders =
 useRef(0);

 renders.current++;

 console.log(
  renders.current
 );

 return <h1>Hello</h1>;
}
```

---

Output:

```text
1

2

3
```

---

Each render increments.

---

# State vs Ref

---

# 24. Comparison

| Feature | useState | useRef |
|----------|----------|----------|
| Causes Re-render | Yes | No |
| Stores Data | Yes | Yes |
| UI Updates | Yes | No |
| Mutable | No | Yes |
| DOM Access | No | Yes |

---

# 25. Memory Trick

```text
useState
     ↓
UI Data

useRef
     ↓
Non-UI Data
```

---

# Real World Example

---

# 26. Search Input

```jsx
const searchRef =
useRef();
```

---

Read value:

```jsx
searchRef.current.value
```

---

Without state.

---

Useful in large forms.

---

# 27. Video Player Example

```jsx
const videoRef =
useRef();
```

---

Play video:

```jsx
videoRef.current.play();
```

---

Pause video:

```jsx
videoRef.current.pause();
```

---

Very common.

---

# 28. Scroll Example

```jsx
const sectionRef =
useRef();
```

---

Scroll:

```jsx
sectionRef.current
.scrollIntoView();
```

---

Used heavily in:

```text
Landing Pages

Dashboards

Documentation Sites
```

---

# useRef vs Variables

---

# 29. Common Interview Question

Normal variable:

```jsx
let count = 0;
```

---

Problem:

Every render:

```text
Variable Recreated
```

---

Value lost.

---

# 30. Example

```jsx
let count = 0;

count++;
```

---

Next render:

```text
count = 0
```

again.

---

# 31. useRef

```jsx
const countRef =
useRef(0);
```

---

Value survives:

```text
All Renders
```

---

# Visualization

---

## Variable

```text
Render 1
 ↓

count = 0

Render 2
 ↓

count = 0
```

---

## Ref

```text
Render 1
 ↓

countRef = 0

Render 2
 ↓

countRef = 1
```

---

Persisted.

---

# React Internals

---

# 32. Fiber Storage

React Fiber stores:

```text
Hook Objects
```

---

Example:

```jsx
useRef(0)
```

---

Fiber:

```text
Hook Node
    ↓

{
 current: 0
}
```

---

# 33. Re-render Behavior

When:

```jsx
ref.current = 10
```

---

Object reference:

```text
Same
```

---

React sees:

```text
No State Change
```

---

No re-render.

---

# 34. State Difference

State update:

```jsx
setCount(10);
```

---

React:

```text
Schedules Update
```

---

Triggers:

```text
Re-render
```

---

# useRef + useEffect

---

# 35. Common Combination

```jsx
const previousValue =
useRef();
```

---

```jsx
useEffect(() => {

 previousValue.current =
 value;

}, [value]);
```

---

Track previous value.

---

Very common.

---

# useRef + External Libraries

---

# 36. Chart.js Example

```jsx
const chartRef =
useRef();
```

---

Store:

```text
Chart Instance
```

---

Without re-render.

---

# 37. Google Maps Example

```jsx
const mapRef =
useRef();
```

---

Store:

```text
Map Object
```

---

Common production use case.

---

# Common Mistakes

---

# 38. Using Ref For UI Updates

Wrong:

```jsx
countRef.current++;
```

---

Expecting:

```text
UI Update
```

---

Won't happen.

---

Need:

```jsx
useState
```

for UI changes.

---

# 39. Accessing DOM Before Mount

Wrong:

```jsx
inputRef.current.focus();
```

---

Before component mounts.

---

Result:

```text
Null Error
```

---

Use:

```jsx
useEffect()
```

---

# 40. Overusing Refs

Bad:

```text
Everything In Refs
```

---

React becomes:

```text
Unpredictable
```

---

Use refs only when needed.

---

# Best Practices

---

# 41. Use Refs For DOM Access

Examples:

```text
Focus

Scroll

Video Control

Canvas
```

---

# 42. Use Refs For Mutable Data

Examples:

```text
Timer IDs

Previous Values

External Instances
```

---

# 43. Use State For UI

Examples:

```text
Counter

Theme

User Data

Form Data
```

---

Never replace state with refs.

---

# 44. Use Ref + Effect Together

Very common pattern.

---

Example:

```text
Previous State Tracking

DOM Operations

Subscriptions
```

---

# Interview Questions

---

### Q1. What is useRef?

```text
Hook For Persisting
Mutable Values
Without Re-render
```

---

### Q2. What does useRef return?

```jsx
{
 current: value
}
```

---

### Q3. Does Updating Ref Cause Re-render?

```text
No
```

---

### Q4. Most Common useRef Usage?

```text
DOM Access
```

---

### Q5. Difference Between useState and useRef?

```text
State
 ↓
Re-render

Ref
 ↓
No Re-render
```

---

### Q6. Can useRef Store Objects?

```text
Yes
```

---

Anything:

```text
String

Array

Object

DOM Node

Function
```

---

### Q7. Why Use Ref For Timers?

```text
Timer ID
Is Not UI Data
```

---

### Q8. Can useRef Replace useState?

```text
No
```

---

### Q9. How To Focus Input Using useRef?

```jsx
inputRef.current.focus();
```

---

### Q10. How To Store Previous State?

```jsx
useRef()
+
useEffect()
```

---

# Memory Tricks

### useState

```text
Store Data
+
Update UI
```

---

### useRef

```text
Store Data

No UI Update
```

---

### Ref Object

```jsx
{
 current: value
}
```

---

### DOM Access

```text
Input

Video

Canvas

Scroll
```

---

### Previous Value

```text
Ref
+
Effect
```

---

### Timer Storage

```text
Ref
```

---

### Rule

```text
UI Data
 ↓
useState

Non-UI Data
 ↓
useRef
```

---

# Quick Revision

```text
useRef
------

Purpose:

Store Mutable Data
Without Re-rendering

Returns:

{
 current: value
}

Most Common Uses:

✔ DOM Access

✔ Input Focus

✔ Previous Values

✔ Timer IDs

✔ Scroll Handling

✔ Third Party Libraries

Differences:

useState
 ↓

Triggers Re-render

useRef
 ↓

No Re-render

Advantages:

✔ Fast

✔ Persistent

✔ DOM Manipulation

✔ No Extra Renders

Disadvantages:

✘ Doesn't Update UI

✘ Can Be Misused

Remember:

useState
 ↓
UI State

useRef
 ↓
Mutable Reference

DOM Access
 ↓
useRef

Previous Value
 ↓
useRef + useEffect
```

---