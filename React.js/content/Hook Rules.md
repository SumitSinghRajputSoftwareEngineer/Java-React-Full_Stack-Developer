# Hook Rules

> **Goal:** Understand React Hook Rules from scratch, why they exist, how React internally tracks hooks, why hooks cannot be called conditionally, how hook ordering works, ESLint Rules of Hooks, common mistakes, React Fiber internals, interview questions, and best practices.

---

# 1. What Problem Do Hook Rules Solve?

Suppose React allowed this:

```jsx
function App() {

  const [name, setName] =
  useState("Sumit");

  if(name === "Sumit") {

    const [age, setAge] =
    useState(25);

  }

}
```

---

Looks harmless.

---

But React internally tracks hooks by:

```text
Position
```

---

Not by:

```text
Variable Name
```

---

If hook order changes:

```text
React Gets Confused
```

---

State gets assigned incorrectly.

---

This can cause:

```text
Wrong State

Wrong Effects

Application Bugs

Crashes
```

---

React created:

```text
Rules Of Hooks
```

---

# 2. What Are Hook Rules?

React officially provides two main rules:

---

## Rule 1

```text
Only Call Hooks
At The Top Level
```

---

## Rule 2

```text
Only Call Hooks
From React Functions
```

---

These two rules power the entire hook system.

---

# 3. Simple Meaning

React expects:

```text
Hook #1

Hook #2

Hook #3

Hook #4
```

---

In the same order:

```text
Every Render
```

---

If order changes:

```text
React Cannot Match State
```

---

# 4. Real-Life Example

Imagine movie theater seats:

```text
Seat 1 → Rahul

Seat 2 → Amit

Seat 3 → Priya
```

---

Next day:

```text
Seat 1 → Rahul

Seat 3 → Amit

Seat 4 → Priya
```

---

Everything becomes:

```text
Misaligned
```

---

React hooks work similarly.

---

Hook order must remain:

```text
Stable
```

---

# 5. The Two Official Rules

---

# Rule 1

## Only Call Hooks At The Top Level

Correct:

```jsx
function App() {

  const [count, setCount] =
  useState(0);

  const [name, setName] =
  useState("");

}
```

---

Hooks execute:

```text
1st Hook

2nd Hook
```

---

Same order every render.

---

Correct.

---

# Rule 2

## Only Call Hooks From React Functions

Allowed:

```jsx
function App() {

  useState();

}
```

---

Allowed:

```jsx
function useCounter() {

  useState();

}
```

---

Not allowed:

```jsx
function calculate() {

  useState();

}
```

---

Because:

```text
Regular JavaScript Function
```

---

Not a React function.

---

# Why React Needs These Rules?

---

# 6. React Does NOT Store Hook Names

Many developers think:

```jsx
const [count] =
useState();
```

---

React stores:

```text
count
```

---

Wrong.

---

React stores:

```text
Hook #1
```

---

Example:

```jsx
const [count] =
useState(0);

const [name] =
useState("");
```

---

Internally:

```text
Hook #1 → count

Hook #2 → name
```

---

React remembers:

```text
Position
```

---

Not variable names.

---

# Internal Hook Tracking

---

# 7. First Render

Component:

```jsx
function App() {

  const [count] =
  useState(0);

  const [name] =
  useState("");

}
```

---

React builds:

```text
Hook #1 → count

Hook #2 → name
```

---

Stored in Fiber.

---

# Visualization

```text
Fiber

 ↓

Hook 1
(count)

 ↓

Hook 2
(name)
```

---

# 8. Second Render

React executes again:

```jsx
function App() {

  const [count] =
  useState();

  const [name] =
  useState();

}
```

---

React expects:

```text
Hook #1 → count

Hook #2 → name
```

---

Everything works.

---

# Why Conditions Break Hooks?

---

# 9. Wrong Example

```jsx
function App() {

  const [count] =
  useState(0);

  if(count > 0) {

    useEffect(() => {

      console.log("Hello");

    });

  }

  const [name] =
  useState("");

}
```

---

First render:

```text
count = 0
```

---

Hook order:

```text
Hook #1 → useState

Hook #2 → useState
```

---

# Second Render

Suppose:

```text
count = 5
```

---

Now:

```text
Hook #1 → useState

Hook #2 → useEffect

Hook #3 → useState
```

