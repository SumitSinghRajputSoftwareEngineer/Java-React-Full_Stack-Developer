# React Project Structure

> **Goal:** Master React Project Structure completely — understand Feature-Based Structure, Component-Based Structure, Folder Organization, Environment Variables, Configuration Management, scalable architecture, enterprise-level folder layouts, best practices, real-world examples, and interview questions.

---

# 1. Why Project Structure Matters?

Many beginners create React projects like:

```text
src
 ├── App.jsx
 ├── Login.jsx
 ├── User.jsx
 ├── Product.jsx
 ├── Dashboard.jsx
 ├── Cart.jsx
 ├── Orders.jsx
 ├── Navbar.jsx
 ├── Sidebar.jsx
 ├── API.js
 ├── Utils.js
```

---

Initially:

```text
Looks Fine
```

---

After 6 months:

```text
100+ Components

50+ APIs

20+ Pages

10+ Developers
```

---

Result:

```text
Messy Project
```

---

Problems:

```text
Hard To Find Files

Code Duplication

Difficult Maintenance

Merge Conflicts

Poor Scalability
```

---

Need:

```text
Well Organized Structure
```

---

# Benefits of Good Project Structure

✔ Easier Navigation

✔ Better Scalability

✔ Team Collaboration

✔ Easier Maintenance

✔ Faster Development

✔ Reduced Bugs

✔ Cleaner Codebase

---

# React Project Architecture

```text
React Application
        ↓

Pages

Components

Hooks

Services

State

Utils

Assets

Configuration
```

---

Everything should have a dedicated place.

---

# Feature-Based Structure

---

# 2. What is Feature-Based Structure?

Definition:

> Organize files by business feature rather than by file type.

---

Most popular architecture in enterprise React applications.

---

Example Features:

```text
Authentication

Products

Orders

Users

Cart

Dashboard
```

---

Each feature owns:

```text
Components

Hooks

Services

Styles

Tests
```

---

# Folder Structure

```text
src

 ├── features

 │     ├── auth

 │     │      ├── components
 │     │      ├── hooks
 │     │      ├── services
 │     │      ├── pages
 │     │      └── types

 │     ├── products

 │     │      ├── components
 │     │      ├── hooks
 │     │      ├── services
 │     │      └── pages

 │     └── orders
```

---

# Visualization

```text
Feature
     ↓

Components

Hooks

Services

Pages

Tests
```

---

Everything related stays together.

---

# Example

Authentication Feature

```text
auth

 ├── Login.jsx

 ├── Register.jsx

 ├── authApi.js

 ├── useAuth.js

 ├── authSlice.js
```

---

All auth code in one place.

---

# Benefits

✔ Highly Scalable

✔ Team Friendly

✔ Easier Maintenance

✔ Easier Refactoring

---

# Real Enterprise Example

```text
src

 ├── features

 │      ├── auth

 │      ├── users

 │      ├── orders

 │      ├── inventory

 │      ├── reports

 │      └── dashboard
```

---

Used in:

```text
Large Enterprise Applications
```

---

# Component-Based Structure

---

# 3. What is Component-Based Structure?

Definition:

> Organize files by component type.

---

Example:

```text
src

 ├── components

 ├── pages

 ├── hooks

 ├── services

 ├── styles

 ├── utils
```

---

# Example Structure

```text
src

 ├── components

 │     ├── Button.jsx

 │     ├── Navbar.jsx

 │     ├── Sidebar.jsx

 │     └── Modal.jsx

 ├── pages

 │     ├── Home.jsx

 │     ├── Login.jsx

 │     └── Dashboard.jsx
```

---

All components together.

---

# Visualization

```text
Components
     ↓

Button

Navbar

Modal

Card
```

---

# Advantages

✔ Easy For Small Projects

✔ Simple Structure

✔ Easy To Understand

---

# Disadvantages

As project grows:

```text
Huge Components Folder
```

---

Example:

```text
components

 ├── Button.jsx

 ├── ProductCard.jsx

 ├── UserCard.jsx

 ├── LoginForm.jsx

 ├── RegisterForm.jsx

 ├── 100 More Files
```

---

Hard to manage.

---

# Feature-Based vs Component-Based

---

# Interview Question

| Feature-Based | Component-Based |
|---------------|----------------|
| Organize by Feature | Organize by Type |
| Scalable | Less Scalable |
| Enterprise Preferred | Small Projects |
| Easier Team Collaboration | Simpler Setup |
| Better Maintenance | Can Become Messy |

