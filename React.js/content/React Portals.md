# React Portals

> **Goal:** Master React Portals completely — understand what Portals are, why they exist, how React normally renders components, how Portals render outside the parent DOM hierarchy, creating portals, modal implementation, event bubbling behavior, internal working, real-world examples, advantages, disadvantages, best practices, and interview questions.

---

# 1. What are React Portals?

React normally renders components inside their parent DOM element.

Example:

```jsx
<App>
   <Dashboard>
      <Modal />
   </Dashboard>
</App>
```

---

Generated DOM:

```html
<div id="root">
    <div class="dashboard">
        <div class="modal">
            Modal Content
        </div>
    </div>
</div>
```

---

Modal becomes a child of:

```text
Dashboard
```

---

Normally this is fine.

---

But sometimes we need:

```text
Modal

Tooltip

Dropdown

Context Menu

Popup

Toast Notification
```

to appear outside the parent DOM tree.

---

React Portals solve this problem.

---

# Definition

> React Portals allow rendering a component into a DOM node that exists outside the parent component hierarchy.

---

# Simple Meaning

Normally:

```text
Parent
  ↓
Child
  ↓
Rendered Inside Parent
```

---

Portal:

```text
Parent
  ↓
Child
  ↓
Rendered Somewhere Else
```

---

# Real Life Analogy

Imagine:

```text
A room inside a house.
```

---

Normally:

```text
Furniture stays inside room.
```

---

Portal:

```text
Furniture appears outside room
but still belongs to room.
```

---

That's exactly how Portals work.

---

# Why Do We Need Portals?

Suppose:

```jsx
<div className="container">

   <Modal />

</div>
```

---

Container CSS:

```css
.container {
   overflow: hidden;
}
```

---

Problem:

```text
Modal gets clipped.
```

---

User sees:

```text
Broken Popup
```

---

Need modal to appear above everything.

---

Portal fixes this.

---

# Visualization

Without Portal:

```text
root
 └── App
      └── Container
            └── Modal
```

---

With Portal:

```text
root
 └── App
      └── Container

modal-root
 └── Modal
```

---

Still React child.

---

But different DOM location.

---

# Common Use Cases

---

# 2. Real World Examples

---

## Modal Dialogs

```text
Login Modal

Confirmation Modal

Delete Modal
```

---

Most common use case.

---

## Tooltips

```text
Hover Tooltip
```

---

Should appear above all elements.

---

## Dropdown Menus

```text
Profile Menu

Mega Menu
```

---

Need to escape parent boundaries.

---

## Toast Notifications

```text
Success Message

Error Notification
```

---

Often rendered globally.

---

## Context Menus

```text
Right Click Menu
```

---

Portal is ideal.

---

# Creating Portals

---

# 3. Portal Architecture

First create another DOM node.

---

Example:

```html
<body>

   <div id="root"></div>

   <div id="portal-root"></div>

</body>
```

---

React app:

```text
root
```

---

Portal destination:

```text
portal-root
```

---

# ReactDOM.createPortal()

React provides:

```jsx
createPortal()
```

---

Syntax:

```jsx
createPortal(
   children,
   domNode
)
```

---

Parameters:

```text
children
```

What to render.

---

```text
domNode
```

Where to render.

---

# Example

```jsx
import { createPortal }
from "react-dom";

function PortalExample() {

 return createPortal(

   <h1>Hello Portal</h1>,

   document.getElementById(
      "portal-root"
   )

 );

}
```

---

Result:

```html
<div id="portal-root">
   <h1>Hello Portal</h1>
</div>
```

---

Even if component exists elsewhere in React tree.

---

# Internal Working

React Tree:

```text
App
 ↓

Dashboard
 ↓

PortalComponent
```

---

DOM Tree:

```text
root
 └── Dashboard

portal-root
 └── PortalComponent
```

---

Important:

```text
React Tree ≠ DOM Tree
```

---

Very important interview question.

---

# React Tree vs DOM Tree

---

# 4. Understanding the Difference

React internally maintains:

```text
Component Tree
```

---

Example:

```jsx
<App>
   <Dashboard>
      <Modal />
   </Dashboard>
</App>
```

---

