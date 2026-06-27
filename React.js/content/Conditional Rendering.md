# Conditional Rendering

> **Goal:** Understand Conditional Rendering from scratch, why it is needed, different techniques like if/else, Ternary Operator, Logical && Operator, Switch Statements, Dynamic UI Rendering, internal working, real-world examples, best practices, and interview questions.

---

# 1. What is Conditional Rendering?

Conditional Rendering means:

```text
Showing Different UI
Based On Conditions
```

---

Just like Java uses:

```java
if

else

switch
```

---

React uses the same concepts to decide:

```text
What To Render
```

---

# 2. Why Do We Need Conditional Rendering?

Applications rarely show:

```text
Same UI Always
```

---

Examples:

```text
Logged In User

Admin User

Loading State

Error State

Empty Data State
```

---

Need:

```text
Different UI
For Different Situations
```

---

This is:

```text
Conditional Rendering
```

---

# 3. Real World Examples

### Login

```text
Logged In
    ↓

Show Dashboard
```

---

```text
Not Logged In
      ↓

Show Login Page
```

---

### E-Commerce

```text
In Stock
    ↓

Show Buy Button
```

---

```text
Out Of Stock
      ↓

Show Notify Me
```

---

# 4. Conditional Rendering Flow

```text
Condition
    ↓

true ?
    ↓

UI A

false ?
    ↓

UI B
```

---

# 5. Example Visualization

```text
isLoggedIn = true

      ↓

Welcome User
```

---

```text
isLoggedIn = false

      ↓

Please Login
```

---

# if/else Rendering

---

# 6. What is if/else Rendering?

Uses traditional:

```javascript
if

else
```

---

To decide which JSX should render.

---

# 7. Basic Example

```jsx
function App() {

 const isLoggedIn = true;

 if(isLoggedIn) {

  return <h1>Welcome</h1>;
 }

 return <h1>Please Login</h1>;
}
```

---

Output:

```text
Welcome
```

---

# 8. Using if/else

```jsx
function App() {

 const isAdmin = false;

 if(isAdmin) {

  return <h1>Admin Panel</h1>;
 }
 else {

  return <h1>User Dashboard</h1>;
 }
}
```

---

Output:

```text
User Dashboard
```

---

# 9. Multiple Conditions

```jsx
if(score >= 90) {

 return <h1>A Grade</h1>;
}

if(score >= 75) {

 return <h1>B Grade</h1>;
}

return <h1>C Grade</h1>;
```

---

Very common.

---

# 10. When to Use if/else?

Use when:

```text
Large Logic

Multiple Conditions

Complex Rendering
```

---

Avoid using huge nested ternaries.

---

# Ternary Operator

---

# 11. What is Ternary Operator?

Short form of:

```javascript
if/else
```

---

Syntax:

```javascript
condition

 ? value1

 : value2
```

---

# 12. Java Example

```java
String result =

 age >= 18

 ? "Adult"

 : "Minor";
```

---

Same concept in React.

---

# 13. Basic React Example

```jsx
function App() {

 const isLoggedIn = true;

 return (

  <h1>

   {

    isLoggedIn

    ? "Welcome"

    : "Login"

   }

  </h1>

 );
}
```

---

Output:

```text
Welcome
```

---

# 14. How Ternary Works

Condition:

```jsx
isLoggedIn
```

---

If:

```text
true
```

---

Render:

```text
Welcome
```

---

Otherwise:

```text
Login
```

---

# 15. Rendering Components

```jsx
{

 isAdmin

 ? <AdminPanel />

 : <UserPanel />

}
```

---

Very common.

---

# 16. Multiple Ternary Example

```jsx
{

 score >= 90

 ? "A"

 : score >= 75

 ? "B"

 : "C"

}
```

---

Works.

---

But difficult to read.

---

# 17. Avoid Deep Nesting

Bad:

```jsx
condition1

 ? condition2

   ? value1

   : value2

 : value3
```

---

Hard to maintain.

---

Use:

```text
if/else
```

instead.

---

# Logical && Operator

---

# 18. What is && Rendering?

Used when:

```text
Render Something

Only If Condition Is True
```

---

Syntax:

```jsx
condition && JSX
```

---

# 19. Example

