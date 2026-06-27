---
title: "JSD12 Full Stack App Backend Architecture"
type: wiki-entry
project: "jsd12-full-stack-app"
createdAt: 2026-06-28T03:05:00
modifiedAt: 2026-06-28T03:05:00
target_agents:
  - backend
  - architect
related_tools:
  - express
  - node
  - mongodb
  - supabase
tags:
  - architecture
  - backend
---

# JSD12 Full Stack App Backend Architecture

> **🤖 Agent Routing:** Backend and Architect agents should read this to understand the minimalist, dual-database approach to building an Express API without over-reliance on third-party NPM packages.

> **🧠 LLM Context:** This document captures the backend design of the JSD12 Full Stack App. It highlights a phased evolution from a raw Node HTTP server into a mature Express application featuring strict Separation of Concerns and dual database connectivity.

## 📌 Architectural Philosophy: Minimalism
A core philosophy of this project is to keep external dependencies to an absolute minimum. 
- **No Nodemon or dotenv loaders:** The app runs natively using Node.js 20+ flags (`node --watch --env-file=.env src/server.js`). This reduces the vulnerability footprint and keeps the build lightweight.
- **Internal REST Client:** Testing is done via `.rest` files inside the `/testHTTP` folder, removing the need for heavy GUI clients like Postman.

## 🏗️ Core Technology Stack
- **Runtime:** Node.js (v20+)
- **Framework:** Express.js 5
- **Primary Database (NoSQL):** MongoDB + Mongoose ODM (Users & Products)
- **Secondary Database (SQL):** PostgreSQL via Supabase (Notes)
- **Security:** Helmet, Express Rate Limiter, Bcrypt, and strict CORS configuration.

## 🧠 Key Architectural Features

### 1. Dual-Database Competency
The application connects to both **MongoDB (Atlas)** and **Supabase (PostgreSQL)** simultaneously on startup. 
- This demonstrates the ability to handle heterogeneous data sources (e.g., using NoSQL for flexible user profiles, and SQL for structured relational data) within a single monolithic API layer.

### 2. Separation of Concerns (SoC)
The project strictly decouples routing from business logic.
- **Routes (`/routes`):** Act purely as traffic controllers. They map endpoints (e.g., `GET /users`) to specific Controller functions.
- **Controllers (`/modules/*/controller`):** Contain the actual data fetching and manipulation logic.

### 3. Centralized Error Handling
Instead of writing `try/catch` blocks that manually format error responses in every single controller, the app relies on a global Express Error Handler middleware.

## 🔗 Related Context
- [[express-global-error-handler]]
- [[jsd12-frontend-architecture]]