---

# Example

Feature-Based:

```text
Auth
 ├── Login
 ├── Register
 ├── API
```

---

Component-Based:

```text
components
 ├── Login
 ├── Register

services
 ├── AuthAPI
```

---

Files scattered.

---

# Folder Organization

---

# 4. Recommended React Folder Structure

Modern React Project

```text
src

 ├── assets

 ├── components

 ├── features

 ├── pages

 ├── hooks

 ├── services

 ├── store

 ├── utils

 ├── routes

 ├── contexts

 ├── styles

 ├── types

 ├── constants

 ├── App.jsx

 └── main.jsx
```

---

Let's understand each.

---

# assets

Stores:

```text
Images

Icons

Fonts

Videos
```

---

Example:

```text
assets

 ├── images

 ├── icons

 └── fonts
```

---

# components

Reusable UI.

---

Example:

```text
Button

Card

Modal

Loader
```

---

```text
components

 ├── Button

 ├── Card

 ├── Modal
```

---

# pages

Page-level components.

---

Example:

```text
Home

Login

Dashboard
```

---

```text
pages

 ├── Home

 ├── Login

 └── Dashboard
```

---

# hooks

Custom hooks.

---

Example:

```text
useAuth

useFetch

useTheme
```

---

```text
hooks

 ├── useAuth.js

 ├── useFetch.js
```

---

# services

API communication.

---

Example:

```text
services

 ├── authService.js

 ├── userService.js

 ├── productService.js
```

---

Never put API logic inside components.

---

# store

Global state.

---

Example:

```text
Redux

Zustand

Recoil
```

---

```text
store

 ├── store.js

 ├── authSlice.js

 ├── userSlice.js
```

---

# utils

Helper functions.

---

Example:

```text
formatDate()

capitalize()

currencyFormatter()
```

---

```text
utils

 ├── formatDate.js

 ├── currency.js
```

---

# routes

React Router configuration.

---

Example:

```text
routes

 ├── AppRoutes.jsx

 ├── ProtectedRoute.jsx
```

---

# contexts

Context API.

---

Example:

```text
ThemeContext

AuthContext
```

---

# styles

Global styles.

---

Example:

```text
styles

 ├── global.css

 ├── variables.css
```

---

# constants

Application constants.

---

Example:

```javascript
export const API_URL =
"https://api.example.com";
```

---

# types

TypeScript definitions.

---

Example:

```typescript
User.ts

Product.ts
```

---

# Environment Variables

---

# 5. What Are Environment Variables?

Definition:

> Environment Variables store configuration values outside source code.

---

Examples:

```text
API URL

Authentication Keys

Feature Flags

Environment Settings
```

---

# Problem Without Environment Variables

Bad:

```javascript
const API_URL =
"https://prod-api.com";
```

---

Need different URLs for:

```text
Development

Testing

Production
```

---

Hardcoded values become problematic.

---

# Solution

Environment Variables.

---

# Vite Example

Create:

```text
.env
```

---

```env
VITE_API_URL=
https://api.example.com
```

---

Access:

```javascript
const apiUrl =

import.meta.env
.VITE_API_URL;
```

---

# CRA Example

```env
REACT_APP_API_URL=
https://api.example.com
```

---

Access:

```javascript
process.env
.REACT_APP_API_URL
```

---

# Environment Files

---

Development

```text
.env.development
```

---

Testing

```text
.env.test
```

---

Production

```text
.env.production
```

---

# Example

Development

```env
VITE_API_URL=
http://localhost:8080
```

---

Production

```env
VITE_API_URL=
https://api.company.com
```

---

Application automatically switches.

---

# Important Rule

Only expose:

```text
Public Values
```

---

Never store:

```text
Passwords

Private Keys

Secrets
```

---

Frontend code is visible.

---

# Configuration Management

---

# 6. What is Configuration Management?

Definition:

> Centralized management of application settings.

---

Instead of:

```javascript
const timeout = 5000;
```

---

inside many files,

---

Use:

```text
Single Configuration Source
```

---

# Example

```javascript
export const config = {

 apiUrl:

 import.meta.env
 .VITE_API_URL,

 timeout: 5000,

 pageSize: 10

};
```

---

Usage:

```javascript
import {
 config
}
from "./config";
```

---