```jsx
{

 isLoggedIn

 &&

 <h1>Welcome</h1>

}
```

---

If:

```text
true
```

---

Output:

```text
Welcome
```

---

If:

```text
false
```

---

Output:

```text
Nothing
```

---

# 20. Internal Working

JavaScript:

```javascript
true && "Hello"
```

---

Returns:

```text
Hello
```

---

JavaScript:

```javascript
false && "Hello"
```

---

Returns:

```text
false
```

---

React ignores:

```text
false
```

---

Nothing rendered.

---

# 21. Notification Example

```jsx
{

 notifications > 0

 &&

 <p>

  New Notifications

 </p>

}
```

---

Only shows when notifications exist.

---

# 22. Button Example

```jsx
{

 isAdmin

 &&

 <button>

  Delete User

 </button>

}
```

---

Admin sees button.

---

Others don't.

---

# 23. Common Interview Trap

Example:

```jsx
{

 0 &&

 <h1>Hello</h1>

}
```

---

Output:

```text
0
```

---

Because:

```javascript
0 && value
```

returns:

```text
0
```

---

Safer:

```jsx
{

 count > 0

 &&

 <h1>Hello</h1>

}
```

---

# Switch Statements

---

# 24. Why Use Switch?

Useful when:

```text
Many Conditions
Depend On One Value
```

---

Example:

```text
User Role
```

---

Possible values:

```text
ADMIN

MANAGER

USER
```

---

# 25. Basic JavaScript Example

```javascript
switch(role) {

 case "ADMIN":

  return "Admin";

 case "USER":

  return "User";

 default:

  return "Guest";
}
```

---

# 26. React Example

```jsx
function Dashboard({

 role

}) {

 switch(role) {

  case "ADMIN":

   return <AdminPanel />;

  case "USER":

   return <UserPanel />;

  default:

   return <GuestPanel />;
 }
}
```

---

# 27. Output

Role:

```text
ADMIN
```

---

Output:

```jsx
<AdminPanel />
```

---

# 28. Role Based UI Example

```jsx
switch(role) {

 case "ADMIN":

  return <AdminMenu />;

 case "MANAGER":

  return <ManagerMenu />;

 case "EMPLOYEE":

  return <EmployeeMenu />;

 default:

  return <Login />;
}
```

---

Very common in enterprise applications.

---

# 29. Switch vs Ternary

Ternary:

```text
Few Conditions
```

---

Switch:

```text
Many Conditions
```

---

Cleaner.

---

# Dynamic UI Rendering

---

# 30. What is Dynamic UI Rendering?

UI changes dynamically based on:

```text
Data

State

Props

API Response
```

---

One of React's biggest strengths.

---

# 31. Example

State:

```jsx
const [

 isDark,

 setIsDark

]
=
useState(false);
```

---

UI:

```jsx
<h1>

 {

  isDark

  ? "Dark Mode"

  : "Light Mode"

 }

</h1>
```

---

Updates instantly.

---

# 32. API Data Example

Response:

```javascript
[
 {
  id: 1,
  name: "John"
 },

 {
  id: 2,
  name: "David"
 }
]
```

---

Render:

```jsx
{

 users.map(user => (

  <p

   key={user.id}

  >

   {user.name}

  </p>

 ))

}
```

---

UI generated dynamically.

---

# 33. Loading State Example

```jsx
if(loading) {

 return <h1>Loading...</h1>;
}
```

---

After loading:

```jsx
return <Users />;
```

---

Very common interview example.

---

# 34. Error State Example

```jsx
if(error) {

 return (

  <h1>

   Something Went Wrong

  </h1>

 );
}
```

---

Otherwise:

```jsx
return <Products />;
```

---

# 35. Authentication Example

```jsx
{

 user

 ? <Dashboard />

 : <Login />

}
```

---

Most common real-world usage.

---

# 36. Empty State Example

```jsx
{

 users.length === 0

 ? (

   <h1>

    No Users Found

   </h1>

 )

 : (

   <UsersList />

 )

}
```

---

Professional applications always handle:

```text
Empty State
```

---

# 37. Dynamic Styling Example

```jsx
<h1

 className={

  isDark

   ? "dark"

   : "light"

 }

>

 Theme

</h1>
```

---

Conditional CSS.

---

# 38. Dynamic Button Example

