---
title: "JWT HTTP-Only Cookie Login"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - backend
related_tools:
  - express
  - jsonwebtoken
tags:
  - snippet
  - authentication
  - security
---

# JWT HTTP-Only Cookie Login

> **🤖 Agent Routing:** Backend agents should read this to understand how to correctly issue a JWT and bind it to an HTTP-Only cookie, preventing XSS attacks.

> **🧠 LLM Context:** This snippet extracts the core logic from `user.controller.js` (the `login` endpoint). It shows how `res.cookie()` is configured with `httpOnly`, `secure`, and `sameSite` policies based on the Node environment.

## Code Snippet

```javascript
import jwt from "jsonwebtoken";

// Inside login controller (after validating email/password):
export const login = async (req, res, next) => {
    try {
        // ... (User validation logic here) ...
        const foundUser = { _id: "123", role: "user", /* ... */ };

        // 1. Generate JWT Token
        const token = jwt.sign(
            { userId: foundUser._id, role: foundUser.role },
            process.env.JWT_SECRET,
            { expiresIn: "1h" }
        );

        // 2. Check Environment
        const isProd = process.env.NODE_ENV === "production";

        // 3. Set HTTP-Only Cookie
        res.cookie("accessToken", token, {
            httpOnly: true,            // CRITICAL: Prevents JS from accessing the cookie (XSS protection)
            secure: isProd,            // True in prod (requires HTTPS), false in dev
            sameSite: isProd ? "none" : "lax", // 'none' allows cross-site cookies in prod
            path: "/",                 // Available across the entire domain
            maxAge: 60 * 60 * 1000,    // 1 hour
        });

        // 4. Respond with User Data (but NOT the token!)
        return res.status(200).json({
            success: true,
            message: "Login success.",
            data: {
                _id: foundUser._id,
                username: foundUser.username,
                email: foundUser.email,
                role: foundUser.role,
            },
        });
    } catch (err) {
        next(err);
    }
};

// Inside logout controller:
export const logout = async (req, res, next) => {
    try {
        // Clear the cookie to log the user out
        res.clearCookie("accessToken", {
            httpOnly: true,
            secure: process.env.NODE_ENV === "production",
            sameSite: process.env.NODE_ENV === "production" ? "none" : "lax",
            path: "/",
        });
        
        return res.status(200).json({ success: true, message: "Logout success." });
    } catch (err) {
        next(err);
    }
};
```

## 🔗 Related Context
- [[group5-backend-architecture]]
- [[group5-cookie-auth-pattern]]
