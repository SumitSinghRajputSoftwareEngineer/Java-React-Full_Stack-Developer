# React Build & Deployment

> **Goal:** Master React Build & Deployment completely — understand Production Builds, Build Optimization, Environment Configurations, Deployment on Netlify, Vercel, AWS, Azure, Docker, CI/CD concepts, enterprise deployment architecture, performance considerations, and interview questions.

---

# 1. What is Build & Deployment?

During development:

```text
React Source Code
```

contains:

```text
JSX

Source Maps

Comments

Development Tools

Unused Code
```

---

Browsers cannot directly understand:

```jsx
<MyComponent />
```

---

Need:

```text
Production Ready Files
```

---

This process is called:

```text
Build
```

---

After build:

```text
Deploy
```

to a server.

---

# Development vs Production

Development:

```text
Fast Refresh

Debugging

Source Maps

Detailed Errors
```

---

Production:

```text
Minified Code

Optimized Assets

Smaller Bundle

Better Performance
```

---

# React Deployment Flow

```text
Developer
      ↓

React Source Code
      ↓

Build
      ↓

HTML + CSS + JS
      ↓

Deploy
      ↓

Web Server
      ↓

Users
```

---

# Production Build

---

# 2. What is a Production Build?

Definition:

> Production build converts React source code into optimized static files ready for deployment.

---

# React Source Code

```jsx
function App() {

 return <h1>Hello</h1>;

}
```

---

Browser receives:

```html
index.html
```

---

and

```text
Bundled JavaScript
```

---

# CRA Production Build

Command:

```bash
npm run build
```

---

Output:

```text
build/
```

---

Structure:

```text
build

 ├── index.html

 ├── assets

 ├── static

 ├── css

 └── js
```

---

# Vite Production Build

Command:

```bash
npm run build
```

---

Output:

```text
dist/
```

---

Structure:

```text
dist

 ├── index.html

 ├── assets

 ├── css

 └── js
```

---

# What Happens During Build?

React performs:

```text
JSX Compilation

Tree Shaking

Minification

Code Splitting

Bundle Generation

Optimization
```

---

# Visualization

```text
React Source
      ↓

Babel
      ↓

Webpack/Vite
      ↓

Optimized Bundle
      ↓

Deployment
```

---

# Build Optimization

---

# 3. What is Build Optimization?

Definition:

> Reducing application size and improving performance before deployment.

---

Goal:

```text
Faster Loading

Smaller Bundle

Better User Experience
```

---

# Why Optimization Matters?

Without optimization:

```text
5 MB Bundle
```

---

User waits:

```text
Several Seconds
```

---

Optimized:

```text
500 KB Bundle
```

---

Loads quickly.

---

# Minification

Removes:

```text
Comments

Extra Spaces

Unused Names
```

---

Before:

```javascript
function calculateTotal() {

 return price + tax;

}
```

---

After:

```javascript
function a(){return b+c}
```

---

Smaller file.

---

# Tree Shaking

Definition:

> Removes unused code from final bundle.

---

Example

```javascript
import {

 add,

 subtract

}
from "./math";
```

---

Using only:

```javascript
add()
```

---

Build removes:

```javascript
subtract()
```

---

Result:

```text
Smaller Bundle
```

---

# Code Splitting

Without code splitting:

```text
Entire App
Downloaded At Once
```

---

Bad for large apps.

---

Example:

```text
10 MB Bundle
```

---

Code splitting:

```text
Download Only Needed Code
```

---

Visualization

```text
Home Bundle

Dashboard Bundle

Admin Bundle
```

---

Loaded when needed.

---

# React Lazy

```jsx
const Dashboard =

React.lazy(
 () => import("./Dashboard")
);
```

---

Only downloads when user visits:

```text
Dashboard
```

---

# Image Optimization

Use:

```text
WebP

Compressed Images

Responsive Images
```

---

Bad:

```text
10 MB PNG
```

---

Good:

```text
100 KB WebP
```

---

# Bundle Analysis

Popular tools:

```text
webpack-bundle-analyzer

vite-bundle-visualizer
```

---

Shows:

```text
Large Dependencies

Unused Packages

Bundle Size
```

---

# Environment Configurations

---

# 4. Why Environment Configurations?

Different environments need different settings.

---

Examples:

```text
Development

Testing

Staging

Production
```

---

API URLs differ.

---

Development:

```text
localhost:8080
```

---

Production:

```text
api.company.com
```

---

