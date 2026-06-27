# useLayoutEffect Hook

> **Goal:** Understand useLayoutEffect from scratch, why React created it, Browser Rendering Pipeline, DOM Measurements, Layout Calculations, Paint Blocking, Flickering Prevention, Internal Working, Real-World Examples, Performance Impact, Common Mistakes, Best Practices, Interview Questions, and React Internals.

---

# 1. What Problem Does useLayoutEffect Solve?

Before understanding:

```jsx
useLayoutEffect()
```

you must understand:

```jsx
useEffect()
```

---

Recall:

```jsx
useEffect(() => {

 // logic

}, []);
```

---

React executes:

```text
Render Component
       ↓
Update DOM
       ↓
Browser Paint
       ↓
useEffect Runs
```

---

Important:

```text
Browser Paint Happens First
```

---

Then:

```text
useEffect Executes
```

---

Most of the time:

```text
Perfectly Fine
```

---

But sometimes we need:

```text
Read DOM Measurements

Modify Layout

Calculate Position

Resize Elements

Prevent Flickering
```

---

Before:

```text
User Sees Screen
```

---

Need a solution.

---

React provides:

```jsx
useLayoutEffect()
```

---

# 2. What is useLayoutEffect?

Definition:

> useLayoutEffect is a React Hook that runs synchronously after DOM updates but before the browser paints the screen.

---

Simple Meaning:

```text
Run After DOM Update

Before Paint
```

---

# 3. Why Was It Created?

Imagine:

```text
Render Modal
```

---

Need:

```text
Measure Width

Measure Height

Calculate Position
```

---

If using:

```jsx
useEffect()
```

---

Flow:

```text
Render
 ↓

Paint
 ↓

Measure
 ↓

Move Element
```

---

User sees:

```text
Wrong Position
```

for a moment.

---

Called:

```text
UI Flicker
```

---

Need:

```text
Measure First

Paint Later
```

---

# 4. Browser Rendering Pipeline

Very important interview topic.

---

When UI updates:

```text
JavaScript Executes
       ↓
DOM Updates
       ↓
Layout Calculation
       ↓
Paint
       ↓
Screen Display
```

---

Browser workflow:

```text
Render
 ↓

DOM Updated
 ↓

Layout
 ↓

Paint
 ↓

User Sees UI
```

---

# 5. Where useEffect Runs

```text
Render
 ↓

DOM Update
 ↓

Paint
 ↓

useEffect
```

---

User already sees UI.

---

# 6. Where useLayoutEffect Runs

```text
Render
 ↓

DOM Update
 ↓

useLayoutEffect
 ↓

Paint
```

---

User never sees intermediate state.

---

# Visualization

---

## useEffect

```text
Render
 ↓

DOM Update
 ↓

Paint
 ↓

Effect
```

---

## useLayoutEffect

```text
Render
 ↓

DOM Update
 ↓

Layout Effect
 ↓

Paint
```

---

Huge difference.

---

# Syntax

---

# 7. Syntax

```jsx
useLayoutEffect(() => {

 // logic

}, []);
```

---

Looks almost identical to:

```jsx
useEffect()
```

---

Difference:

```text
Execution Timing
```

---

# First Example

---

# 8. Example

```jsx
useLayoutEffect(() => {

 console.log(
  "Layout Effect"
 );

}, []);
```

---

Runs:

```text
Before Browser Paint
```

---

# 9. Compare With useEffect

```jsx
useEffect(() => {

 console.log(
  "Effect"
 );

}, []);
```

---

Runs:

```text
After Paint
```

---

# DOM Measurement Example

---

# 10. Most Common Use Case

Measure element dimensions.

---

Example:

```jsx
<div ref={boxRef}>
 Hello
</div>
```

---

Need:

```text
Width

Height
```

---

# 11. Using useRef

```jsx
const boxRef =
useRef();
```

---

# 12. Measure Element

```jsx
useLayoutEffect(() => {

 const width =
 boxRef.current
 .offsetWidth;

 console.log(width);

}, []);
```

---

Runs before paint.

---

Accurate measurement.

---

# Why Not useEffect?

---

# 13. Problem

Suppose:

```jsx
useEffect(() => {

 const width =
 boxRef.current
 .offsetWidth;

}, []);
```

---

Flow:

```text
Paint First
 ↓

Measure Later
```

---

May cause:

```text
Visible Layout Shift
```

---

# Tooltip Example

---

# 14. Real-World Example

Tooltips often need:

```text
Target Position

Tooltip Width

Tooltip Height
```

---

Before display.

---

# 15. Tooltip Flow

Wrong:

```text
Show Tooltip
 ↓

Measure
 ↓

Move Tooltip
```

---

User sees:

```text
Jumping Tooltip
```

---

Bad UX.

---

Correct:

```text
Measure
 ↓

Position
 ↓

Paint
```