```javascript
axios.get(

 config.apiUrl

);
```

---

# Benefits

✔ Single Source Of Truth

✔ Easier Maintenance

✔ Environment Specific Values

✔ Better Scalability

---

# Configuration Folder

```text
src

 ├── config

 │     ├── index.js

 │     ├── apiConfig.js

 │     ├── appConfig.js
```

---

# Example

apiConfig.js

```javascript
export const apiConfig = {

 baseURL:

 import.meta.env
 .VITE_API_URL,

 timeout: 5000

};
```

---

appConfig.js

```javascript
export const appConfig = {

 pageSize: 20,

 defaultTheme:
 "light"

};
```

---

# Enterprise Folder Structure

---

# 7. Real World Enterprise React Structure

```text
src

 ├── assets

 ├── components

 ├── features

 │      ├── auth

 │      ├── products

 │      ├── orders

 │      ├── dashboard

 │      └── users

 ├── hooks

 ├── services

 ├── routes

 ├── store

 ├── contexts

 ├── config

 ├── utils

 ├── types

 ├── constants

 ├── styles

 ├── App.tsx

 └── main.tsx
```

---

Supports:

```text
Large Teams

Large Applications

Micro Frontends

Enterprise Systems
```

---

# Common Mistakes

---

# Huge Components Folder

Bad:

```text
components

 ├── 500 files
```

---

Hard to manage.

---

# API Calls Inside Components

Bad:

```jsx
function User() {

 axios.get(...)

}
```

---

Use:

```text
services/
```

---

# Hardcoded URLs

Bad:

```javascript
"https://api.company.com"
```

---

Use:

```env
VITE_API_URL
```

---

# Duplicate Configurations

Bad:

```javascript
timeout = 5000
```

in many files.

---

Use centralized config.

---

# Best Practices

---

# 8. Production Recommendations

✔ Prefer Feature-Based Structure

✔ Separate UI and Business Logic

✔ Use services for API calls

✔ Use hooks for reusable logic

✔ Store configuration centrally

✔ Use environment variables

✔ Keep components small

✔ Create reusable components

✔ Organize by domain

✔ Use TypeScript types folder

---

# Interview Questions

---

### Q1. Why is project structure important?

```text
Scalability

Maintainability

Team Collaboration
```

---

### Q2. What is Feature-Based Structure?

```text
Organize Files By Business Feature
```

---

### Q3. What is Component-Based Structure?

```text
Organize Files By Component Type
```

---

### Q4. Which structure is preferred for enterprise projects?

```text
Feature-Based Structure
```

---

### Q5. What belongs inside services folder?

```text
API Calls

Axios Logic

Backend Communication
```

---

### Q6. What are environment variables?

```text
External Configuration Values
```

---

### Q7. How do you access Vite environment variables?

```javascript
import.meta.env
```

---

### Q8. Why should secrets not be stored in React env files?

```text
Frontend Code Is Public
```

---

### Q9. What is configuration management?

```text
Centralized Application Settings
```

---

### Q10. What belongs inside utils folder?

```text
Reusable Helper Functions
```

---

# Memory Tricks

### Feature-Based

```text
Group By Business Feature
```

---

### Component-Based

```text
Group By File Type
```

---

### services

```text
API Logic
```

---

### hooks

```text
Reusable Logic
```

---

### utils

```text
Helper Functions
```

---

### store

```text
Global State
```

---

### Environment Variables

```text
External Config
```

---

### Configuration Management

```text
Centralized Settings
```

---

# Complete Architecture Flow

```text
User
      ↓

Routes
      ↓

Pages
      ↓

Features
      ↓

Components
      ↓

Hooks
      ↓

Services
      ↓

API
```

---

# Quick Revision

```text
React Project Structure
-----------------------

Feature-Based
 ↓
 Organize By Feature

Component-Based
 ↓
 Organize By Type

Important Folders:

assets
 ↓
 Images

components
 ↓
 Reusable UI

pages
 ↓
 Screens

hooks
 ↓
 Reusable Logic

services
 ↓
 API Calls

store
 ↓
 Global State

utils
 ↓
 Helpers

routes
 ↓
 Navigation

config
 ↓
 Application Settings

Environment Variables
 ↓
 External Configuration

Configuration Management
 ↓
 Centralized Settings

Best Choice:

Small App
 ↓
 Component-Based

Large Enterprise App
 ↓
 Feature-Based
```

---
