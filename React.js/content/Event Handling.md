|# Event Handling

> **Goal:** Understand React Events, Synthetic Events, Event Binding, Passing Arguments to Events, Form Events, Keyboard Events, Mouse Events, Event Propagation, preventDefault(), stopPropagation(), internal working, real-world examples, best practices, and interview questions.

---

# 1. What is Event Handling?

Event Handling is the process of responding to user actions.

---

Examples:

```text
Button Click

Typing

Mouse Hover

Form Submit

Key Press

Scroll
```

---

React applications are:

```text
Interactive
```

---

Users constantly interact with UI.

---

React uses:

```text
Event Handlers
```

to respond to those interactions.

---

# 2. Real Life Example

Suppose user clicks:

```text
Login Button
```

---

Application should:

```text
Validate Data

Send API Request

Redirect User
```

---

This is achieved using:

```text
Event Handling
```

---

# 3. Event Flow

```text
User Action
      ↓

Event Triggered
      ↓

Event Handler Executes
      ↓

State Updated
      ↓

UI Re-rendered
```

---

# React Events

---

# 4. What are React Events?

React Events are special event handlers attached to JSX elements.

---

HTML:

```html
<button onclick="save()">
 Save
</button>
```

---

React:

```jsx
<button onClick={save}>
 Save
</button>
```

---

Notice:

```text
onClick
```

---

Uses:

```text
CamelCase
```

---

# 5. Common React Events

```text
onClick

onChange

onSubmit

onKeyDown

onKeyUp

onMouseEnter

onMouseLeave

onFocus

onBlur
```

---

# 6. First React Event Example

```jsx
function App() {

 function handleClick() {

  alert("Button Clicked");
 }

 return (

  <button

   onClick={handleClick}

  >

   Click

  </button>

 );
}
```

---

Output:

```text
Alert Opens
```

when button clicked.

---

# 7. Inline Event Handler

```jsx
<button

 onClick={() =>

  alert("Clicked")

 }

>

 Click

</button>
```

---

Works perfectly.

---

# 8. Function Reference vs Function Call

Correct:

```jsx
<button

 onClick={handleClick}

>
```

---

Wrong:

```jsx
<button

 onClick={handleClick()}

>
```

---

Why?

Because:

```text
Function Executes Immediately
```

during rendering.

---

Instead of click.

---

# Synthetic Events

---

# 9. What is a Synthetic Event?

One of the most important React interview topics.

---

Definition:

> SyntheticEvent is a wrapper around the browser's native event object.

---

Simple Meaning:

```text
React's Own Event System
```

---

# 10. Why React Uses Synthetic Events?

Different browsers behave differently.

---

Example:

```text
Chrome

Firefox

Safari

Edge
```

---

Event implementations vary.

---

React provides:

```text
Consistent API
```

across all browsers.

---

# 11. Example

```jsx
function handleClick(event) {

 console.log(event);
}
```

---

Here:

```text
event
```

is not native browser event.

---

It is:

```text
SyntheticEvent
```

---

# 12. Synthetic Event Benefits

```text
Cross Browser Compatibility

Consistent Behavior

Better Performance

Unified API
```

---

# 13. Accessing Native Event

Sometimes needed.

---

Example:

```jsx
function handleClick(event) {

 console.log(

  event.nativeEvent

 );
}
```

---

Provides:

```text
Actual Browser Event
```

---

# Event Binding

---

# 14. What is Event Binding?

Binding means:

```text
Connecting Event
To Handler Function
```

---

Example:

```jsx
<button

 onClick={handleClick}

>
```

---

Here:

```text
onClick
```

is bound to:

```text
handleClick
```

---

# 15. Event Binding in Functional Components

Simple.

---

Example:

```jsx
function App() {

 const handleClick = () => {

  alert("Clicked");
 };

 return (

  <button

   onClick={handleClick}

  >

   Click

  </button>

 );
}
```

---

No special binding required.

---

# 16. Event Binding in Class Components

Important interview topic.

---

Example:

```jsx
class App
extends React.Component {

 handleClick() {

  console.log("Clicked");
 }

 render() {

  return (

   <button

    onClick={this.handleClick}

   >

    Click

   </button>

  );
 }
}
```

---

Problem:

```text
this becomes undefined
```

---

# 17. Solution 1

Constructor Binding.

---

```jsx
constructor() {

 super();

 this.handleClick =

 this.handleClick.bind(this);
}
```

---

# 18. Solution 2

Arrow Function

---

```jsx
handleClick = () => {

 console.log("Clicked");
}
```

---

Most common.

---

# Passing Arguments to Events

---

# 19. Problem

Need to pass data.

---

Example:

```text
Delete User
```

---

Need:

```text
User ID
```

---

# 20. Wrong Approach

```jsx
<button

 onClick={deleteUser(1)}

>
 Delete
</button>
```

---

Executes immediately.

---

Wrong.

---

# 21. Correct Approach

```jsx
<button

 onClick={() =>

  deleteUser(1)

 }

>

 Delete

</button>
```

---

Function executes:

