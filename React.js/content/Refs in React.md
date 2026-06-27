# Refs in React

> **Goal:** Master React Refs completely — understand why Refs exist, how React normally works without Refs, createRef(), useRef(), DOM Access, Forward Refs, useImperativeHandle(), internal working, real-world examples, lifecycle behavior, best practices, common mistakes, and interview questions.

---

# 1. What are Refs?

React follows:

```text
Declarative Programming
```

---

Meaning:

Instead of manually manipulating DOM:

```javascript
document.getElementById()
```

---

We describe:

```text
What UI Should Look Like
```

and React updates DOM.

---

Example:

```jsx
const [count, setCount] =
useState(0);

return <h1>{count}</h1>;
```

---

React updates UI automatically.

---

Normally:

```text
React Controls DOM
```

---

But sometimes we need:

```text
Focus Input

Play Video

Scroll Element

Measure Height

Access Child Methods
```

---

For these cases:

```text
Refs
```

are used.

---

# Definition

> Refs provide a way to directly access DOM elements or React component instances.

---

# Simple Meaning

Normally:

```text
React → DOM
```

---

With Ref:

```text
Component
     ↓

Ref
     ↓

Direct DOM Access
```

---

# Real-Life Analogy

Suppose:

```text
React = Company Manager
```

---

Normally:

```text
Manager Handles Everything
```

---

Ref:

```text
Emergency Direct Phone Number
```

---

Used only when necessary.

---

# Why Refs Exist?

Some operations cannot be achieved using state.

---

Example:

Focus input.

---

Without Ref:

```jsx
setState()
```

cannot focus cursor.

---

Need:

```jsx
input.focus()
```

---

Ref solves this.

---

# Common Use Cases

---

# 2. Real World Uses

---

## Focus Input

```jsx
input.focus()
```

---

## Play Video

```jsx
video.play()
```

---

## Pause Video

```jsx
video.pause()
```

---

## Scroll Element

```jsx
div.scrollIntoView()
```

---

## Measure Element

```jsx
div.offsetHeight
```

---

## Trigger Child Methods

```jsx
child.openModal()
```

---

# Types of Refs

React provides:

```text
createRef()

useRef()
```

---

# createRef()

---

# 3. What is createRef()?

Used primarily in:

```text
Class Components
```

---

Creates a Ref object.

---

Syntax:

```jsx
React.createRef()
```

---

# Example

```jsx
class App extends React.Component {

 inputRef =
 React.createRef();

}
```

---

Generated object:

```javascript
{
 current: null
}
```

---

Initially:

```text
current = null
```

---

After mounting:

```text
current = DOM Element
```

---

# Attaching Ref

```jsx
<input
 ref={this.inputRef}
/>
```

---

React stores:

```text
Input DOM Node
```

inside:

```javascript
this.inputRef.current
```

---

# Accessing DOM

```jsx
this.inputRef.current.focus();
```

---

Input receives focus.

---

# Complete Example

```jsx
class App
extends React.Component {

 inputRef =
 React.createRef();

 focusInput = () => {

  this.inputRef.current.focus();

 };

 render() {

  return (

   <>
    <input
      ref={this.inputRef}
    />

    <button
      onClick={
       this.focusInput
      }
    >
      Focus
    </button>
   </>

  );

 }

}
```

---

Flow:

```text
Button Click
      ↓

Ref Access
      ↓

focus()
      ↓

Input Focused
```

---

# Limitations of createRef()

Every render creates:

```text
New Ref Object
```

---

Not ideal for function components.

---

Therefore React introduced:

```text
useRef()
```

---

# useRef()

---

# 4. What is useRef()?

Most common modern Ref API.

---

Used in:

```text
Functional Components
```

---

Syntax:

```jsx
const ref =
useRef(initialValue);
```

---

Example:

```jsx
const inputRef =
useRef(null);
```

---

Generated:

```javascript
{
 current: null
}
```

---

After mounting:

```javascript
{
 current: inputElement
}
```

---

# Example

```jsx
function App() {

 const inputRef =
 useRef(null);

 return (

  <input
   ref={inputRef}
  />

 );

}
```

---

React automatically stores:

```text
DOM Element
```

inside:

```javascript
inputRef.current
```

---

# Accessing DOM Elements

---

# 5. Accessing DOM Elements

Most common Ref use case.

---

Example:

```jsx
const inputRef =
useRef(null);
```

---

Attach:

```jsx
<input
 ref={inputRef}
/>
```

---

Access:

```jsx
inputRef.current.focus();
```

---

# Complete Example

