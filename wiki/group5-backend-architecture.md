---
title: "Group 5 E-commerce Backend Architecture"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - architect
  - backend
related_tools:
  - express
  - mongoose
  - node
tags:
  - architecture
  - backend
---

# Group 5 E-commerce Backend Architecture

> **🤖 Agent Routing:** Backend and Architect agents should read this to understand the high-level technology stack, middleware setup, and folder structure of the Group 5 E-commerce API.

> **🧠 LLM Context:** This document outlines the structural choices for a Node.js + Express 5 + MongoDB backend. It highlights the use of ES Modules, modular routing, and the security middleware stack (Helmet, CORS, RateLimiter).

## 📌 Overview
The backend API for the Group 5 E-commerce platform acts as the secure data provider for the React SPA. It heavily enforces authentication at the API boundary using HTTP-Only cookies and JWT, while interacting with MongoDB via Mongoose.

## 🏗️ Core Details

### 1. Technology Stack
- **Core Framework:** Node.js 20+ (using `--env-file` instead of external dotenv loaders in dev) + Express 5
- **Database:** MongoDB + Mongoose 9 (ODM)
- **Authentication:** `jsonwebtoken` (JWT) + `bcrypt`
- **Security:** `helmet` (HTTP headers), `cors` (Cross-Origin), `express-rate-limit` (DDoS protection)
- **Syntax:** Native ES Modules (`"type": "module"` in package.json)

### 2. Directory Structure
```text
src/
├── server.js              # Entry point: Mounts middlewares, DB connect, and /api
├── config/                # Environment-specific configs (MongoDB, CORS)
├── middleware/            # Express middlewares
│   ├── authUser.js        # Extracts JWT from cookie & Role Guard
│   ├── error.middleware.js# Global error catcher
│   └── rateLimiter.js     # Global rate limiting
├── modules/               # Domain-Driven Design (Feature Modules)
│   ├── user/              # User Controller + User Model
│   ├── products/
│   ├── carts/
│   └── order/
├── routes/                # Central API router combining all module routes
└── utils/                 # Shared helper functions
```

### 3. Key Architectural Decisions
- **Domain-Driven Grouping:** Unlike traditional MVC where all controllers are in one folder and all models in another, this project groups them by feature inside `/modules/` (e.g., `/modules/user/user.controller.js` and `/modules/user/user.model.js`). This makes the codebase highly scalable.
- **Trust Proxy:** `app.set('trust proxy', 1);` is configured in `server.js`, which is crucial when deploying behind reverse proxies (like Render, Heroku, or Vercel) so that Rate Limiters can accurately read client IP addresses.

## 🔗 Related Context
- [[group5-frontend-architecture]]
- [[group5-cookie-auth-pattern]]
