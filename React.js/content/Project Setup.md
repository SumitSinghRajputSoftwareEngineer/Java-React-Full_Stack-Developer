# Project Setup

> **Goal:** Understand how React projects are created and executed, Create React App (CRA), Vite, Project Structure, package.json, npm vs npx vs yarn, React Development Server, Build Process, Production Deployment, and interview questions.

---

# 1. Why Do We Need a Project Setup?

React is not like:

```html
index.html
```

where we simply write HTML and open it in a browser.

---

React applications require:

```text
JSX Compilation
Module Bundling
Hot Reloading
Development Server
Build Optimization
```

---

Example:

```jsx
function App() {

 return <h1>Hello React</h1>;
}
```

---

Browser cannot understand:

```jsx
<h1>Hello React</h1>
```

directly.

---

Need tools to convert:

```text
JSX
   ↓
JavaScript
```

---

Need a React project setup.

---

# 2. Ways to Create React Applications

Modern React applications are usually created using:

```text
1. Create React App (CRA)

2. Vite
```

---

Older projects:

```text
Mostly CRA
```

---

Modern projects:

```text
Mostly Vite
```

---

# Create React App (CRA)

---

# 3. What is Create React App?

Create React App is an official React tool that creates a React project with all required configurations.

---

Created by:

```text
React Team (Meta)
```

---

It automatically configures:

```text
Webpack
Babel
Dev Server
Testing Setup
Build Tools
```

---

Without CRA:

You would manually configure:

```text
Webpack
Babel
Loaders
Plugins
```

---

Very difficult.

---

CRA simplifies everything.

---

# 4. Creating a CRA Project

Command:

```bash
npx create-react-app my-app
```

---

Example:

```bash
npx create-react-app employee-management
```

---

This creates:

```text
employee-management/
```

project.

---

Navigate:

```bash
cd employee-management
```

---

Start project:

```bash
npm start
```

---

Output:

```text
React App Running
http://localhost:3000
```

---

# 5. CRA Folder Structure

After creation:

```text
employee-management
│
├── node_modules
│
├── public
│   ├── index.html
│
├── src
│   ├── App.js
│   ├── index.js
│
├── package.json
│
└── package-lock.json
```

---

# 6. Important CRA Files

---

## src/

Contains:

```text
Application Code
```

---

Examples:

```text
Components
Hooks
CSS
Utilities
```

---

## public/

Contains:

```text
Static Files
```

---

Example:

```text
index.html
favicon.ico
```

---

## package.json

Contains:

```text
Project Metadata
Dependencies
Scripts
```

---

# 7. Problems with CRA

CRA became slower over time.

---

Issues:

```text
Slow Startup
Slow Build
Large Bundle
Webpack Complexity
```

---

React community moved toward:

```text
Vite
```

---

# Vite Setup

---

# 8. What is Vite?

Vite (pronounced "veet") is a modern frontend build tool.

---

Created by:

```text
Evan You
```

creator of Vue.js.

---

Goal:

```text
Faster Development
```

---

Compared to CRA:

```text
Much Faster
```

---

# 9. Why Vite is Fast?

CRA:

```text
Bundle Entire Project First
```

---

Then:

```text
Start Development Server
```

---

Vite:

```text
Native ES Modules
```

---

Loads only required files.

---

Result:

```text
Instant Startup
```

---

# 10. Creating Vite React Project

Command:

```bash
npm create vite@latest
```

---

Or:

```bash
npm create vite@latest employee-management
```

---

Select:

```text
React
```

---

Select:

```text
JavaScript
```

or

```text
TypeScript
```

---

Install dependencies:

```bash
npm install
```

---

Run:

```bash
npm run dev
```

---

Output:

```text
http://localhost:5173
```

---

# 11. Vite Folder Structure

```text
employee-management
│
├── node_modules
│
├── public
│
├── src
│
├── package.json
│
├── vite.config.js
│
└── index.html
```

---

Notice:

```text
index.html
```

is in root.

---

Unlike CRA:

```text
public/index.html
```

---

# 12. Important Vite Files

---

## vite.config.js

Vite configuration file.

---

Example:

```javascript
import {
 defineConfig
}
from "vite";

export default defineConfig({

});
```

---

Used for:

```text
Aliases
Plugins
Build Configuration
```

---

# 13. CRA vs Vite

