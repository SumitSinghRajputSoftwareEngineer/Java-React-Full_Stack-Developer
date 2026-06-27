# React Design Patterns

> **Goal:** Master React Design Patterns completely — understand Atomic Design, Component Composition, Separation of Concerns, Reusability Principles, how large-scale React applications are designed, real-world examples, enterprise architecture, best practices, interview questions, and common mistakes.

---

# 1. What Are Design Patterns?

A design pattern is:

```text
A Proven Solution
To A Recurring Problem
```

---

In React:

```text
How To Organize Components

How To Reuse Code

How To Build Scalable UI

How To Reduce Duplication
```

---

Example:

Without patterns:

```text
Messy Components

Code Duplication

Large Files

Difficult Maintenance
```

---

With patterns:

```text
Reusable Components

Scalable Architecture

Cleaner Code

Easy Maintenance
```

---

# Why Design Patterns Matter?

Suppose project grows from:

```text
5 Components
```

to

```text
500 Components
```

---

Without patterns:

```text
Project Becomes Chaos
```

---

Need:

```text
Structured Development
```

---

# Benefits

✔ Scalability

✔ Reusability

✔ Maintainability

✔ Better Team Collaboration

✔ Easier Testing

✔ Consistent UI

---

# Overview

```text
React Design Patterns
        ↓

Atomic Design

Component Composition

Separation Of Concerns

Reusability Principles
```

---

# Atomic Design

---

# 2. What is Atomic Design?

Atomic Design is a UI design methodology created by:

:contentReference[oaicite:0]{index=0}

---

Idea:

Build complex UI using small reusable pieces.

---

Just like chemistry:

```text
Atoms
 ↓

Molecules
 ↓

Organisms
 ↓

Templates
 ↓

Pages
```

---

UI is built similarly.

---

# Visualization

```text
Button
Input
Label
    ↓

Login Form
    ↓

Header
Sidebar
Login Section
    ↓

Dashboard Page
```

---

# Atomic Design Levels

```text
Atoms
 ↓

Molecules
 ↓

Organisms
 ↓

Templates
 ↓

Pages
```

---

Let's understand each.

---

# 3. Atoms

Atoms are:

```text
Smallest UI Components
```

---

Cannot be broken further.

---

Examples:

```text
Button

Input

Label

Checkbox

Icon
```

---

Example

Button Component

```jsx
function Button({

 children

}) {

 return (

  <button>

   {children}

  </button>

 );

}
```

---

Usage:

```jsx
<Button>

 Save

</Button>
```

---

Atom characteristics:

```text
Reusable

Independent

Simple
```

---

# Examples

```text
Button

Input

Text

Avatar

Badge
```

---

# Molecules

---

# 4. What is a Molecule?

Molecule:

```text
Combination Of Multiple Atoms
```

---

Example:

Login Form Field

---

Contains:

```text
Label

Input

Error Message
```

---

Visualization:

```text
Label
  +
Input
  +
Error Text
```

---

Example

```jsx
function FormField({

 label,

 error

}) {

 return (

  <div>

   <label>
    {label}
   </label>

   <input />

   {
    error &&
    <p>{error}</p>
   }

  </div>

 );

}
```

---

This is:

```text
Molecule
```

---

# More Examples

```text
Search Box

Login Field

Product Card Header

Profile Information
```

---

# Organisms

---

# 5. What is an Organism?

Organism:

```text
Combination Of Molecules
And Atoms
```

---

Represents:

```text
Major UI Sections
```

---

Example:

Header

Contains:

```text
Logo

Search Bar

Navigation

Profile Menu
```

---

Visualization

```text
Header
   ↓

Logo

Search Bar

Menu

Avatar
```

---

Example

```jsx
function Header() {

 return (

  <header>

   <Logo />

   <SearchBar />

   <Navbar />

   <ProfileMenu />

  </header>

 );

}
```

---

Organism examples:

```text
Header

Sidebar

Footer

Product Grid

Shopping Cart
```

---

# Templates

---

# 6. What is a Template?

Template defines:

```text
Page Layout
```

---

Contains:

```text
Header

Sidebar

Main Content

Footer
```

---

No actual data.

---

Visualization

```text
Header

Sidebar

Content

Footer
```

---

Example

```jsx
function DashboardTemplate() {

 return (

  <>

   <Header />

   <Sidebar />

   <MainContent />

   <Footer />

  </>

 );

}
```

---

Template focuses on:

```text
Layout
```

---

Not content.

---

# Pages

---

# 7. What is a Page?

Page is:

```text
Final Rendered Screen
```

---

Uses:

```text
Template

Real Data

API Responses
```

---

Example

```jsx
function DashboardPage() {

 const users =
 useUsers();

 return (

  <DashboardTemplate>

   <UserList
    users={users}
   />

  </DashboardTemplate>

 );

}
```

