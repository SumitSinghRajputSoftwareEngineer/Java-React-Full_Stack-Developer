# Props

> **Goal:** Understand Props from scratch, why they exist, how data flows between components, Passing Props, Default Props, Props Destructuring, Children Props, Prop Drilling, PropTypes Validation, real-world examples, internal working, best practices, and interview questions.

---

# 1. What are Props?

Props stands for:

```text
Properties
```

---

Props are used to:

```text
Pass Data
From Parent Component
To Child Component
```

---

Think of Props as:

```text
Function Arguments
```

---

Example:

Function:

```javascript
function greet(name) {

 return "Hello " + name;
}
```

---

Call:

```javascript
greet("John");
```

---

Output:

```text
Hello John
```

---

Similarly:

```jsx
<User name="John" />
```

---

Here:

```text
name
```

is a Prop.

---

# 2. Why Do We Need Props?

Without Props:

```jsx
function User() {

 return <h1>John</h1>;
}
```

---

Problem:

```text
Always Shows John
```

---

Not reusable.

---

Using Props:

```jsx
function User(props) {

 return <h1>{props.name}</h1>;
}
```

---

Usage:

```jsx
<User name="John" />

<User name="David" />

<User name="Smith" />
```

---

Output:

```text
John

David

Smith
```

---

Same component.

Different data.

---

# 3. Data Flow in React

React follows:

```text
One-Way Data Binding
```

---

Flow:

```text
Parent
   ↓
Child
```

---

Example:

```text
App
 ↓

User
```

---

Props move:

```text
Top To Bottom
```

---

Also called:

```text
Unidirectional Data Flow
```

---

# 4. First Props Example

Parent:

```jsx
function App() {

 return (

  <User
   name="John"
  />

 );
}
```

---

Child:

```jsx
function User(props) {

 return (

  <h1>

   {props.name}

  </h1>

 );
}
```

---

Output:

```text
John
```

---

# 5. How React Stores Props Internally?

When React sees:

```jsx
<User name="John" />
```

---

React creates:

```javascript
{
 name: "John"
}
```

---

And passes it as:

```jsx
props
```

---

Inside component:

```jsx
function User(props)
```

---

Props becomes:

```javascript
{
 name: "John"
}
```

---

# Passing Props

---

# 6. Passing String Props

Parent:

```jsx
<User
 name="John"
/>
```

---

Child:

```jsx
function User(props) {

 return (

  <h1>

   {props.name}

  </h1>

 );
}
```

---

Output:

```text
John
```

---

# 7. Passing Number Props

Parent:

```jsx
<User
 age={25}
/>
```

---

Child:

```jsx
<h1>

 {props.age}

</h1>
```

---

Output:

```text
25
```

---

# 8. Passing Boolean Props

Parent:

```jsx
<User
 isAdmin={true}
/>
```

---

Child:

```jsx
{
 props.isAdmin
  ? "Admin"
  : "User"
}
```

---

Output:

```text
Admin
```

---

# 9. Boolean Shortcut

Instead of:

```jsx
<User
 isAdmin={true}
/>
```

---

Can write:

```jsx
<User
 isAdmin
/>
```

---

React interprets as:

```jsx
isAdmin={true}
```

---

# 10. Passing Arrays

Parent:

```jsx
<UserList

 users={[
  "John",
  "David",
  "Smith"
 ]}

/>
```

---

Child:

```jsx
function UserList({

 users

}) {

 return (

  <ul>

   {
    users.map(user =>

     <li key={user}>
      {user}
     </li>

    )
   }

  </ul>

 );
}
```

---

# 11. Passing Objects

Parent:

```jsx
<User

 user={{

  name: "John",

  age: 25

 }}

/>
```

---

Child:

```jsx
<h1>

 {props.user.name}

</h1>
```

---

Output:

```text
John
```

---

# 12. Passing Functions

Very common.

---

Parent:

```jsx
function App() {

 function greet() {

  alert("Hello");
 }

 return (

  <User
   greet={greet}
  />

 );
}
```

---

Child:

```jsx
<button

 onClick={props.greet}

>

 Click

</button>
```

---

This enables:

```text
Child To Trigger
Parent Logic
```

---

# 13. Passing Multiple Props

Parent:

```jsx
<User

 name="John"

 age={25}

 city="Bangalore"

/>
```

---

Child:

```jsx
function User(props) {

 return (

  <>

   <h1>{props.name}</h1>

   <p>{props.age}</p>

   <p>{props.city}</p>

  </>

 );
}
```

---

# 14. Props Are Read Only

Very important interview topic.

---

Wrong:

```jsx
props.name = "David";
```

---

Never do this.

---

Reason:

```text
Props Are Immutable
```

---

Meaning:

```text
Cannot Be Modified
```

---

# 15. Why Props Are Immutable?

React follows:

```text
Predictable Data Flow
```

---

Parent owns data.

---

Child only reads data.

---

Flow:

```text
Parent
  ↓

Child Reads

Not Changes
```

---

# Default Props

---