React Tree:

```text
App
 ↓

Dashboard
 ↓

Modal
```

---

Even if Modal uses Portal.

---

DOM Tree:

```text
root
 └── Dashboard

portal-root
 └── Modal
```

---

React still treats:

```text
Modal
```

as child of:

```text
Dashboard
```

---

This becomes important for:

```text
Context

State

Props

Events
```

---

All continue working normally.

---

# Modal Implementation

---

# 5. Why Portals Are Perfect For Modals?

A modal should:

```text
Appear Above Everything

Ignore Parent CSS

Ignore Overflow Hidden

Cover Entire Screen
```

---

Portal achieves this.

---

# Create Modal Root

```html
<body>

   <div id="root"></div>

   <div id="modal-root"></div>

</body>
```

---

# Modal Component

```jsx
import { createPortal }
from "react-dom";

function Modal() {

 return createPortal(

   <div className="modal">

      <h2>

         Login

      </h2>

   </div>,

   document.getElementById(
      "modal-root"
   )

 );

}
```

---

Usage:

```jsx
function App() {

 return (

   <>
      <Dashboard />
      <Modal />
   </>

 );

}
```

---

Modal appears inside:

```text
modal-root
```

---

Not inside dashboard.

---

# Modal Overlay Example

```jsx
function Modal() {

 return createPortal(

  <div className="overlay">

      <div className="modal">

         Modal Content

      </div>

  </div>,

  document.getElementById(
   "modal-root"
  )

 );

}
```

---

CSS:

```css
.overlay {
   position: fixed;
   inset: 0;
}
```

---

Covers full screen.

---

# Portal Rendering Flow

---

# 6. Internal Rendering Process

Step 1:

```text
React renders Modal component.
```

---

Step 2:

```text
createPortal() executes.
```

---

Step 3:

```text
React locates target DOM node.
```

---

Step 4:

```text
React injects JSX there.
```

---

Result:

```text
React Parent

AND

Different DOM Location
```

---

Both simultaneously.

---

# Event Bubbling with Portals

---

# 7. Most Important Interview Topic

Many developers think:

```text
Portal changes event behavior.
```

---

Wrong.

---

Events still bubble through:

```text
React Component Tree
```

---

Not DOM Tree.

---

Extremely important.

---

# Example

Parent:

```jsx
function App() {

 return (

  <div
   onClick={() =>
    console.log("Parent")
   }
  >

   <Modal />

  </div>

 );

}
```

---

Modal:

```jsx
function Modal() {

 return createPortal(

  <button>

   Click Me

  </button>,

  document.getElementById(
   "portal-root"
  )

 );

}
```

---

User clicks:

```text
Button
```

---

Output:

```text
Parent
```

---

Even though button is not inside parent DOM.

---

# Why?

React Tree:

```text
App
 ↓

Modal
 ↓

Button
```

---

DOM Tree:

```text
root
 └── App

portal-root
 └── Button
```

---

React uses:

```text
Component Tree
```

for event propagation.

---

Not DOM structure.

---

# Event Bubbling Flow

```text
Button Click
      ↓

Modal
      ↓

App
```

---

Even across portals.

---

# Stopping Event Bubbling

Works normally.

---

Example:

```jsx
<button

 onClick={(e)=>{

   e.stopPropagation();

 }}

>
 Click
</button>
```

---

Prevents parent event execution.

---

# Context API with Portals

---

# 8. Context Still Works

Many interviewers ask:

```text
Can Portal access Context?
```

---

Answer:

```text
YES
```

---

Example:

```jsx
<UserContext.Provider>

   <Modal />

</UserContext.Provider>
```

---

Modal:

```jsx
const user =
useContext(UserContext);
```

---

Works perfectly.

---

Reason:

```text
Portal remains
inside React Tree.
```

---

# State and Props with Portals

---

# 9. State Still Works

Parent:

```jsx
const [open,
setOpen] =
useState(true);
```

---

Pass to Modal:

```jsx
<Modal
 open={open}
/>
```

---

Works normally.

---

Portal affects:

```text
DOM Placement Only
```

---

Not:

```text
Props

State

Context

Events
```

---

# Advantages

---