| Feature | CRA | Vite |
|----------|----------|----------|
| Startup Speed | Slow | Very Fast |
| Build Speed | Moderate | Fast |
| HMR | Slower | Instant |
| Configuration | Hidden | Simple |
| Popularity | Older Projects | Modern Projects |
| Default Port | 3000 | 5173 |

---

Interview Answer:

```text
For new projects,
prefer Vite.
```

---

# Project Structure

---

# 14. Why Project Structure Matters?

Small applications:

```text
Manageable
```

---

Large applications:

```text
Hundreds Of Components
Thousands Of Files
```

---

Need organization.

---

# 15. Basic Project Structure

```text
src
│
├── components
│
├── pages
│
├── assets
│
├── services
│
├── hooks
│
├── utils
│
├── routes
│
└── App.jsx
```

---

# 16. components/

Reusable UI components.

---

Examples:

```text
Button
Navbar
Card
Footer
Sidebar
```

---

Structure:

```text
components
│
├── Navbar.jsx
├── Button.jsx
├── Footer.jsx
```

---

# 17. pages/

Represents screens.

---

Examples:

```text
Home
Login
Dashboard
Products
```

---

Structure:

```text
pages
│
├── Home.jsx
├── Login.jsx
├── Dashboard.jsx
```

---

# 18. services/

Contains API calls.

---

Example:

```javascript
import axios
from "axios";

export const getUsers =
() => {

 return axios.get("/users");
};
```

---

Used for:

```text
Backend Communication
```

---

# 19. hooks/

Custom hooks.

---

Example:

```javascript
useFetch()

useAuth()

useTheme()
```

---

# 20. assets/

Contains:

```text
Images
Icons
Fonts
Videos
```

---

Example:

```text
logo.png
banner.jpg
```

---

# 21. routes/

Contains routing configuration.

---

Example:

```javascript
<Route
 path="/login"
 element={<Login />}
/>
```

---

# Understanding package.json

---

# 22. What is package.json?

Most important file in a React project.

---

Definition:

> package.json contains metadata, dependencies, scripts, and project information.

---

Example:

```json
{
  "name": "employee-management",

  "version": "1.0.0",

  "scripts": {

    "start": "react-scripts start"
  }
}
```

---

# 23. Information Stored in package.json

Contains:

```text
Project Name
Version
Scripts
Dependencies
Author
License
```

---

# 24. Dependencies

Example:

```json
"dependencies": {

 "react": "^19.0.0",

 "react-dom": "^19.0.0"
}
```

---

Meaning:

```text
Packages Needed
For Application
```

---

# 25. Scripts

Example:

```json
"scripts": {

 "start": "react-scripts start",

 "build": "react-scripts build",

 "test": "react-scripts test"
}
```

---

Run:

```bash
npm start
```

---

Executes:

```bash
react-scripts start
```

---

# 26. package-lock.json

Automatically generated.

---

Purpose:

```text
Locks Dependency Versions
```

---

Ensures:

```text
Same Packages
On Every Machine
```

---

# npm vs npx vs yarn

---

# 27. What is npm?

npm stands for:

```text
Node Package Manager
```

---

Installed with:

```text
Node.js
```

---

Used for:

```text
Installing Packages
Managing Dependencies
Running Scripts
```

---

Example:

```bash
npm install axios
```

---

# 28. What is npx?

npx stands for:

```text
Node Package Execute
```

---

Runs package without installing globally.

---

Example:

```bash
npx create-react-app my-app
```

---

Flow:

```text
Download Package
Run Package
Discard Package
```

---

No global installation required.

---

# 29. What is Yarn?

Yarn is another package manager.

---

Created by:

```text
Meta
```

---

Similar to:

```text
npm
```

---

Install:

```bash
yarn add axios
```

---

Run:

```bash
yarn start
```

---

# 30. npm vs npx vs Yarn

| Feature | npm | npx | Yarn |
|----------|----------|----------|----------|
| Install Packages | Yes | No | Yes |
| Run Temporary Package | No | Yes | No |
| Dependency Management | Yes | No | Yes |
| Script Execution | Yes | Yes | Yes |
| Package Manager | Yes | No | Yes |

---

# React Development Server

---

# 31. What is Development Server?

Development server runs application locally.

---

Example:

```bash
npm run dev
```

---

Starts:

```text
Local React Server
```

---

Accessible at:

```text
http://localhost:5173
```

(Vite)

---

or

```text
http://localhost:3000
```

(CRA)

---

# 32. Why Development Server?

Provides:

```text
Hot Reloading
Error Overlay
Fast Refresh
Development Environment
```