```text
Only On Click
```

---

# 22. Multiple Arguments

```jsx
<button

 onClick={() =>

  updateUser(

   1,

   "John"

  )

 }

>

 Update

</button>
```

---

Perfectly valid.

---

# 23. Passing Event and Argument

```jsx
<button

 onClick={(event) =>

  handleClick(

   event,

   1

  )

 }

>
 Click
</button>
```

---

Handler:

```jsx
function handleClick(

 event,

 id

) {

 console.log(id);
}
```

---

# Form Events

---

# 24. What are Form Events?

Events related to:

```text
Input

Form

Textarea

Select
```

---

Most common:

```text
onChange

onSubmit

onFocus

onBlur
```

---

# 25. onChange Event

Triggered when input value changes.

---

Example:

```jsx
<input

 onChange={handleChange}

/>
```

---

Handler:

```jsx
function handleChange(event) {

 console.log(

  event.target.value

 );
}
```

---

# 26. Controlled Input Example

```jsx
const [

 name,

 setName

]
=
useState("");
```

---

```jsx
<input

 value={name}

 onChange={(e) =>

  setName(
   e.target.value
  )
 }

/>
```

---

React controls value.

---

# 27. onSubmit Event

```jsx
<form

 onSubmit={handleSubmit}

>
```

---

Handler:

```jsx
function handleSubmit(event) {

 event.preventDefault();

 console.log("Submitted");
}
```

---

# 28. Why preventDefault()?

Normally:

```text
Form Reloads Page
```

---

React applications:

```text
Avoid Page Refresh
```

---

Use:

```jsx
event.preventDefault();
```

---

# Keyboard Events

---

# 29. Keyboard Events Overview

Common keyboard events:

```text
onKeyDown

onKeyUp

onKeyPress (Deprecated)
```

---

# 30. onKeyDown Example

```jsx
<input

 onKeyDown={handleKey}

/>
```

---

Handler:

```jsx
function handleKey(event) {

 console.log(event.key);
}
```

---

Press:

```text
A
```

---

Output:

```text
A
```

---

# 31. Enter Key Example

```jsx
function handleKey(event) {

 if(event.key === "Enter") {

  console.log("Search");
 }
}
```

---

Common interview example.

---

# 32. Keyboard Event Properties

```jsx
event.key

event.code

event.altKey

event.ctrlKey

event.shiftKey
```

---

Example:

```jsx
if(event.ctrlKey)
```

---

Detects:

```text
Ctrl Key
```

---

# Mouse Events

---

# 33. Mouse Events Overview

Common mouse events:

```text
onClick

onDoubleClick

onMouseEnter

onMouseLeave

onMouseMove

onContextMenu
```

---

# 34. Mouse Enter Example

```jsx
<div

 onMouseEnter={() =>

  console.log("Entered")
 }

>
 Hover Me
</div>
```

---

# 35. Mouse Leave Example

```jsx
<div

 onMouseLeave={() =>

  console.log("Left")
 }

>
 Hover Me
</div>
```

---

# 36. Mouse Move Example

```jsx
<div

 onMouseMove={(e) =>

  console.log(

   e.clientX,

   e.clientY

  )
 }

>
 Move Mouse
</div>
```

---

Tracks cursor position.

---

# Event Propagation

---

# 37. What is Event Propagation?

Events move through DOM hierarchy.

---

Example:

```text
Parent
  ↓

Child
```

---

Click child.

---

Parent may also receive event.

---

This is:

```text
Event Propagation
```

---

# 38. Example

```jsx
<div

 onClick={() =>

  console.log("Parent")
 }

>

 <button

  onClick={() =>

   console.log("Child")
  }

 >

  Click

 </button>

</div>
```

---

Click button.

---

Output:

```text
Child

Parent
```

---

Why?

Because event bubbles upward.

---

# 39. Event Bubbling

Default behavior.

---

Flow:

```text
Child
  ↑

Parent
  ↑

GrandParent
```

---

Moves:

```text
Bottom To Top
```

---

# 40. Event Capturing

Less commonly used.

---

Flow:

```text
GrandParent
  ↓

Parent
  ↓

Child
```

---

Moves:

```text
Top To Bottom
```

---

# 41. Capturing Example

```jsx
<div

 onClickCapture={() =>

  console.log("Parent")
 }

>
```

---

Uses:

```text
Capture Phase
```

---

# 42. Event Propagation Flow

```text
Capture Phase
      ↓

Target Phase
      ↓

Bubble Phase
```

---

Very important interview topic.

---

# preventDefault()

---

# 43. What is preventDefault()?

Stops browser's default behavior.

---

Example:

```text
Form Submission

Anchor Navigation
```

---

# 44. Form Example

```jsx
function handleSubmit(event) {

 event.preventDefault();

 console.log("Submitted");
}
```

---

Without:

```jsx
preventDefault()
```

---

Browser:

```text
Reloads Page
```

---

# 45. Anchor Example

```jsx
<a

 href="https://google.com"

 onClick={(e) =>

  e.preventDefault()
 }

>

 Google

</a>
```

