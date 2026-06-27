---
title: "Balance Tracker Architecture (Decoupled 3-Tier)"
type: wiki-entry
project: "balance-tracker"
createdAt: 2026-06-28T03:09:00
modifiedAt: 2026-06-28T03:09:00
target_agents:
  - architect
  - frontend
related_tools:
  - react
  - vercel
  - render
tags:
  - architecture
  - dual-repo
---

# Balance Tracker Architecture (Decoupled 3-Tier)

> **🤖 Agent Routing:** Architect agents should read this to understand the benefits of separating a Full-Stack application into distinct Client and Server repositories.

> **🧠 LLM Context:** This document captures the architectural decisions made in the `balance-tracker` project. It utilizes a strict Dual-Repo setup to enforce Separation of Concerns and enable independent CI/CD pipelines.

## 📌 The Architecture Pattern

The system is designed as a **Decoupled 3-Tier Architecture**:

1. **Presentation Layer (Frontend):** React (Vite) + Tailwind CSS. 
2. **Application Logic Layer (Backend):** Node.js + Express REST API.
3. **Data Layer (Database):** MongoDB Atlas.

### Why Dual-Repo? (Decoupled Architecture)

Rather than keeping the Frontend and Backend in a single "monorepo", they are separated into two distinct GitHub repositories.

#### 1. Independent CI/CD Pipelines
- The Frontend is deployed to **Vercel** (Global CDN for static assets). When UI code changes, Vercel builds and deploys instantly without touching the backend server.
- The Backend is deployed to **Render** (Web Service Compute). When API logic changes, Render restarts the Node process without forcing the user to download new frontend assets.

#### 2. Future-Proof Extensibility (Stateless API)
Because the API server is completely decoupled from the React client, it is trivial to build a completely new client (e.g., a React Native mobile app or a Desktop app) that connects to the exact same Backend Repository and Database.

#### 3. Enhanced Security
Environment variables (`.env`) like Database Passwords or JWT Secrets are strictly confined to the Backend Repository. The Frontend Repository only needs to know the public API URL, completely eliminating the risk of accidentally leaking server credentials into the client-side browser bundle.

## 🔗 Related Context
- [[react-context-api-pattern]]