---

Order changed.

---

React becomes confused.

---

# Visualization

First Render:

```text
1 → useState(count)

2 → useState(name)
```

---

Second Render:

```text
1 → useState(count)

2 → useEffect

3 → useState(name)
```

---

React now thinks:

```text
name state
=
effect state
```

---

Everything breaks.

---

# Therefore

Never:

```jsx
if(condition) {

 useEffect();

}
```

---

# 10. Correct Solution

Move condition:

```jsx
useEffect(() => {

  if(condition) {

    console.log("Hello");

  }

}, [condition]);
```

---

Hook remains:

```text
Always Called
```

---

Only logic changes.

---

Correct.

---

# Why Loops Break Hooks?

---

# 11. Wrong Example

```jsx
for(let i = 0;
    i < 5;
    i++) {

  useState();

}
```

---

Render 1:

```text
5 Hooks
```

---

Render 2:

```text
3 Hooks
```

---

Hook order changes.

---

React breaks.

---

Never do this.

---

# Why Nested Functions Break Hooks?

---

# 12. Wrong Example

```jsx
function App() {

  function loadData() {

    useEffect(() => {

    });

  }

}
```

---

Problem:

```text
React Cannot Guarantee
Execution Order
```

---

Hooks must execute:

```text
During Component Render
```

---

Not later.

---

# Why Event Handlers Break Hooks?

---

# 13. Wrong Example

```jsx
<button
 onClick={() => {

   useState();

 }}
>
Click
</button>
```

---

Reason:

```text
Event Runs Later
```

---

Not during rendering.

---

React hook system fails.

---

# Only Call Hooks From React Functions

---

# 14. Allowed Places

React Component:

```jsx
function App() {

 useState();

}
```

---

Custom Hook:

```jsx
function useCounter() {

 useState();

}
```

---

Correct.

---

# 15. Not Allowed

Regular function:

```jsx
function calculate() {

 useState();

}
```

---

Class:

```jsx
class User {

}
```

---

Utility:

```jsx
function sum() {

}
```

---

Not React functions.

---

# Rules Of Hooks Summary

---

# 16. Allowed

```jsx
function App() {

 useState();

 useEffect();

}
```

---

```jsx
function useAuth() {

 useContext();

}
```

---

Correct.

---

# 17. Not Allowed

Inside:

```text
if

for

while

switch

nested functions

event handlers

try/catch
```

---

# Why try/catch Is Not Allowed?

---

# 18. Example

Wrong:

```jsx
try {

 useEffect();

}
catch {

}
```

---

Reason:

```text
Execution May Skip
```

---

Hook order can change.

---

React forbids it.

---

# React Fiber Internals

---

# 19. Hook Linked List

React stores hooks inside:

```text
Fiber Node
```

---

Structure:

```text
Fiber

 ↓

Hook

 ↓

Hook

 ↓

Hook
```

---

Linked list.

---

# Example

```jsx
useState()

useEffect()

useMemo()
```

---

Stored as:

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

# 20. React Render Process

Render begins:

```text
currentHook = firstHook
```

---

Every hook call:

```text
Move To Next Hook
```

---

Example:

```jsx
useState()

useEffect()

useMemo()
```

---

Traversal:

```text
Hook 1

Hook 2

Hook 3
```

---

Must always remain:

```text
Same Order
```

---

# Hook Index Concept

---

# 21. Simplified Mental Model

React behaves like:

```text
Hook[0]

Hook[1]

Hook[2]
```

---

Example:

```jsx
useState()
```

---

Stored:

```text
Hook[0]
```

---

Next:

```jsx
useEffect()
```

---

Stored:

```text
Hook[1]
```

---

Next:

```jsx
useMemo()
```

---

Stored:

```text
Hook[2]
```

---

Order matters.

---

# ESLint Rules Of Hooks

---

# 22. React Provides Plugin

Package:

```bash
eslint-plugin-react-hooks
```

---

Detects:

```text
Conditional Hooks

Loop Hooks

Nested Hooks
```

---

Before runtime.

---

# Example Error

```text
React Hook useEffect
is called conditionally
```

---

Very common.

---

# React Strict Mode

---

# 23. Why Strict Mode Helps?

Strict Mode intentionally:

```text
Double Invokes
Some Logic
```