# 16. What are Default Props?

Used when parent does not pass a prop.

---

Example:

Parent:

```jsx
<User />
```

---

No name passed.

---

Need default value.

---

# 17. Default Parameter Approach

Modern React approach.

---

```jsx
function User({

 name = "Guest"

}) {

 return (

  <h1>{name}</h1>

 );
}
```

---

Usage:

```jsx
<User />
```

---

Output:

```text
Guest
```

---

# 18. Override Default Value

Usage:

```jsx
<User
 name="John"
/>
```

---

Output:

```text
John
```

---

Passed value overrides default.

---

# 19. Older DefaultProps Syntax

```jsx
User.defaultProps = {

 name: "Guest"
};
```

---

Still seen in older projects.

---

Modern approach:

```text
Default Parameters
```

---

Recommended.

---

# Props Destructuring

---

# 20. What is Props Destructuring?

Extracting values from props object.

---

Without destructuring:

```jsx
function User(props) {

 return (

  <h1>

   {props.name}

  </h1>

 );
}
```

---

Repeated:

```jsx
props.name

props.age

props.city
```

---

Verbose.

---

# 21. Destructuring Example

```jsx
function User({

 name,

 age

}) {

 return (

  <>

   <h1>{name}</h1>

   <p>{age}</p>

  </>

 );
}
```

---

Cleaner.

---

# 22. Alternative Destructuring

```jsx
function User(props) {

 const {

  name,

  age

 } = props;

 return (

  <>

   <h1>{name}</h1>

   <p>{age}</p>

  </>

 );
}
```

---

Also valid.

---

# 23. Nested Destructuring

Props:

```jsx
<User

 user={{

  name: "John",

  age: 25

 }}

/>
```

---

Child:

```jsx
function User({

 user: {

  name,

  age

 }

}) {

 return (

  <>
   {name}
   {age}
  </>

 );
}
```

---

Advanced usage.

---

# Children Props

---

# 24. What is Children Prop?

One of React's most powerful features.

---

Consider:

```jsx
<Card>

 Hello React

</Card>
```

---

Content between tags:

```text
Hello React
```

is automatically passed as:

```text
children
```

prop.

---

# 25. Internal Representation

React converts:

```jsx
<Card>

 Hello React

</Card>
```

---

Into:

```jsx
<Card

 children="Hello React"

/>
```

---

# 26. Children Example

Component:

```jsx
function Card({

 children

}) {

 return (

  <div>

   {children}

  </div>

 );
}
```

---

Usage:

```jsx
<Card>

 <h1>Hello</h1>

</Card>
```

---

Output:

```html
<div>
 <h1>Hello</h1>
</div>
```

---

# 27. Real World Example

Modal Component.

---

Component:

```jsx
function Modal({

 children

}) {

 return (

  <div>

   {children}

  </div>

 );
}
```

---

Usage:

```jsx
<Modal>

 <h1>Login</h1>

 <button>

  Submit

 </button>

</Modal>
```

---

Reusable.

---

# 28. Multiple Children

Example:

```jsx
<Card>

 <h1>Title</h1>

 <p>Description</p>

</Card>
```

---

React stores:

```text
Array Of Elements
```

---

As children.

---

# 29. Why Children is Powerful?

Enables:

```text
Component Composition
```

---

Used heavily in:

```text
Modal

Card

Layout

Sidebar

Dialog
```

---

# Prop Drilling

---

# 30. What is Prop Drilling?

One of the most asked React interview questions.

---

Problem:

Need to pass data through multiple levels.

---

Visualization:

```text
App
 ↓

Parent
 ↓

Child
 ↓

GrandChild
```

---

Need value in:

```text
GrandChild
```

---

# 31. Example

App:

```jsx
<Parent
 user="John"
/>
```

---

Parent:

```jsx
<Child
 user={user}
/>
```

---

Child:

```jsx
<GrandChild
 user={user}
/>
```

---

GrandChild:

```jsx
<h1>{user}</h1>
```

---

Data passed through:

```text
Multiple Components
```

---

Even if intermediate components don't need it.

---

This is:

```text
Prop Drilling
```

---

# 32. Why Prop Drilling is a Problem?

Imagine:

```text
10 Component Levels
```

---

Every level passes:

```jsx
user={user}
```

---

Code becomes:

```text
Messy

Hard To Maintain

Hard To Scale
```

---

# 33. Prop Drilling Visualization

```text
App
 ↓ user

Header
 ↓ user

Layout
 ↓ user

Dashboard
 ↓ user

Profile
```

---

Only:

```text
Profile
```

needs user.

---

Others are just forwarding.

---

# 34. Solution to Prop Drilling

Common solutions:

```text
Context API

Redux

Zustand

Recoil
```

---

Most common:

```text
Context API
```

---

# 35. When Prop Drilling is Okay?

Small hierarchy:

```text
Parent
 ↓

Child
```

---

Perfectly fine.

---

Avoid overengineering.

---

# PropTypes Validation

---

# 36. What is PropTypes?

PropTypes validates props at runtime.