---

Visualization

```text
Template
    +
Real Data
    ↓

Page
```

---

# Atomic Design Folder Structure

---

```text
src

 ├── components

 │     ├── atoms

 │     │     ├── Button

 │     │     ├── Input

 │     │     └── Label

 │     ├── molecules

 │     │     ├── SearchBox

 │     │     └── FormField

 │     ├── organisms

 │     │     ├── Header

 │     │     └── Sidebar

 │     ├── templates

 │     │     └── DashboardTemplate

 │     └── pages

 │           └── DashboardPage
```

---

# Benefits of Atomic Design

✔ Highly Reusable

✔ Consistent UI

✔ Easy Maintenance

✔ Easy Scaling

✔ Team Friendly

---

# Component Composition

---

# 8. What is Component Composition?

Definition:

> Building larger components by combining smaller components.

---

React strongly encourages:

```text
Composition
```

---

Instead of:

```text
Inheritance
```

---

# Real World Example

Building:

```text
Dashboard
```

---

Using:

```text
Navbar

Sidebar

Cards

Charts
```

---

Visualization

```text
Dashboard
     ↓

Navbar

Sidebar

Cards

Charts
```

---

# Example

Small Components

```jsx
function Header() {

 return <h1>Header</h1>;

}
```

---

```jsx
function Footer() {

 return <h1>Footer</h1>;

}
```

---

Compose:

```jsx
function Page() {

 return (

  <>

   <Header />

   <Footer />

  </>

 );

}
```

---

This is:

```text
Component Composition
```

---

# Why Composition?

Without composition:

```text
Huge Components
```

---

Example:

```jsx
Dashboard.jsx

2000 Lines
```

---

Hard to maintain.

---

Composition creates:

```text
Small Reusable Components
```

---

# Children-Based Composition

Very common.

---

Example

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

Usage

```jsx
<Card>

 <h1>
  Product
 </h1>

</Card>
```

---

Flexible.

---

# Compound Composition Example

```jsx
<Modal>

 <Modal.Header />

 <Modal.Body />

 <Modal.Footer />

</Modal>
```

---

Large libraries use this pattern.

---

Examples:

```text
Material UI

Ant Design

Radix UI
```

---

# Separation of Concerns (SoC)

---

# 9. What is Separation of Concerns?

Definition:

> Each part of the application should have a single responsibility.

---

Avoid:

```text
One Component Doing Everything
```

---

# Bad Example

```jsx
function UserPage() {

 // API Call

 // Validation

 // Business Logic

 // Rendering

 // Styling

}
```

---

One component handling:

```text
Too Many Responsibilities
```

---

Hard to maintain.

---

# Good Approach

Separate concerns.

---

Visualization

```text
UI Component
      ↓

Custom Hook
      ↓

Service Layer
      ↓

API
```

---

# Example

API Logic

```javascript
// userService.js

export function
getUsers() {

 return axios.get(
  "/users"
 );

}
```

---

Custom Hook

```javascript
function useUsers() {

}
```

---

UI Component

```jsx
function UsersPage() {

}
```

---

Each file has:

```text
Single Responsibility
```

---

# Real Project Example

```text
UserPage
     ↓

useUsers Hook
     ↓

userService
     ↓

API
```

---

Clean architecture.

---

# Benefits

✔ Easier Maintenance

✔ Easier Testing

✔ Better Reusability

✔ Cleaner Code

✔ Better Scalability

---

# Reusability Principles

---

# 10. What is Reusability?

Definition:

> Write code once and use it many times.

---

Goal:

```text
Avoid Duplication
```

---

# Bad Example

Three buttons:

```jsx
<button>
 Save
</button>

<button>
 Update
</button>

<button>
 Delete
</button>
```

---

Repeated styles.

---

# Better

Reusable Button

```jsx
function Button({

 children

}) {

 return (

  <button>

   {children}

  </button>

 );

}
```

---

Usage

```jsx
<Button>
 Save
</Button>

<Button>
 Update
</Button>

<Button>
 Delete
</Button>
```

---

Reusable.

---

# Reusable Components

Examples:

```text
Button

Card

Modal

Table

Input

Loader
```

---

Should be:

```text
Generic

Flexible

Configurable
```

---

# Good Reusable Component

```jsx
function Button({

 text,

 variant

}) {

 return (

  <button>

   {text}

  </button>

 );

}
```

---

Usage

```jsx
<Button

 text="Save"

 variant="primary"

/>
```

---

Same component.

---

Multiple uses.

---

# Reusable Hooks

Example

```javascript
useFetch()

useDebounce()

useAuth()

useTheme()
```

---

Used throughout application.

---

# Reusable Services

```javascript
userService.js

productService.js

authService.js
```

---