---

Using:

```jsx
useLayoutEffect()
```

---

# Auto Resize Example

---

# 16. Text Area Resize

Need:

```text
Calculate Content Height
```

---

Then:

```text
Resize Text Area
```

---

Before paint.

---

Perfect candidate.

---

# Scroll Position Example

---

# 17. Preserve Scroll Position

Suppose:

```text
Chat Application
```

---

New message arrives.

---

Need:

```text
Scroll To Bottom
```

---

Before user sees update.

---

# Example

```jsx
useLayoutEffect(() => {

 containerRef.current
 .scrollTop =
 containerRef.current
 .scrollHeight;

}, [messages]);
```

---

Smooth experience.

---

# useRef + useLayoutEffect

---

# 18. Common Combination

Usually:

```text
useRef
     +
useLayoutEffect
```

---

Because:

```text
Need DOM Access
```

---

Example:

```jsx
const ref =
useRef();
```

---

Then:

```jsx
useLayoutEffect(() => {

 ref.current.focus();

}, []);
```

---

# Internal Working

---

# 19. React Render Phases

React has:

```text
Render Phase

Commit Phase
```

---

# Render Phase

React calculates:

```text
Virtual DOM
```

---

No DOM changes yet.

---

# Commit Phase

React:

```text
Updates DOM
```

---

Then:

```text
Runs Effects
```

---

# 20. Execution Order

```text
Render Phase
       ↓

Commit DOM
       ↓

useLayoutEffect
       ↓

Browser Paint
       ↓

useEffect
```

---

Very important.

---

# Visualization

```text
Render
 ↓

DOM Commit
 ↓

Layout Effect
 ↓

Paint
 ↓

Effect
```

---

# Cleanup Function

---

# 21. Cleanup Support

Like useEffect:

```jsx
useLayoutEffect(() => {

 return () => {

 };

}, []);
```

---

Supports cleanup.

---

# 22. Example

```jsx
useLayoutEffect(() => {

 console.log("Mounted");

 return () => {

  console.log(
   "Cleanup"
  );

 };

}, []);
```

---

# Cleanup Timing

When:

```text
Dependency Changes

OR

Component Unmounts
```

---

Cleanup executes.

---

# Dependency Array Behavior

---

# 23. No Dependency Array

```jsx
useLayoutEffect(() => {

}, );
```

---

Runs:

```text
Every Render
```

---

# 24. Empty Dependency Array

```jsx
useLayoutEffect(() => {

}, []);
```

---

Runs:

```text
Once
```

---

After mount.

---

# 25. Dependencies

```jsx
useLayoutEffect(() => {

}, [count]);
```

---

Runs when:

```text
count changes
```

---

# useLayoutEffect vs useEffect

---

# 26. Most Important Interview Question

| Feature | useEffect | useLayoutEffect |
|----------|------------|----------------|
| DOM Updated | Yes | Yes |
| Before Paint | No | Yes |
| After Paint | Yes | No |
| Blocks Paint | No | Yes |
| Layout Calculations | No | Yes |
| DOM Measurement | Rarely | Yes |
| Performance Cost | Lower | Higher |

---

# 27. Visual Comparison

## useEffect

```text
Render
 ↓

DOM Update
 ↓

Paint
 ↓

Effect
```

---

## useLayoutEffect

```text
Render
 ↓

DOM Update
 ↓

Layout Effect
 ↓

Paint
```

---

# 28. Memory Trick

```text
useEffect
 ↓

After Paint

useLayoutEffect
 ↓

Before Paint
```

---

# Why Can useLayoutEffect Be Dangerous?

---

# 29. Paint Blocking

Remember:

```text
useLayoutEffect
Blocks Paint
```

---

Browser cannot paint until:

```text
Effect Finishes
```

---

# 30. Example

```jsx
useLayoutEffect(() => {

 for(
  let i=0;
  i<1000000000;
  i++
 ) {}

}, []);
```

---

Result:

```text
Frozen UI
```

---

Bad experience.

---

# Rule

```text
Keep Layout Effects Fast
```

---

# Common Use Cases

---

# 31. DOM Measurements

Examples:

```text
Width

Height

Position

Bounding Rectangle
```

---

# 32. Scroll Synchronization

Examples:

```text
Chat Apps

Logs

Feeds
```

---

# 33. Tooltip Positioning

Examples:

```text
Tooltips

Dropdowns

Popups

Context Menus
```

---

# 34. Animation Setup

Examples:

```text
GSAP

Framer Motion

Custom Animations
```

---

Need measurements before paint.

---

# React Strict Mode

---

# 35. Development Behavior

In Strict Mode:

```text
Layout Effect
May Run Twice
```

---

Development only.

---

Used to detect:

```text
Side Effect Bugs
```

---

Production:

```text
Runs Normally
```

---

# Common Mistakes

