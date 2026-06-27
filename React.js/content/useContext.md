# useContext Hook

> **Goal:** Understand useContext from scratch, why React created it, Context API, Provider, Consumer, Global State Sharing, Prop Drilling Problem, Context Flow, Re-rendering Behavior, Real-World Examples, Internal Working, Performance Considerations, Best Practices, Common Mistakes, Interview Questions, and React Internals.

---

# 1. What Problem Does useContext Solve?

Imagine an application:

```text
App
 ↓
Dashboard
 ↓
Profile
 ↓
UserInfo
```

---

User data exists in:

```text
App Component
```

---

But needed inside:

```text
UserInfo Component
```

---

Without Context:

```jsx
<App user={user} />
```

↓

```jsx
<Dashboard user={user} />
```

↓

```jsx
<Profile user={user} />
```

↓

```jsx
<UserInfo user={user} />
```

---

Even though:

```text
Dashboard
```

and

```text
Profile
```

don't use:

```text
user
```

---

They still receive it.

---

This is called:

```text
Prop Drilling
```

---

Problem:

```text
Too Much Prop Passing

Hard To Maintain

Messy Code

Poor Scalability
```

---

Need:

```text
Direct Access
To Shared Data
```

---

React introduced:

```text
Context API

+

useContext Hook
```

---

# 2. What is Prop Drilling?

Definition:

> Passing props through multiple intermediate components just to reach a deeply nested component.

---

Visualization:

```text
App
 ↓ user

Dashboard
 ↓ user

Profile
 ↓ user

UserInfo
```

---

Only:

```text
UserInfo
```

needs data.

---

Others simply forward it.

---

Bad design.

---

# 3. Real World Example

Suppose application stores:

```text
Logged-In User

Theme

Language

Currency

Authentication Status
```

---

Almost every component needs them.

---

Passing props everywhere becomes:

```text
Nightmare
```

---

Need:

```text
Central Sharing
```

---

# 4. What is Context?

Definition:

> Context allows data to be shared across components without passing props manually through every level.

---

Simple Meaning:

```text
Global Data Storage
For Component Tree
```

---

# 5. What is useContext?

Definition:

> useContext is a React Hook used to read values from a Context.

---

Simple Meaning:

```text
Access Shared Data
From Anywhere
```

---

# 6. Context API Components

React Context consists of:

```text
1. Create Context

2. Provider

3. Consumer

4. useContext
```

---

Modern React mainly uses:

```text
Create Context

Provider

useContext
```

---

Consumer is rarely used.

---

# 7. Context Flow

```text
Provider
    ↓

Component
    ↓

Component
    ↓

Component

useContext()
```

---

Any component inside provider can access data.

---

# 8. Creating Context

Step 1:

```jsx
import {

 createContext

}
from "react";
```

---

Create context:

```jsx
const UserContext =
createContext();
```

---

Now React creates:

```text
Context Object
```

---

# 9. What Does createContext() Return?

Conceptually:

```jsx
{
 Provider,

 Consumer
}
```

---

Contains:

```text
Provider Component

Consumer Component

Internal Metadata
```

---

# 10. Provider

Provider supplies data.

---

Example:

```jsx
<UserContext.Provider>

</UserContext.Provider>
```

---

Think of Provider as:

```text
Data Source
```

---

# 11. Providing Data

```jsx
<UserContext.Provider

 value="John"

>

 <App />

</UserContext.Provider>
```

---

Now:

```text
John
```

available to all child components.

---

# 12. Visualization

```text
Provider

value = John
      ↓

Dashboard
      ↓

Profile
      ↓

UserInfo
```

---

Every component can access:

```text
John
```

---

# 13. Using useContext

Import:

```jsx
import {

 useContext

}
from "react";
```

---

Use:

```jsx
const user =

 useContext(

  UserContext

 );
```

---

Now:

```text
user = John
```

---

# 14. Complete Example

## Create Context

```jsx
import {

 createContext

}
from "react";

export const UserContext =

 createContext();
```

---

## Provider

```jsx
<UserContext.Provider

 value="John"

>

 <Dashboard />

</UserContext.Provider>
```

---

## Consumer Component

```jsx
import {

 useContext

}
from "react";

import {

 UserContext

}
from "./UserContext";
```

---

```jsx
function UserInfo() {

 const user =

 useContext(

  UserContext

 );

 return <h1>{user}</h1>;
}
```

---

Output:

```text
John
```

---

# 15. Internal Working

When React sees:

```jsx
<UserContext.Provider

 value="John"

>
```

---

React stores:

```text
Current Context Value
```

---