Shared API logic.

---

# DRY Principle

Most important reusability concept.

---

DRY means:

```text
Don't Repeat Yourself
```

---

Bad:

```text
Same Logic
Copied Everywhere
```

---

Good:

```text
Reusable Function
Reusable Component
Reusable Hook
```

---

# Example

Bad

```javascript
fetch("/users")
```

in 20 places.

---

Good

```javascript
getUsers()
```

reused everywhere.

---

# Real World Architecture

```text
Reusable Components
         ↓

Reusable Hooks
         ↓

Reusable Services
         ↓

Reusable Utilities
```

---

# Atomic Design vs Component Composition

---

# Interview Question

Atomic Design:

```text
UI Organization Methodology
```

---

Component Composition:

```text
Building Components
Using Components
```

---

Example

Atomic Design:

```text
Atoms
 ↓
Molecules
 ↓
Organisms
```

---

Composition:

```text
Header
+
Sidebar
+
Footer
```

---

Build larger UI.

---

# Separation of Concerns vs Reusability

---

Separation Of Concerns:

```text
Split Responsibilities
```

---

Example

```text
UI

Hooks

Services
```

---

Reusability:

```text
Reuse Code
```

---

Example

```text
Button

Card

Modal
```

---

# Common Mistakes

---

# Giant Components

Bad

```jsx
Dashboard.jsx

3000 Lines
```

---

Split into smaller components.

---

# Duplicated Logic

Bad

```javascript
Same API Logic
In 20 Files
```

---

Create service.

---

# Duplicated UI

Bad

```jsx
10 Different Buttons
```

---

Create reusable button.

---

# Mixing Responsibilities

Bad

```jsx
Component
+
API Calls
+
Validation
+
State Logic
+
Rendering
```

---

Separate concerns.

---

# Best Practices

---

✔ Prefer Composition Over Inheritance

✔ Follow Atomic Design For Large UI Systems

✔ Create Reusable Components

✔ Extract Reusable Hooks

✔ Follow DRY Principle

✔ Separate UI And Business Logic

✔ Keep Components Small

✔ Use Feature-Based Structure

✔ Build Generic Components

✔ Think Scalability First

---

# Interview Questions

---

### Q1. What is Atomic Design?

```text
UI Design Methodology
Using

Atoms
Molecules
Organisms
Templates
Pages
```

---

### Q2. What is Component Composition?

```text
Building Components
By Combining Components
```

---

### Q3. Why does React prefer Composition over Inheritance?

```text
More Flexible

More Reusable

Easier To Maintain
```

---

### Q4. What is Separation of Concerns?

```text
Each Module
Has One Responsibility
```

---

### Q5. What is DRY Principle?

```text
Don't Repeat Yourself
```

---

### Q6. What belongs in a reusable component?

```text
Generic UI Logic
```

---

### Q7. What is an Atom?

```text
Smallest UI Component
```

---

### Q8. What is an Organism?

```text
Collection Of Molecules
And Atoms
```

---

### Q9. Why is reusability important?

```text
Less Duplication

Better Maintainability
```

---

### Q10. Give examples of reusable React code.

```text
Components

Hooks

Services

Utilities
```

---

# Memory Tricks

### Atomic Design

```text
Build UI Like Chemistry

Atoms
 ↓
Molecules
 ↓
Organisms
 ↓
Templates
 ↓
Pages
```

---

### Component Composition

```text
Combine Small Components
To Build Large Components
```

---

### Separation Of Concerns

```text
One Responsibility
Per Module
```

---

### Reusability

```text
Write Once
Use Many Times
```

---

### DRY

```text
Don't Repeat Yourself
```

---

# Complete Design Flow

```text
Atoms
   ↓

Molecules
   ↓

Organisms
   ↓

Templates
   ↓

Pages
   ↓

Composition
   ↓

Reusable UI
   ↓

Separated Concerns
   ↓

Scalable Application
```

---

# Quick Revision

```text
React Design Patterns
---------------------

Atomic Design
 ↓
 UI Methodology

Atoms
 ↓
 Button

Molecules
 ↓
 Form Field

Organisms
 ↓
 Header

Templates
 ↓
 Layout

Pages
 ↓
 Final Screen

Component Composition
 ↓
 Combine Components

Separation Of Concerns
 ↓
 One Responsibility

Reusability
 ↓
 Write Once Use Many Times

DRY
 ↓
 Don't Repeat Yourself

Benefits:

✔ Scalability

✔ Reusability

✔ Maintainability

✔ Cleaner Architecture

✔ Better Team Collaboration

Remember:

Atomic Design
 ↓
 Organize UI

Composition
 ↓
 Build UI

Separation OfConcerns
 ↓
 Split Responsibilities

Reusability
 ↓
 Avoid Duplication
```

---