---

# 33. Hot Module Replacement (HMR)

One of the best Vite features.

---

Example:

```jsx
<h1>Hello</h1>
```

---

Change:

```jsx
<h1>Welcome</h1>
```

---

Browser updates instantly.

---

Without refresh.

---

# 34. Fast Refresh

React preserves:

```text
State
```

during updates.

---

Example:

```text
Counter Value = 10
```

---

Code changes.

---

Counter still:

```text
10
```

---

State preserved.

---

# Build and Production Deployment

---

# 35. Why Build is Needed?

Development code contains:

```text
Debug Code
Source Maps
Extra Checks
```

---

Not optimized.

---

Need production version.

---

# 36. Build Command

CRA:

```bash
npm run build
```

---

Vite:

```bash
npm run build
```

---

Output:

```text
dist/
```

(Vite)

---

```text
build/
```

(CRA)

---

# 37. What Happens During Build?

React performs:

```text
Minification
Tree Shaking
Bundling
Optimization
```

---

Result:

```text
Smaller Files
Faster Loading
```

---

# 38. Minification

Before:

```javascript
function calculateTotal() {

 return amount + tax;
}
```

---

After:

```javascript
function a(){return b+c}
```

---

Smaller file size.

---

# 39. Tree Shaking

Unused code removed.

---

Example:

Imported:

```javascript
import {
 add,
 subtract
}
from "./math";
```

---

Only use:

```javascript
add()
```

---

Build removes:

```text
subtract()
```

---

# 40. Production Deployment

After build:

```text
dist/
```

or

```text
build/
```

folder generated.

---

Deploy to:

```text
Netlify
Vercel
AWS S3
Azure Static Web Apps
GitHub Pages
Firebase Hosting
Nginx Server
Apache Server
```

---

# 41. Vercel Deployment

Very popular.

---

Steps:

```text
Push Code To GitHub
Connect Repository
Deploy
```

---

Automatic deployment.

---

# 42. Deployment Flow

```text
React Source Code
        ↓

Build
        ↓

Optimized Files
        ↓

Hosting Platform
        ↓

Users Access Website
```

---

# Interview Questions

---

### Q1. What is Create React App?

```text
Tool that creates
React applications with
pre-configured setup.
```

---

### Q2. What is Vite?

```text
Modern frontend build tool
focused on speed.
```

---

### Q3. Which is preferred today?

```text
Vite
```

---

### Q4. What is package.json?

```text
Project configuration file.
```

---

### Q5. Difference between npm and npx?

npm:

```text
Installs Packages
```

---

npx:

```text
Executes Packages
```

---

### Q6. What is package-lock.json?

```text
Locks dependency versions.
```

---

### Q7. What is HMR?

```text
Hot Module Replacement
```

Updates modules without full reload.

---

### Q8. What happens during build?

```text
Minification
Bundling
Optimization
Tree Shaking
```

---

### Q9. What is Tree Shaking?

```text
Removing unused code.
```

---

### Q10. Where are production files generated?

Vite:

```text
dist/
```

CRA:

```text
build/
```

---

# Memory Tricks

### CRA

```text
Old React Setup
```

---

### Vite

```text
Fast React Setup
```

---

### package.json

```text
Project Brain
```

---

### npm

```text
Install Packages
```

---

### npx

```text
Run Packages
```

---

### Yarn

```text
Alternative To npm
```

---

### Build

```text
Optimize Application
```

---

### Tree Shaking

```text
Remove Unused Code
```

---

# Quick Revision

```text
Project Setup
-------------

CRA
 ↓
Older React Setup

Vite
 ↓
Modern React Setup

Important Files
---------------

package.json

package-lock.json

vite.config.js

Important Folders
-----------------

src/

components/

pages/

services/

hooks/

assets/

routes/

Commands
---------

Create Vite Project

npm create vite@latest

Install Packages

npm install

Run Project

npm run dev

Build Project

npm run build

Build Optimizations
-------------------

✔ Minification

✔ Tree Shaking

✔ Bundling

✔ Optimization

Deployment
----------

Build
 ↓

dist/

 ↓

Vercel
Netlify
AWS
Azure
Firebase

Remember
---------

npm
 ↓
Install

npx
 ↓
Execute

Yarn
 ↓
Alternative Package Manager

package.json
 ↓
Project Configuration
```

---
**Next Topic:** Components (Functional Components, Class Components, Component Composition, Reusable Components, Pure Components, and Component Lifecycle).