```jsx
<button>

 {

  loading

  ? "Saving..."

  : "Save"

 }

</button>
```

---

Excellent UX.

---

# 39. Dynamic Icon Example

```jsx
{

 isFavorite

 ? "❤️"

 : "🤍"

}
```

---

Used everywhere.

---

# 40. Dynamic Component Rendering

```jsx
const Component =

 isAdmin

 ? AdminDashboard

 : UserDashboard;
```

---

Render:

```jsx
<Component />
```

---

Advanced pattern.

---

# Best Practices

---

# 41. Use Ternary for Simple Conditions

Good:

```jsx
{

 isLoggedIn

 ? "Welcome"

 : "Login"

}
```

---

# 42. Use if/else for Complex Logic

Good:

```jsx
if(user === null) {

 return <Login />;
}
```

---

Cleaner.

---

# 43. Use && for Optional UI

Good:

```jsx
{

 error

 &&

 <ErrorMessage />

}
```

---

Simple and readable.

---

# 44. Avoid Nested Ternaries

Bad:

```jsx
a

 ? b

   ? c

   : d

 : e
```

---

Hard to read.

---

# 45. Extract Complex Logic

Instead of:

```jsx
Huge JSX Conditions
```

---

Use:

```jsx
function renderContent() {

 if(loading) {

  return <Loader />;
 }

 return <Data />;
}
```

---

Cleaner code.

---

# Interview Questions

---

### Q1. What is Conditional Rendering?

```text
Displaying different UI
based on conditions.
```

---

### Q2. What are common ways of Conditional Rendering?

```text
if/else

Ternary

&& Operator

Switch Statement
```

---

### Q3. When should you use Ternary?

```text
Simple if/else conditions.
```

---

### Q4. When should you use &&?

```text
Render UI only when
condition is true.
```

---

### Q5. What happens here?

```jsx
{

 false &&

 <h1>Hello</h1>

}
```

---

Output:

```text
Nothing
```

---

### Q6. What happens here?

```jsx
{

 0 &&

 <h1>Hello</h1>

}
```

---

Output:

```text
0
```

---

Important interview question.

---

### Q7. When should you use Switch?

```text
Multiple cases based
on a single value.
```

---

### Q8. What is Dynamic UI Rendering?

```text
UI changes based on
State, Props, or Data.
```

---

### Q9. Which is better: Ternary or if/else?

```text
Depends on complexity.

Simple → Ternary

Complex → if/else
```

---

### Q10. Give a real-world example of Conditional Rendering.

```text
Login Page

Role-Based UI

Loading Screen

Error Handling

Shopping Cart
```

---

# Real World Example

### Login Application

```jsx
function App() {

 const [

  isLoggedIn,

  setIsLoggedIn

 ]
 =
 useState(false);

 return (

  <div>

   {

    isLoggedIn

    ? (

      <Dashboard />

     )

    : (

      <Login />

     )

   }

  </div>

 );
}
```

---

Behavior:

```text
false
 ↓

Login Screen
```

---

```text
true
 ↓

Dashboard
```

---

# Memory Tricks

### Conditional Rendering

```text
Show UI Based On Condition
```

---

### if/else

```text
Complex Logic
```

---

### Ternary

```text
Short if/else
```

---

### &&

```text
Show Only If True
```

---

### Switch

```text
Multiple Cases
```

---

### Dynamic UI

```text
State Changes UI
```

---

# Quick Revision

```text
Conditional Rendering
---------------------

Purpose
-------

Show Different UI
Based On Conditions

Methods
--------

1. if/else

2. Ternary

3. && Operator

4. Switch

if/else
--------

Best For:

Complex Logic

Ternary
--------

condition

 ? UI1

 : UI2

&& Operator
-----------

condition

 && UI

Used For:

Optional Rendering

Switch
------

Best For:

Multiple Cases

Dynamic UI
----------

Changes Based On:

State

Props

API Data

Examples
--------

Login Screen

Admin Dashboard

Loading Spinner

Error Message

Empty State

Shopping Cart

Remember
--------

if/else
 ↓
Complex Logic

Ternary
 ↓
Short if/else

&&
 ↓
Render If True

Switch
 ↓
Multiple Cases

Dynamic UI
 ↓
React's Superpower
```