---

Navigation prevented.

---

# 46. Common Use Cases

```text
Form Submit

Anchor Tags

Drag And Drop

Custom Navigation
```

---

# stopPropagation()

---

# 47. What is stopPropagation()?

Stops event from moving to parent elements.

---

# 48. Example

```jsx
<div

 onClick={() =>

  console.log("Parent")
 }

>

 <button

  onClick={(e) => {

   e.stopPropagation();

   console.log("Child");
  }}

 >

  Click

 </button>

</div>
```

---

Output:

```text
Child
```

---

Parent never executes.

---

# 49. Why Use stopPropagation()?

Suppose:

```text
Modal
```

inside:

```text
Overlay
```

---

Clicking modal:

```text
Should Not Close Overlay
```

---

Use:

```jsx
stopPropagation()
```

---

# 50. Real World Example

```text
Card
  ↓

Delete Button
```

---

Clicking delete:

```text
Delete Item
```

---

Should not trigger:

```text
Card Click Event
```

---

Use:

```jsx
stopPropagation()
```

---

# preventDefault() vs stopPropagation()

One of the most asked interview questions.

---

# 51. preventDefault()

Stops:

```text
Browser Default Behavior
```

---

Example:

```text
Form Reload

Anchor Navigation
```

---

# 52. stopPropagation()

Stops:

```text
Event Bubbling
```

---

Example:

```text
Parent Event Execution
```

---

# 53. Comparison

| Feature | preventDefault() | stopPropagation() |
|----------|----------|----------|
| Stops Browser Action | Yes | No |
| Stops Bubbling | No | Yes |
| Used In Forms | Yes | Sometimes |
| Used In Nested Elements | No | Yes |

---

# Interview Questions

---

### Q1. What are React Events?

```text
Events attached to JSX
for handling user actions.
```

---

### Q2. Why are React event names CamelCase?

```text
React convention.

Example:

onClick

onChange
```

---

### Q3. What is a Synthetic Event?

```text
React wrapper around
native browser events.
```

---

### Q4. Why does React use Synthetic Events?

```text
Cross-browser compatibility.
```

---

### Q5. How do you pass arguments to an event?

```jsx
onClick={() =>

 deleteUser(id)

}
```

---

### Q6. Difference between onClick={fn} and onClick={fn()}?

```jsx
onClick={fn}
```

Executes on click.

---

```jsx
onClick={fn()}
```

Executes immediately.

---

### Q7. What is Event Bubbling?

```text
Event travels
from child to parent.
```

---

### Q8. What is Event Capturing?

```text
Event travels
from parent to child.
```

---

### Q9. What does preventDefault() do?

```text
Stops browser's
default behavior.
```

---

### Q10. What does stopPropagation() do?

```text
Stops event bubbling.
```

---

### Q11. Difference between preventDefault() and stopPropagation()?

preventDefault:

```text
Stops Default Browser Action
```

---

stopPropagation:

```text
Stops Event Bubbling
```

---

# Real World Example

### Search Form

```jsx
function Search() {

 const [

  search,

  setSearch

 ]
 =
 useState("");

 function handleSubmit(event) {

  event.preventDefault();

  console.log(search);
 }

 return (

  <form

   onSubmit={handleSubmit}

  >

   <input

    value={search}

    onChange={(e) =>

     setSearch(
      e.target.value
     )
    }

   />

   <button>

    Search

   </button>

  </form>

 );
}
```

---

Uses:

```text
onChange

onSubmit

preventDefault()
```

---

# Memory Tricks

### Event Handling

```text
User Action → Function
```

---

### Synthetic Event

```text
React Event Wrapper
```

---

### Event Binding

```text
Connect Event To Function
```

---

### Form Events

```text
Input Handling
```

---

### Keyboard Events

```text
Key Detection
```

---

### Mouse Events

```text
Pointer Interaction
```

---

### Bubbling

```text
Child → Parent
```

---

### Capturing

```text
Parent → Child
```

---

### preventDefault()

```text
Stop Browser Action
```

---

### stopPropagation()

```text
Stop Event Travel
```

---

# Quick Revision

```text
Event Handling
--------------

User Action
      ↓
Event
      ↓
Handler
      ↓
State Update
      ↓
UI Update

Common Events
-------------

onClick

onChange

onSubmit

onKeyDown

onMouseEnter

onMouseLeave

Synthetic Event
---------------

React Wrapper
Around Native Events

Passing Arguments
-----------------

onClick={() =>

 fn(id)

}

Event Propagation
-----------------

Capture
 ↓

Target
 ↓

Bubble

Bubbling
---------

Child → Parent

Capturing
----------

Parent → Child

preventDefault()
----------------

Stop Browser Action

Examples:

Form Reload

Anchor Navigation

stopPropagation()
-----------------

Stop Event Bubbling

Remember
---------

Synthetic Event
 ↓
Cross Browser

preventDefault()
 ↓
Stop Default Action

stopPropagation()
 ↓
Stop Bubbling

Event Flow
----------

Capture
 ↓

Target
 ↓

Bubble
```