---

Helps detect:

```text
Wrong Data Types
Missing Props
Invalid Values
```

---

# 37. Installation

```bash
npm install prop-types
```

---

# 38. Basic Example

```jsx
import PropTypes
from "prop-types";
```

---

Component:

```jsx
function User({

 name

}) {

 return <h1>{name}</h1>;
}
```

---

Validation:

```jsx
User.propTypes = {

 name:
  PropTypes.string
};
```

---

# 39. Required Props

```jsx
User.propTypes = {

 name:

 PropTypes.string
 .isRequired
};
```

---

Now React warns if:

```jsx
<User />
```

---

Without:

```text
name
```

---

# 40. Number Validation

```jsx
User.propTypes = {

 age:

 PropTypes.number
};
```

---

# 41. Boolean Validation

```jsx
User.propTypes = {

 isAdmin:

 PropTypes.bool
};
```

---

# 42. Array Validation

```jsx
User.propTypes = {

 users:

 PropTypes.array
};
```

---

# 43. Object Validation

```jsx
User.propTypes = {

 user:

 PropTypes.object
};
```

---

# 44. Function Validation

```jsx
User.propTypes = {

 onClick:

 PropTypes.func
};
```

---

# 45. Shape Validation

Most useful.

---

```jsx
User.propTypes = {

 user:

 PropTypes.shape({

  name:
   PropTypes.string,

  age:
   PropTypes.number

 })
};
```

---

Expected:

```javascript
{
 name: "John",
 age: 25
}
```

---

# 46. OneOf Validation

Restrict values.

---

```jsx
User.propTypes = {

 role:

 PropTypes.oneOf([

  "ADMIN",

  "USER",

  "MANAGER"

 ])
};
```

---

Only allowed:

```text
ADMIN

USER

MANAGER
```

---

# 47. PropTypes vs TypeScript

PropTypes:

```text
Runtime Validation
```

---

TypeScript:

```text
Compile-Time Validation
```

---

Modern projects often use:

```text
TypeScript
```

instead of PropTypes.

---

# Interview Questions

---

### Q1. What are Props?

```text
Properties used to pass data
from parent to child component.
```

---

### Q2. Are Props mutable?

```text
No

Props are immutable.
```

---

### Q3. What type of data can be passed as Props?

```text
Strings

Numbers

Booleans

Arrays

Objects

Functions

Components
```

---

### Q4. What is Props Destructuring?

```text
Extracting values
from props object.
```

---

### Q5. What is Children Prop?

```text
Content placed between
opening and closing tags.
```

---

### Q6. What is Prop Drilling?

```text
Passing props through
multiple component levels.
```

---

### Q7. How to solve Prop Drilling?

```text
Context API

Redux

Zustand

Recoil
```

---

### Q8. What are Default Props?

```text
Fallback values used
when props are not passed.
```

---

### Q9. What is PropTypes?

```text
Runtime prop validation library.
```

---

### Q10. What is isRequired?

```text
Makes prop mandatory.
```

---

### Q11. PropTypes vs TypeScript?

PropTypes:

```text
Runtime Validation
```

---

TypeScript:

```text
Compile-Time Validation
```

---

# Real World Example

```text
Amazon Product Card
```

---

Parent:

```jsx
<ProductCard

 title="iPhone 16"

 price={89999}

 inStock={true}

/>
```

---

Child:

```jsx
function ProductCard({

 title,

 price,

 inStock

}) {

 return (

  <div>

   <h2>{title}</h2>

   <p>{price}</p>

   <p>

    {
      inStock
       ? "Available"
       : "Out Of Stock"
    }

   </p>

  </div>

 );
}
```

---

Reusable:

```text
Same Component

Different Products
```

---

# Memory Tricks

### Props

```text
Parent → Child Data
```

---

### Destructuring

```text
Extract Props
```

---

### Children

```text
Content Between Tags
```

---

### Default Props

```text
Fallback Values
```

---

### Prop Drilling

```text
Passing Through Many Levels
```

---

### PropTypes

```text
Runtime Validation
```

---

# Quick Revision

```text
Props
-----

Pass Data
Parent → Child

Examples
--------

String

Number

Boolean

Array

Object

Function

Rules
-----

✔ Read Only

✔ Immutable

✔ One-Way Data Flow

Default Props
-------------

Used When Value Missing

Props Destructuring
-------------------

const { name } = props

Children Prop
-------------

<Card>

 Content

</Card>

↓

children

Prop Drilling
-------------

App
 ↓

Parent
 ↓

Child
 ↓

GrandChild

Problem:
Pass Data Through
Many Levels

Solution:
---------

Context API

Redux

Zustand

PropTypes
---------

Runtime Validation

Examples:

PropTypes.string

PropTypes.number

PropTypes.bool

PropTypes.func

PropTypes.shape()

PropTypes.oneOf()

Remember
--------

Props
 ↓
Pass Data

Children
 ↓
Composition

Prop Drilling
 ↓
Too Many Levels

PropTypes
 ↓
Validate Props
```