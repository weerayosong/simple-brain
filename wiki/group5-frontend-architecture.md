---
title: "Group 5 E-commerce Frontend Architecture"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - architect
  - frontend
related_tools:
  - react
  - react-router
  - tailwind
tags:
  - architecture
  - frontend
---

# Group 5 E-commerce Frontend Architecture

> **🤖 Agent Routing:** Frontend and Architect agents should read this to understand the high-level technology stack and folder structure of the Group 5 E-commerce Single-Page Application (SPA).

> **🧠 LLM Context:** This document outlines the structural choices for a React 19 + Vite + Tailwind v4 web client, focusing on separation of concerns between layout, Context providers, API services, and Role-Based Routing.

## 📌 Overview
The web client for the Group 5 E-commerce platform is built as a robust SPA. It handles complex states like global Authentication (via HTTP-Only Cookies) and Shopping Carts, while restricting access to Admin Dashboards using a secure routing mechanism. 

## 🏗️ Core Details

### 1. Technology Stack
- **Core Framework:** React 19 + Vite 8 (SPA with HMR)
- **Routing:** React Router 7 (`createBrowserRouter`)
- **Styling & UI:** Tailwind CSS v4 (`@tailwindcss/vite`), Radix UI / Base UI (Unstyled primitives wrapped with `clsx` and `tailwind-merge`)
- **Forms:** `react-hook-form` + `zod` for robust client-side validation.
- **Miscellaneous:** `recharts` for admin charts, `sonner` for toast notifications.

### 2. Directory Structure
```text
src/
├── App.jsx                # Root layout (NavBar + Outlet + Footer)
├── main.jsx               # Router definition & Context Providers
├── components/
│   ├── layout/            # Layout wrappers
│   ├── screens/desktop/   # Page-level smart components (Home, Catalog, Admin)
│   └── ui/                # Reusable dumb UI primitives (Button, Card, ProductCard)
├── context/               # Global state (AuthContext, CartContext)
├── hooks/                 # Custom React hooks
├── services/              # API wrapper functions (auth, product, cart)
├── utils/api.js           # Core fetch interceptor (injects cookies)
└── lib/utils.js           # cn() helper (clsx + tailwind-merge)
```

### 3. Authentication & API Strategy
- **No Local Storage for Tokens:** The application relies entirely on secure HTTP-Only Cookies set by the backend. The frontend does not store `accessToken` in `localStorage`.
- **Credentials Include:** Every `fetch` request triggered through `utils/api.js` explicitly uses `credentials: "include"`, ensuring cookies are sent cross-origin.
- **Role-Based Routing:** `main.jsx` uses wrapper components (`GuestRoute`, `PrivateRoute`, `AdminRoute`) to protect branches of the DOM tree.

## 🔗 Related Context
- [[group5-protected-routes]]
- [[group5-auth-context]]
- [[group5-cookie-fetcher]]