Need environment-specific configuration.

---

# Environment Files

Vite:

```text
.env

.env.development

.env.production
```

---

CRA:

```text
.env

.env.production

.env.test
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

# Accessing Variables

Vite:

```javascript
import.meta.env
.VITE_API_URL
```

---

CRA:

```javascript
process.env
.REACT_APP_API_URL
```

---

# Important Security Rule

Never store:

```text
Database Passwords

API Secrets

Private Keys
```

---

Frontend code is public.

---

# Netlify Deployment

---

# 5. What is Netlify?

:contentReference[oaicite:0]{index=0}

provides:

```text
Frontend Hosting
```

---

Supports:

```text
React

Vue

Angular

Static Sites
```

---

# Deployment Flow

```text
GitHub
    ↓

Netlify
    ↓

Automatic Deployment
```

---

# Steps

---

Push project:

```text
GitHub
```

---

Connect repository.

---

Build command:

```bash
npm run build
```

---

Publish directory:

```text
dist
```

(Vite)

---

or

```text
build
```

(CRA)

---

Deploy.

---

# Benefits

✔ Free Tier

✔ CI/CD

✔ SSL

✔ CDN

✔ Custom Domains

---

# Vercel Deployment

---

# 6. What is Vercel?

:contentReference[oaicite:1]{index=1}

is extremely popular for React applications.

---

Supports:

```text
React

Next.js

Vite
```

---

# Deployment Flow

```text
GitHub
    ↓

Vercel
    ↓

Automatic Deploy
```

---

# Steps

1.

```text
Import GitHub Repo
```

---

2.

```text
Configure Build
```

---

3.

```text
Deploy
```

---

# Benefits

✔ Extremely Fast

✔ Automatic Deployments

✔ Preview Deployments

✔ Global CDN

✔ Easy Rollback

---

# AWS Deployment

---

# 7. Deploying React on AWS

AWS =

:contentReference[oaicite:2]{index=2}

---

Popular services:

```text
S3

CloudFront

EC2

Elastic Beanstalk

Amplify
```

---

# Option 1: S3 + CloudFront

Most common.

---

Build:

```bash
npm run build
```

---

Upload:

```text
dist/
```

to

```text
S3 Bucket
```

---

Use:

```text
CloudFront CDN
```

---

Architecture

```text
User
  ↓

CloudFront
  ↓

S3 Bucket
```

---

Benefits:

✔ Cheap

✔ Fast

✔ Scalable

---

# Option 2: AWS Amplify

Easy deployment.

---

Flow:

```text
GitHub
   ↓

Amplify
   ↓

Deployment
```

---

Features:

✔ CI/CD

✔ Hosting

✔ Authentication

✔ Backend Integration

---

# Azure Deployment

---

# 8. Deploying React on Azure

Azure =

:contentReference[oaicite:3]{index=3}

---

Popular options:

```text
Azure Static Web Apps

Azure App Service

Azure Storage
```

---

# Azure Static Web Apps

Recommended.

---

Flow:

```text
GitHub
   ↓

Azure
   ↓

Deployment
```

---

Build automatically.

---

Provides:

✔ SSL

✔ CDN

✔ Authentication

✔ CI/CD

---

# Azure App Service

Useful when:

```text
Frontend
+
Backend
```

are deployed together.

---

# Docker Deployment

---

# 9. What is Docker?

:contentReference[oaicite:4]{index=4}

allows packaging application with dependencies.

---

Problem:

```text
Works On My Machine
```

---

Different servers:

```text
Different Environments
```

---

Docker solves:

```text
Environment Consistency
```

---

# Docker Architecture

```text
React App
      ↓

Docker Image
      ↓

Docker Container
      ↓

Server
```

---

# Dockerfile

Example:

```dockerfile
FROM node:20

WORKDIR /app

COPY . .

RUN npm install

RUN npm run build

CMD ["npm","start"]
```

---

# Production Dockerfile

More common:

```dockerfile
FROM node:20 AS build

WORKDIR /app

COPY . .

RUN npm install

RUN npm run build

FROM nginx

COPY --from=build
/app/dist
/usr/share/nginx/html
```

---

# Build Docker Image

```bash
docker build -t react-app .
```

---

# Run Container

```bash
docker run -p 80:80 react-app
```

---

Application runs inside container.

---

# Benefits

✔ Consistent Environment

✔ Easy Deployment

✔ Scalability

✔ Cloud Friendly

✔ Microservice Ready

---

# CI/CD Pipeline

---

# 10. What is CI/CD?

CI:

```text
Continuous Integration
```

---

CD:

```text
Continuous Deployment
```

---

Flow:

```text
Developer Pushes Code
          ↓