# 10. Benefits of Portals

---

## Escape Parent Boundaries

```text
Overflow Hidden
```

no longer a problem.

---

## Better Modal Rendering

Perfect for:

```text
Dialogs

Popups

Tooltips
```

---

## Cleaner DOM Structure

Dedicated:

```text
modal-root

tooltip-root
```

---

Possible.

---

## State Works Normally

No extra complexity.

---

## Context Works Normally

No special handling.

---

# Disadvantages

---

# 11. Drawbacks

---

## Additional DOM Node Needed

Need:

```html
<div id="portal-root"></div>
```

---

## More Complex Debugging

DOM location differs from React location.

---

Can confuse beginners.

---

## Positioning Challenges

Sometimes CSS positioning becomes tricky.

---

Need proper:

```css
z-index
```

management.

---

# Common Mistakes

---

# 12. Forgetting Portal Root

Wrong:

```jsx
document.getElementById(
 "portal-root"
)
```

---

If:

```text
portal-root
```

doesn't exist.

---

Portal fails.

---

# Using Portals Everywhere

Wrong:

```text
Every Component
```

---

Portal should be used only when needed.

---

Best for:

```text
Modals

Tooltips

Notifications
```

---

# Mixing DOM Assumptions

Developers assume:

```text
Portal breaks events.
```

---

False.

---

Events still bubble.

---

# Best Practices

---

# 13. Production Recommendations

✔ Create dedicated portal roots

✔ Use portals for overlays

✔ Use portals for modals

✔ Keep portal components reusable

✔ Manage z-index carefully

✔ Remember event bubbling still works

✔ Remember context still works

---

# Real World Portal Architecture

```text
body
 ├── root
 │    └── React App
 │
 ├── modal-root
 │    └── Modals
 │
 ├── tooltip-root
 │    └── Tooltips
 │
 └── toast-root
      └── Notifications
```

---

# Interview Questions

---

### Q1. What is a React Portal?

```text
A way to render a component
outside its parent DOM hierarchy.
```

---

### Q2. Which API creates portals?

```jsx
createPortal()
```

---

### Q3. Why use Portals?

```text
Modals

Tooltips

Popups

Notifications
```

---

### Q4. Does a Portal change the React Tree?

```text
NO
```

---

Only DOM placement changes.

---

### Q5. Does Context work inside Portals?

```text
YES
```

---

### Q6. Does State work inside Portals?

```text
YES
```

---

### Q7. How do events bubble?

```text
Through React Tree
```

---

Not DOM Tree.

---

### Q8. Can stopPropagation work?

```text
YES
```

---

Works normally.

---

### Q9. What is the most common use case?

```text
Modal Dialog
```

---

### Q10. React Tree vs DOM Tree?

React Tree:

```text
Component Hierarchy
```

---

DOM Tree:

```text
Actual Browser DOM
```

---

Portal changes:

```text
DOM Tree
```

Only.

---

# Memory Tricks

### Portal

```text
Render Elsewhere
```

---

### createPortal()

```text
Move DOM Output
```

---

### Modal

```text
Most Common Use Case
```

---

### Context

```text
Still Works
```

---

### State

```text
Still Works
```

---

### Event Bubbling

```text
Uses React Tree
```

---

### React Tree

```text
Component Hierarchy
```

---

### DOM Tree

```text
Browser Structure
```

---

# Complete Portal Flow

```text
Parent Component
        ↓

Portal Component
        ↓

createPortal()
        ↓

Target DOM Node
        ↓

Rendered Outside Parent

BUT

Still Inside React Tree
        ↓

Props Work

State Works

Context Works

Events Bubble Normally
```

---

# Quick Revision

```text
React Portals
-------------

Purpose:
Render Outside Parent DOM

API:
createPortal()

Common Uses:

Modal
Tooltip
Popup
Toast

Portal Changes:

✔ DOM Location

Portal Does NOT Change:

✔ State
✔ Props
✔ Context
✔ React Tree

Event Bubbling:

Button
 ↓
Portal Component
 ↓
Parent Component

Uses React Tree

Most Common Interview Question:

Portal affects:
 ↓
DOM Tree

NOT:
 ↓
React Tree
```

---