---

Helps detect:

```text
Broken Effects

Broken Hooks
```

---

Useful during development.

---

# Common Mistakes

---

# 24. Calling Hook In Condition

Wrong:

```jsx
if(show) {

 useEffect();

}
```

---

# 25. Calling Hook In Loop

Wrong:

```jsx
for(...) {

 useState();

}
```

---

# 26. Calling Hook In Event

Wrong:

```jsx
onClick(() => {

 useState();

})
```

---

# 27. Calling Hook In Utility Function

Wrong:

```jsx
function helper() {

 useState();

}
```

---

# 28. Calling Hook After Return

Wrong:

```jsx
if(!user)
 return null;

useEffect();
```

---

Hook may not execute.

---

Violation.

---

# Correct Pattern

---

# 29. Hook First

```jsx
function App() {

 useEffect(() => {

 });

 if(!user)
  return null;

}
```

---

Hooks before returns.

---

Correct.

---

# Real World Example

---

# 30. Authentication Component

Wrong:

```jsx
if(user) {

 useEffect(() => {

 });

}
```

---

Correct:

```jsx
useEffect(() => {

 if(user) {

 }

}, [user]);
```

---

Hook always runs.

---

Logic becomes conditional.

---

# Advanced Rule

---

# 31. Same Hook Count

Render 1:

```text
5 Hooks
```

---

Render 2:

```text
5 Hooks
```

---

Good.

---

Render 2:

```text
4 Hooks
```

---

Bad.

---

Render 2:

```text
7 Hooks
```

---

Bad.

---

Hook count must remain:

```text
Stable
```

---

# Interview Questions

---

### Q1. What are the two Rules of Hooks?

```text
1. Only Call Hooks At Top Level

2. Only Call Hooks From React Functions
```

---

### Q2. Why Can't Hooks Be Called Conditionally?

```text
Hook Order Changes
```

---

React loses state mapping.

---

### Q3. How Does React Track Hooks?

```text
By Position
```

---

Not variable names.

---

### Q4. Can Hooks Be Called In Loops?

```text
No
```

---

### Q5. Can Hooks Be Called In Event Handlers?

```text
No
```

---

### Q6. Can Custom Hooks Use Hooks?

```text
Yes
```

---

### Q7. Can Regular Functions Use Hooks?

```text
No
```

---

### Q8. Where Are Hooks Stored?

```text
React Fiber
```

---

### Q9. Which ESLint Plugin Checks Hook Rules?

```text
eslint-plugin-react-hooks
```

---

### Q10. Why Does Hook Order Matter?

```text
React Maps State
Using Hook Order
```

---

# Memory Tricks

### Hook Rule 1

```text
Top Level Only
```

---

### Hook Rule 2

```text
React Functions Only
```

---

### React Tracks Hooks By

```text
Position
```

---

### Never Use Hooks Inside

```text
if

for

while

switch

event handlers

nested functions

try/catch
```

---

### Always

```text
Call Hooks First

Then Logic
```

---

# Quick Revision

```text
Hook Rules
----------

Rule 1:

Only Call Hooks
At Top Level

Rule 2:

Only Call Hooks
Inside React Functions

Why?

React Tracks Hooks
By Order

Allowed:

✔ Components

✔ Custom Hooks

Not Allowed:

✘ if

✘ for

✘ while

✘ switch

✘ event handlers

✘ nested functions

✘ try/catch

React Internals:

Fiber
 ↓
Hook 1
 ↓
Hook 2
 ↓
Hook 3

Remember:

React Does NOT Track

count

name

user

React Tracks

Hook #1

Hook #2

Hook #3
```

---

# Final Advanced Hooks Map

```text
useState
 ↓
State

useEffect
 ↓
Side Effects

useContext
 ↓
Shared Data

useReducer
 ↓
Complex State

useRef
 ↓
Mutable References

useMemo
 ↓
Memoize Value

useCallback
 ↓
Memoize Function

useLayoutEffect
 ↓
Before Paint

useImperativeHandle
 ↓
Custom Ref API

useDebugValue
 ↓
Debug Custom Hooks

useId
 ↓
Unique Stable IDs

Custom Hooks
 ↓
Reusable Logic

Hook Rules
 ↓
How React Safely Tracks Hooks
```

---