GitHub
          ↓

Build
          ↓

Tests
          ↓

Deployment
```

---

# Popular CI/CD Tools

```text
GitHub Actions

Azure DevOps

Jenkins

GitLab CI

CircleCI
```

---

# Example

Push code.

---

Pipeline:

```bash
npm install
```

---

```bash
npm test
```

---

```bash
npm run build
```

---

Deploy automatically.

---

# Enterprise Deployment Architecture

---

# Real World React Deployment

```text
User
   ↓

CDN
   ↓

React Build Files
   ↓

API Gateway
   ↓

Backend Services
   ↓

Database
```

---

# Common Deployment Mistakes

---

# Deploy Development Build

Wrong:

```text
npm start
```

in production.

---

Use:

```bash
npm run build
```

---

# Hardcoded URLs

Wrong:

```javascript
"http://localhost:8080"
```

---

Use:

```env
VITE_API_URL
```

---

# Large Bundle Sizes

Wrong:

```text
10 MB JavaScript
```

---

Use:

```text
Lazy Loading

Code Splitting
```

---

# No Caching

Bad:

```text
Every Request Downloads Everything
```

---

Use:

```text
CDN + Browser Cache
```

---

# Storing Secrets in Frontend

Wrong:

```env
SECRET_KEY=123456
```

---

Frontend is public.

---

# Best Practices

---

✔ Always create production build

✔ Use environment variables

✔ Enable code splitting

✔ Optimize images

✔ Use CDN

✔ Use CI/CD

✔ Monitor bundle size

✔ Compress assets

✔ Use HTTPS

✔ Automate deployments

---

# Interview Questions

---

### Q1. What is a production build?

```text
Optimized Build
For Deployment
```

---

### Q2. What command creates a React build?

```bash
npm run build
```

---

### Q3. What is tree shaking?

```text
Removing Unused Code
```

---

### Q4. What is code splitting?

```text
Loading Code
Only When Needed
```

---

### Q5. What is React.lazy?

```text
Lazy Loading Components
```

---

### Q6. What is Docker?

```text
Containerization Platform
```

---

### Q7. Why use environment variables?

```text
Environment-Specific Configurations
```

---

### Q8. Can secrets be stored in React env files?

```text
No
```

---

### Q9. What AWS service is commonly used for React hosting?

```text
S3 + CloudFront
```

---

### Q10. What is CI/CD?

```text
Automatic Build
Test
Deploy Pipeline
```

---

# Memory Tricks

### Production Build

```text
Source Code
     ↓
Optimized Files
```

---

### Tree Shaking

```text
Remove Unused Code
```

---

### Code Splitting

```text
Load When Needed
```

---

### Netlify

```text
Easy Frontend Hosting
```

---

### Vercel

```text
React Friendly Hosting
```

---

### AWS

```text
Enterprise Cloud
```

---

### Azure

```text
Microsoft Cloud
```

---

### Docker

```text
Package App
With Dependencies
```

---

### CI/CD

```text
Push Code
Build
Test
Deploy
```

---

# Complete Deployment Flow

```text
React Source Code
        ↓

npm run build
        ↓

Optimized Bundle
        ↓

Environment Config
        ↓

CI/CD Pipeline
        ↓

Netlify / Vercel
AWS / Azure
Docker
        ↓

Users
```

---

# Quick Revision

```text
React Build & Deployment
------------------------

Production Build
 ↓
 Optimized Files

Build Optimization
 ↓
 Minification
 Tree Shaking
 Code Splitting

Environment Config
 ↓
 .env Files

Deployment Platforms

Netlify
 ↓
 Easy Hosting

Vercel
 ↓
 React Friendly

AWS
 ↓
 S3 + CloudFront

Azure
 ↓
 Static Web Apps

Docker
 ↓
 Containerized Deployment

CI/CD
 ↓
 Automated Build & Deploy

Best Practices:

✔ Production Build

✔ Lazy Loading

✔ CDN

✔ HTTPS

✔ Environment Variables

✔ CI/CD

Remember:

Build
 ↓
 Create Optimized Files

Deploy
 ↓
 Host Files

Docker
 ↓
 Package Application

CI/CD
 ↓
 Automate Everything
```

---
