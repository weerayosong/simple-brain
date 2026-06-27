---
title: "Role-Based Wrapper Route Pattern"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - architect
  - frontend
related_tools:
  - react-router
  - react
tags:
  - architecture-pattern
  - routing
  - security
---

# Role-Based Wrapper Route Pattern

> **🤖 Agent Routing:** Frontend and Architect agents should read this to understand how React Router 7 is used to implement strict access control (Public, Private, Guest, Admin) via Component Wrappers.

> **🧠 LLM Context:** This document explores the `ProtectedRoutes.jsx` pattern. It leverages the `<Outlet />` component of React Router to act as a security gateway, intercepting unauthorized users before they can render sensitive UI components.

## 📌 The Problem
In an e-commerce SPA, different routes require different levels of access:
1. Anyone can see `/catalog`.
2. Only logged-in users can see `/cart` or `/profile`.
3. Logged-in users should *not* be able to access the `/login` page again.
4. Only users with the role `admin` can access `/admin`.

Writing access-control logic (like `if (!user) navigate('/login')`) inside *every single* page component is redundant, prone to errors, and causes visual flashing before the redirect happens.

## 🛡️ The Solution (Wrapper Route Injection)
The application defines three distinct Wrapper Components that read from the global `AuthContext`:

1. **`GuestRoute`**: Blocks authenticated users.
   - Logic: `!user ? <Outlet /> : <Navigate to="/" />`
   - Use Case: Wrapping `/login` and `/register`.
2. **`PrivateRoute`**: Blocks anonymous users.
   - Logic: `user ? <Outlet /> : <Navigate to="/login" />`
   - Use Case: Wrapping `/cart`, `/payment`, `/profile`.
3. **`AdminRoute`**: Blocks anyone who isn't an admin.
   - Logic: `user && user.role === "admin" ? <Outlet /> : <Navigate to="/" />`
   - Use Case: Wrapping the entire `/admin` dashboard layout.

### Implementation in `createBrowserRouter`

These wrappers are injected directly into the routing tree as `element` parents, with the protected pages passed as `children`:

```javascript
// src/main.jsx (Abridged)
const router = createBrowserRouter([
  // 🔴 Admin Only Branch
  {
    element: <AdminRoute />, // Gateway
    children: [
      {
        path: "/admin",
        element: <AdminLayout />,
        children: [
           { index: true, element: <AdminDashboard /> },
        ]
      }
    ]
  },
  
  // 🟡 Guest Only Branch
  {
    element: <GuestRoute />, // Gateway
    children: [
      { path: "/login", element: <DLoginScreen /> },
      { path: "/register", element: <DRegisterScreen /> },
    ],
  },
]);
```

## ✨ Benefits
- **Zero Redundancy:** Page components (like `AdminDashboard`) remain "dumb" regarding authentication. They can assume the user exists and has the correct role.
- **Tree-Level Security:** Because the wrapper prevents the `<Outlet />` from rendering, the child component code isn't even mounted if the user lacks permissions, preventing any unauthorized API calls that might have lived in a `useEffect`.

## 🔗 Related Context
- [[group5-frontend-architecture]]
- [[group5-auth-context]]