Inside:

```text
Context Object
```

---

When:

```jsx
useContext(UserContext)
```

executes,

React finds nearest:

```text
Provider
```

---

Returns:

```text
John
```

---

# 16. Nearest Provider Rule

Important Interview Topic.

---

Example:

```jsx
<UserContext.Provider

 value="John"

>

 <ComponentA>

  <UserContext.Provider

   value="David"

  >

   <ComponentB />

  </UserContext.Provider>

 </ComponentA>

</UserContext.Provider>
```

---

ComponentB gets:

```text
David
```

---

Because nearest provider wins.

---

# 17. Why?

React searches upward:

```text
Component
   ↑

Provider
```

---

First provider found:

```text
Used
```

---

# Context with Objects

---

# 18. Storing Complex Data

Example:

```jsx
<UserContext.Provider

 value={{

  name: "John",

  age: 25

 }}

>
```

---

Access:

```jsx
const user =

 useContext(

  UserContext

 );
```

---

```jsx
user.name
```

---

Output:

```text
John
```

---

# 19. Real World User Context

```jsx
value={{

 id: 1,

 username: "john",

 email: "john@gmail.com",

 role: "ADMIN"

}}
```

---

Available globally.

---

# Context + State

---

# 20. Dynamic Context

Most common usage.

---

Example:

```jsx
const [

 user,

 setUser

]
=
useState(null);
```

---

Provider:

```jsx
<UserContext.Provider

 value={user}

>
```

---

Now context updates automatically.

---

# 21. Login Example

Initially:

```text
null
```

---

After login:

```jsx
setUser({
 name: "John"
});
```

---

Every consumer gets:

```text
Updated User
```

---

Automatically.

---

# Theme Example

---

# 22. Dark Mode Example

Context:

```jsx
const ThemeContext =
createContext();
```

---

Provider:

```jsx
<ThemeContext.Provider

 value="dark"

>
```

---

Consumer:

```jsx
const theme =

 useContext(

  ThemeContext

 );
```

---

Output:

```text
dark
```

---

# 23. Dynamic Theme

```jsx
const [

 theme,

 setTheme

]
=
useState("light");
```

---

Provider:

```jsx
<ThemeContext.Provider

 value={theme}

>
```

---

Toggle:

```jsx
setTheme("dark");
```

---

All components update.

---

# Authentication Example

---

# 24. Most Common Real World Context

Authentication.

---

Store:

```text
Current User

JWT Token

Roles

Permissions
```

---

Globally.

---

# 25. Example

```jsx
<AuthContext.Provider

 value={{

  user,

  login,

  logout

 }}

>
```

---

Any component:

```jsx
const auth =

 useContext(
  AuthContext
 );
```

---

Can access:

```text
Login

Logout

User
```

---

# Context vs Props

---

# 26. Props

Flow:

```text
Parent
 ↓

Child
 ↓

Grandchild
```

---

Data flows manually.

---

# 27. Context

Flow:

```text
Provider
    ↓

Any Descendant
```

---

Direct access.

---

# 28. Comparison

| Feature | Props | Context |
|----------|----------|----------|
| Data Sharing | Parent → Child | Global Tree |
| Prop Drilling | Yes | No |
| Easy For Deep Components | No | Yes |
| Reusability | Higher | Lower |

---

# Context Re-rendering

---

# 29. Important Interview Topic

Provider value changes:

```jsx
setUser(...)
```

---

All consumers:

```text
Re-render
```

---

Automatically.

---

# 30. Example

```jsx
<UserContext.Provider

 value={user}

>
```

---

User changes.

---

Every:

```jsx
useContext(UserContext)
```

component updates.

---

# 31. Performance Problem

Large Context:

```text
100 Components
```

---

One value changes.

---

All consumers re-render.

---

Potential performance issue.

---

# 32. Solution

Split contexts.

---

Instead of:

```text
One Giant Context
```

---

Use:

```text
User Context

Theme Context

Language Context
```

---

Separately.

---

# Context vs Redux

---

# 33. Common Interview Question

Many developers confuse them.

---

## Context

Purpose:

```text
Share Data
```

---

Examples:

```text
Theme

User

Language
```

---

## Redux

Purpose:

```text
Complex Global State
```

---

Examples:

```text
Cart

Orders

Products

Caching

Large Applications
```

---

# 34. Memory Trick

```text
Context
 ↓

Avoid Prop Drilling

Redux
 ↓

Advanced State Management
```

---

# Context vs useState

---

# 35. useState

Stores state inside:

```text
Single Component
```

---

# 36. Context

Shares state across:

```text
Multiple Components
```

---

# 37. Together

Most common pattern:

```jsx
useState()
```

stores data.

---

```jsx
Context
```

shares data.

---

Example:

```jsx
const [

 user,

 setUser

]
=
useState(null);
```

---

Provider:

```jsx
value={{

 user,

 setUser

}}
```

---

Consumers:

```jsx
useContext(...)
```

---

# Common Mistakes

---

# 38. Using Context for Everything

Bad.

---

Context is not replacement for:

```text
Local State
```

---

Keep local data:

```jsx
useState()
```

---

# 39. Forgetting Provider

Example:

```jsx
useContext(UserContext)
```

---

Without Provider.

---

Returns:

```text
Default Value
```

---

Or:

```text
Undefined
```

---

# 40. Giant Context Object

Bad:

```text
User

Theme

Language

Products

Orders

Cart

Everything
```

---

Creates:

```text
Unnecessary Re-renders
```

---

# 41. Creating Context Inside Component

Wrong:

```jsx
function App() {

 const UserContext =

 createContext();

}
```

---

Context recreated every render.

---

Bad.

---

# Best Practices

---

# 42. Create Separate Context Files

Example:

```text
UserContext.js

ThemeContext.js

AuthContext.js
```

---

# 43. Keep Context Focused

Good:

```text
One Responsibility
```

---

Per context.

---

# 44. Combine Context + Custom Hook

Example:

```jsx
export function useAuth() {

 return useContext(
  AuthContext
 );
}
```

---

Cleaner code.

---

# 45. Store Only Shared Data

Examples:

```text
Theme

User

Language

Authentication
```

---

Good candidates.

---

# React Fiber Internals

---

# 46. How React Stores Context

React Fiber maintains:

```text
Context Object
```

---

Contains:

```text
Current Value

Provider Reference

Consumer References
```

---

# 47. During Render

Consumer executes:

```jsx
useContext(
 UserContext
);
```

---

React:

```text
Find Provider
      ↓

Read Value
      ↓

Subscribe Consumer
```

---

# 48. Provider Update

When value changes:

```jsx
setUser(...)
```

---

React identifies:

```text
Subscribed Consumers
```

---

Triggers:

```text
Re-render
```

---

# Real World Example

---

# 49. E-Commerce Application

Global Context:

```text
Logged User

Theme

Language

Currency
```

---

Available everywhere.

---

Components:

```text
Navbar

Profile

Orders

Checkout

Settings
```

---

Can access data directly.

---

Without:

```text
Prop Drilling
```

---

# Interview Questions

---

### Q1. What problem does useContext solve?

```text
Prop Drilling
```

---

### Q2. What is Context?

```text
Shared Data Mechanism
For Component Tree
```

---

### Q3. What is useContext?

```text
Hook Used To Read
Context Values
```

---

### Q4. What are Context Components?

```text
createContext()

Provider

Consumer

useContext()
```

---

### Q5. What is Prop Drilling?

```text
Passing Props Through
Multiple Components
```

---

### Q6. Which Provider Is Used?

```text
Nearest Provider
```

---

### Q7. Does Context Replace Redux?

```text
No
```

---

Different purposes.

---

### Q8. Can Context Trigger Re-renders?

```text
Yes
```

---

Consumers re-render.

---

### Q9. Can Context Store Objects?

```text
Yes
```

---

Objects

Arrays

Functions

Anything
```

---

### Q10. Most Common Context Use Cases?

```text
Authentication

Theme

Language

User Data
```

---

# Memory Tricks

### Context

```text
Shared Data
```

---

### Provider

```text
Supplies Data
```

---

### useContext

```text
Reads Data
```

---

### Prop Drilling

```text
Too Much Prop Passing
```

---

### Nearest Provider

```text
Closest Provider Wins
```

---

### Context + useState

```text
Store + Share
```

---

### Context

```text
Global Access

Within Component Tree
```

---

# Quick Revision

```text
useContext
----------

Purpose:

Share Data Across Components

Solves:

Prop Drilling

Core Parts:

1. createContext()

2. Provider

3. useContext()

Flow:

Provider
    ↓

Components
    ↓

useContext()

Common Use Cases:

✔ Authentication

✔ Theme

✔ Language

✔ User Data

Advantages:

✔ No Prop Drilling

✔ Cleaner Components

✔ Easier Data Sharing

Disadvantages:

✘ Re-rendering

✘ Not For Complex State

Remember:

useState
 ↓

Store Data

useContext
 ↓

Share Data

Provider
 ↓

Give Data

Consumer
 ↓

Read Data
```

---