---

# 36. Using useLayoutEffect Everywhere

Wrong.

---

Example:

```jsx
useLayoutEffect(() => {

 fetch("/users");

}, []);
```

---

No DOM measurement.

---

Use:

```jsx
useEffect()
```

instead.

---

# 37. API Calls Inside Layout Effects

Bad.

---

Because:

```text
Blocks Paint
```

---

User waits longer.

---

# 38. Heavy Computation

Wrong:

```jsx
useLayoutEffect(() => {

 expensiveTask();

}, []);
```

---

Causes:

```text
UI Delay
```

---

# Best Practices

---

# 39. Prefer useEffect

Default choice:

```text
useEffect
```

---

Most effects belong there.

---

# 40. Use useLayoutEffect Only When Needed

Examples:

```text
DOM Measurement

Scroll Management

Position Calculation
```

---

# 41. Keep It Fast

Avoid:

```text
Heavy Loops

API Calls

Large Computations
```

---

# 42. Combine With useRef

Common pattern:

```jsx
useRef()
+
useLayoutEffect()
```

---

Used in most production applications.

---

# Real-World Example

---

# 43. Modal Centering

Need:

```text
Modal Width

Modal Height
```

---

Before:

```text
Display Modal
```

---

Use:

```jsx
useLayoutEffect()
```

---

Calculate:

```text
Center Position
```

---

Then:

```text
Paint
```

---

No flicker.

---

# React Internals

---

# 44. Fiber Commit Phase

React Fiber commit process:

```text
Commit DOM
       ↓

Layout Effects
       ↓

Paint
       ↓

Passive Effects
```

---

Passive effects:

```text
useEffect
```

---

Layout effects:

```text
useLayoutEffect
```

---

# 45. Why React Separates Them

Because:

```text
DOM Measurement
```

must happen:

```text
Before Paint
```

---

But:

```text
API Calls

Logging

Analytics
```

can happen:

```text
After Paint
```

---

Better performance.

---

# Interview Questions

---

### Q1. What is useLayoutEffect?

```text
Hook That Runs
After DOM Update
But Before Paint
```

---

### Q2. Difference Between useEffect and useLayoutEffect?

```text
useEffect
 ↓
After Paint

useLayoutEffect
 ↓
Before Paint
```

---

### Q3. Does useLayoutEffect Block Paint?

```text
Yes
```

---

### Q4. Most Common Use Case?

```text
DOM Measurement
```

---

### Q5. Can API Calls Be Made Inside useLayoutEffect?

```text
Technically Yes

Practically Avoid
```

---

### Q6. Which Hook Is Faster?

```text
useEffect
```

---

Because:

```text
Doesn't Block Paint
```

---

### Q7. Which Hook Is Used For Measuring Width?

```text
useLayoutEffect
```

---

### Q8. What Runs First?

```text
useLayoutEffect
```

---

Then:

```text
Browser Paint
```

---

Then:

```text
useEffect
```

---

### Q9. Is useLayoutEffect Common?

```text
No
```

---

Much less common than:

```text
useEffect
```

---

### Q10. Can useLayoutEffect Cause Performance Issues?

```text
Yes
```

---

Because it blocks painting.

---

# Memory Tricks

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

### DOM Measurement

```text
useLayoutEffect
```

---

### API Calls

```text
useEffect
```

---

### Tooltip Positioning

```text
useLayoutEffect
```

---

### Scroll Adjustment

```text
useLayoutEffect
```

---

### Rule

```text
Need DOM Measurement?
 ↓

useLayoutEffect

Otherwise
 ↓

useEffect
```

---

# Quick Revision

```text
useLayoutEffect
---------------

Purpose:

Run Logic Before Browser Paint

Timing:

Render
 ↓

DOM Update
 ↓

useLayoutEffect
 ↓

Paint
 ↓

useEffect

Most Common Uses:

✔ DOM Measurement

✔ Width Calculation

✔ Height Calculation

✔ Tooltip Positioning

✔ Scroll Management

✔ Animation Setup

Advantages:

✔ No Flickering

✔ Accurate Measurements

✔ Smoother Layout Updates

Disadvantages:

✘ Blocks Paint

✘ Can Hurt Performance

✘ Overuse Is Dangerous

Remember:

useEffect
 ↓
After Paint

useLayoutEffect
 ↓
Before Paint

Default Choice
 ↓
useEffect

Special Layout Work
 ↓
useLayoutEffect
```

---

# Final Hook Flow Map

```text
useState
 ↓
State Management

useEffect
 ↓
Side Effects

useContext
 ↓
Global Data

useReducer
 ↓
Complex State

useRef
 ↓
Mutable References

useMemo
 ↓
Memoize Values

useCallback
 ↓
Memoize Functions

useLayoutEffect
 ↓
DOM Measurement
Before Paint
```

---