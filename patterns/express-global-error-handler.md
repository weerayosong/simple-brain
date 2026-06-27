---
title: "Express Global Error Handler"
type: wiki-entry
project: "jsd12-full-stack-app"
createdAt: 2026-06-28T03:05:00
modifiedAt: 2026-06-28T03:05:00
target_agents:
  - backend
related_tools:
  - express
  - node
tags:
  - snippet
  - error-handling
  - security
---

# Express Global Error Handler

> **🤖 Agent Routing:** Backend agents should read this to understand how to implement a Centralized Error Handling mechanism in Express to prevent unhandled promise rejections and server crashes.

> **🧠 LLM Context:** This snippet is extracted from `server.js` in the JSD12 Full Stack App. It acts as a safety net, catching any errors thrown by upstream controllers. It formats the error into a consistent JSON structure and conditionally hides the stack trace based on the environment (`NODE_ENV`).

## Code Snippet

```javascript
import express from "express";
const app = express();

// ... (Other middlewares like CORS, Helmet, JSON Parser)
// ... (API Routes: app.use("/api", apiRoutes))

/**
 * 🛡️ Centralized Error Handling Middleware
 * Must be defined AFTER all routes and other middlewares.
 * Express recognizes this as an error handler because it takes 4 arguments (err, req, res, next).
 */
app.use((err, req, res, next) => {
    // 1. Log to server console for debugging
    console.error(err.stack);
    
    // 2. Format a consistent JSON response for the frontend
    res.status(err.status || 500).json({
        success: false,
        message: err.message || "Internal Server Error!",
        path: req.originalUrl,
        method: req.method,
        timestamp: new Date().toISOString(),
        
        // 3. Security: Never expose the stack trace in Production
        stack: process.env.NODE_ENV === "development" ? err.stack : undefined,
    });
});

app.listen(3002, () => console.log("Server running"));
```

## ✨ Implementation Details
- **Position is Critical:** This middleware must be placed at the very bottom of `server.js`, right before `app.listen`. If an error is thrown in a route, Express will skip all normal routes and jump straight to this 4-argument middleware.
- **Express 5 Native Promise Support:** In Express v4, you had to manually wrap async controllers with `try/catch` and call `next(err)`. In Express v5, unhandled promise rejections are automatically passed to this error handler!
- **Security Check:** Exposing `err.stack` reveals the internal folder structure of your server. This snippet checks `process.env.NODE_ENV` to ensure it only leaks that data when running locally.

## 🔗 Related Context
- [[jsd12-backend-architecture]]
