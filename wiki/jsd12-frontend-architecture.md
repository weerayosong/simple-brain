---
title: "JSD12 Full Stack App Frontend Architecture"
type: wiki-entry
project: "jsd12-full-stack-app"
createdAt: 2026-06-28T03:00:00
modifiedAt: 2026-06-28T03:00:00
target_agents:
  - frontend
  - architect
related_tools:
  - react
  - vite
  - tailwind
tags:
  - architecture
  - frontend
---

# JSD12 Full Stack App Frontend Architecture

> **🤖 Agent Routing:** Frontend and Architect agents should read this to understand the evolutionary architecture of this project, moving from a lightweight Single-Screen app to a fully routed Admin Dashboard.

> **🧠 LLM Context:** This document captures the frontend design of the JSD12 Full Stack App, which is split into two distinct React UI applications (`frontend` and `frontend2`). Both consume the same API server.

## 📌 The Dual-UI Architecture
This project features two separate frontend applications to demonstrate different architectural patterns in React.

### 1. Lab I: Single-Screen API Playground (`/frontend`)
The first application is designed as a raw testing ground for multiple databases (MongoDB & Supabase) running in parallel.

- **Routing:** None. It explicitly avoids React Router. It is a single-screen dashboard.
- **State Management:** Strict local state. It avoids the Context API, keeping data flows closely bound to their respective columns.
- **Goal:** Maximum performance, minimal complexity (YAGNI principle). By keeping the app single-screen and decoupling logic via Custom Hooks (like `useCrud`), it remains extremely lightweight.

### 2. Lab II: User Dashboard (`/frontend2`)
The second application evolves into a standard, modern Web Application.

- **Routing:** Introduces client-side routing to handle different views (Home, Dashboard).
- **Authentication:** Integrates with the backend's Auth/Bcrypt endpoints.
- **State Management:** Introduces React Contexts (`/contexts`) and specialized hooks (`useUsers.js`, `useProducts.js`) to share authenticated state across the application tree.

## 🏗️ Core Technology Stack (Both Apps)
- **Framework:** React 19 + Vite 8
- **Styling:** Tailwind CSS v4 (Native Vite integration)
- **Deployment:** Vercel

## 🔗 Related Context
- [[react-use-crud]]