```jsx
function App() {

 const inputRef =
 useRef(null);

 const handleClick =
 () => {

  inputRef.current.focus();

 };

 return (

  <>
   <input
    ref={inputRef}
   />

   <button
    onClick={
     handleClick
    }
   >
    Focus
   </button>
  </>

 );

}
```

---

Output:

```text
Input Gets Focus
```

---

# Accessing Value

```jsx
inputRef.current.value
```

---

Example:

```jsx
console.log(
 inputRef.current.value
);
```

---

Reads current value.

---

# Ref Lifecycle

---

# 6. Internal Working

Initial Render:

```javascript
{
 current:null
}
```

---

React Mounts:

```text
DOM Created
```

---

React Assigns:

```javascript
ref.current =
DOM Node
```

---

Now:

```jsx
ref.current.focus()
```

works.

---

# Visualization

```text
Render
  ↓

DOM Created
  ↓

Ref Attached
  ↓

current Updated
```

---

# Refs vs State

---

# 7. Most Important Difference

State:

```jsx
setCount()
```

---

Triggers:

```text
Re-render
```

---

Ref:

```jsx
ref.current = value
```

---

Does NOT trigger:

```text
Re-render
```

---

# Example

```jsx
const countRef =
useRef(0);

countRef.current++;
```

---

UI will NOT update.

---

Reason:

```text
Refs Are Mutable
```

---

React ignores ref changes.

---

# Comparison

| Feature | State | Ref |
|----------|---------|---------|
| Causes Re-render | Yes | No |
| Stores UI State | Yes | No |
| Mutable | No | Yes |
| DOM Access | No | Yes |
| Performance | More Costly | Cheap |

---

# Storing Mutable Values

---

# 8. Ref as Variable

Many developers use refs for:

```text
Previous Value

Timer ID

Socket Connection

Render Count
```

---

Example

```jsx
const renderCount =
useRef(0);

renderCount.current++;
```

---

No re-render.

---

Useful.

---

# Previous State Example

```jsx
const prevCount =
useRef();
```

---

Store previous value:

```jsx
useEffect(() => {

 prevCount.current =
 count;

}, [count]);
```

---

Now:

```jsx
prevCount.current
```

contains previous count.

---

# Forward Refs

---

# 9. Problem Without ForwardRef

Parent:

```jsx
<MyInput />
```

---

Inside:

```jsx
function MyInput() {

 return <input />;

}
```

---

Parent wants:

```jsx
focus()
```

---

But Ref cannot directly reach inner input.

---

Why?

Because:

```text
Ref Stops At Component Boundary
```

---

Need:

```text
Forward Ref
```

---

# What is Forward Ref?

Definition:

> Forward Ref allows a parent component to pass its ref to a child DOM element.

---

# Syntax

```jsx
forwardRef()
```

---

# Example

```jsx
const MyInput =

forwardRef(

 (props, ref) => {

  return (

   <input
    ref={ref}
   />

  );

 }

);
```

---

Parent:

```jsx
const inputRef =
useRef(null);

<MyInput
 ref={inputRef}
/>
```

---

Now:

```jsx
inputRef.current.focus();
```

works.

---

# Internal Flow

Without ForwardRef:

```text
Parent Ref
     ↓

Child Component

❌ Stops
```

---

With ForwardRef:

```text
Parent Ref
      ↓

forwardRef
      ↓

DOM Element
```

---

Success.

---

# Real World Example

Custom Input Component.

---

```jsx
<MyInput />
```

---

Parent still wants:

```jsx
focus()
```

---

Forward Ref solves this.

---

# useImperativeHandle()

---

# 10. Why useImperativeHandle Exists?

Suppose:

```jsx
Parent
 ↓

Child
```

---

Parent accesses:

```jsx
ref.current
```

---

By default:

```text
Entire DOM Element Exposed
```

---

Sometimes:

```text
Too Much Access
```

---

Need controlled exposure.

---

Solution:

```text
useImperativeHandle()
```

---

# Definition

> useImperativeHandle customizes what a parent receives when accessing a forwarded ref.

---

# Syntax

```jsx
useImperativeHandle(
 ref,
 () => ({
 })
);
```

---

# Example

Child:

```jsx
const MyInput =

forwardRef(

 (props, ref) => {

  const inputRef =
  useRef();

  useImperativeHandle(

   ref,

   () => ({

    focusInput() {

     inputRef.current.focus();

    }

   })

  );

  return (

   <input
    ref={inputRef}
   />

  );

 }

);
```

---

Parent:

```jsx
const ref =
useRef();
```

---

Call:

```jsx
ref.current.focusInput();
```

---

Works.

---

# Internal Flow

Without useImperativeHandle:

```text
Parent
 ↓

Gets Entire Input
```

---

With useImperativeHandle:

```text
Parent
 ↓

Gets Only Exposed Methods
```

---

Safer.

---

# Real World Example

Modal Component

---

Expose:

```jsx
open()

close()
```

---

Hide:

```text
Internal State

DOM Structure
```

---

Perfect use case.

---

# Complete Example

```jsx
const Modal =

forwardRef(

 (props, ref) => {

  useImperativeHandle(

   ref,

   () => ({

    open() {

     console.log(
      "Open Modal"
     );

    }

   })

  );

  return null;

 }

);
```

---

Parent:

```jsx
modalRef.current.open();
```

---

Only open exposed.

---

# Refs vs State vs Props

---

# 11. Comparison

| Feature | Props | State | Ref |
|----------|--------|--------|--------|
| Read Only | Yes | No | No |
| Re-render | Yes | Yes | No |
| DOM Access | No | No | Yes |
| Mutable | No | Yes | Yes |
| Parent Communication | Yes | Limited | Yes |

---

# Common Mistakes

---

# 12. Using Ref Instead of State

Wrong:

```jsx
const countRef =
useRef(0);
```

---

Display:

```jsx
<h1>
 {countRef.current}
</h1>
```

---

Won't update UI.

---

Need:

```jsx
useState()
```

---

# Accessing Ref Before Mount

Wrong:

```jsx
ref.current.focus();
```

during render.

---

Current:

```text
null
```

---

Use:

```jsx
useEffect()
```

---

# Excessive DOM Manipulation

Wrong:

```jsx
document.getElementById()
```

with React.

---

Prefer:

```jsx
Refs
```

only when needed.

---

# Best Practices

---

# 13. Recommendations

✔ Use useRef in functional components

✔ Use refs for DOM access

✔ Use refs for mutable values

✔ Use ForwardRef in reusable components

✔ Use useImperativeHandle carefully

✔ Prefer state for UI updates

✔ Avoid direct DOM manipulation when possible

---

# Interview Questions

---

### Q1. What is a Ref?

```text
Direct Reference To
DOM Element Or Component
```

---

### Q2. Difference between createRef and useRef?

createRef:

```text
Class Components
```

---

useRef:

```text
Functional Components
```

---

### Q3. Does changing ref.current cause re-render?

```text
NO
```

---

### Q4. When should useRef be used?

```text
DOM Access

Mutable Values

Previous Values

Timers
```

---

### Q5. What is ForwardRef?

```text
Pass Ref Through Component
```

---

### Q6. Why useImperativeHandle?

```text
Control Ref Exposure
```

---

### Q7. What is the default value of a Ref?

```text
null
```

---

### Q8. State vs Ref?

State:

```text
Triggers Re-render
```

---

Ref:

```text
Does Not
```

---

### Q9. Can useRef store values?

```text
YES
```

---

### Q10. Which is preferred for DOM access?

```text
useRef
```

---

# Memory Tricks

### createRef

```text
Class Components
```

---

### useRef

```text
Functional Components
```

---

### Ref

```text
Direct DOM Access
```

---

### ForwardRef

```text
Pass Ref Down
```

---

### useImperativeHandle

```text
Control Ref Exposure
```

---

### State

```text
Re-render
```

---

### Ref

```text
No Re-render
```

---

# Complete Ref Flow

```text
Component
     ↓

useRef()

     ↓

ref.current

     ↓

DOM Element

     ↓

focus()

scroll()

play()

pause()
```

---

ForwardRef Flow:

```text
Parent
   ↓

Ref

   ↓

forwardRef

   ↓

Child DOM Element
```

---

useImperativeHandle Flow:

```text
Parent
   ↓

Ref

   ↓

useImperativeHandle

   ↓

Custom Methods Only
```

---

# Quick Revision

```text
Refs
-----

Purpose:
Direct DOM Access

APIs:

createRef()
 ↓
 Class Components

useRef()
 ↓
 Functional Components

Common Uses:

Focus Input

Scroll Element

Play Video

Store Mutable Values

ForwardRef
 ↓
 Pass Ref To Child

useImperativeHandle
 ↓
 Customize Ref API

Important:

State Change
 ↓
 Re-render

Ref Change
 ↓
 No Re-render

Remember:

Ref
 ↓
 Direct Access

ForwardRef
 ↓
 Pass Ref

useImperativeHandle
 ↓
 Expose Limited Methods
```

---