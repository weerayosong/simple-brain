---
title: "Cookie Auth Middleware & Role Guard"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - backend
related_tools:
  - express
  - cookie-parser
tags:
  - snippet
  - authentication
  - middleware
---

# Cookie Auth Middleware & Role Guard

> **🤖 Agent Routing:** Backend agents should read this to see how Express middlewares intercept requests, extract JWTs from cookies, and block unauthorized users or roles.

> **🧠 LLM Context:** This snippet extracts `authUser.js`. Because the app uses HTTP-Only cookies, we do not look for the `Authorization: Bearer <token>` header. Instead, we use `cookie-parser` to read `req.cookies.accessToken`.

## Code Snippet

```javascript
import jwt from "jsonwebtoken";

// 1. Authentication Middleware (Is the user logged in?)
export const authUser = async (req, res, next) => {
  try {
    // CRITICAL: Extract token from cookies, not headers
    const token = req.cookies.accessToken;

    if (!token) {
      return res
        .status(401)
        .json({ success: false, message: "No token found, access denied!" });
    }

    // Verify token payload
    const decodedToken = jwt.verify(token, process.env.JWT_SECRET);
    
    // Attach decoded user info to the request object for downstream controllers
    req.user = { userId: decodedToken.userId, role: decodedToken.role };
    
    next();
  } catch (error) {
    return res
      .status(401)
      .json({ success: false, message: "Session expired or invalid token!" });
  }
};

// 2. Authorization Middleware (Is the user an Admin?)
// Note: This must be used *after* authUser in the route chain
export const adminOnly = (req, res, next) => {
  if (req.user?.role !== "admin") {
    return res.status(403).json({ success: false, message: "Admin access required!" });
  }
  next();
};
```

## ✨ Usage Example in Routes
```javascript
import { Router } from "express";
import { authUser, adminOnly } from "../middleware/authUser.js";

const router = Router();

// Private route (Logged in users only)
router.get("/profile", authUser, getProfileHandler);

// Admin route (Logged in + Admin role)
router.post("/products", authUser, adminOnly, createProductHandler);
```

## 🔗 Related Context
- [[group5-backend-architecture]]
- [[group5-jwt-cookie-login]]
