---
title: "Express API Versioning (Directory-Based)"
type: wiki-entry
project: "balance-tracker"
createdAt: 2026-06-28T03:10:00
modifiedAt: 2026-06-28T03:10:00
target_agents:
  - backend
  - architect
related_tools:
  - express
  - node
tags:
  - snippet
  - architecture-pattern
  - api-design
---

# Express API Versioning (Directory-Based)

> **🤖 Agent Routing:** Backend and Architect agents should read this to understand how to gracefully evolve an API over time without breaking backwards compatibility for older clients.

> **🧠 LLM Context:** This snippet is extracted from the `balance-tracker` backend. It demonstrates a directory-based approach to API Versioning (`/api/v1` vs `/api/v2`). 

## 📌 The Problem
When building APIs, requirements change. You might start with a simple app where all tasks belong to one global pool (v1). Later, you add User Authentication, meaning tasks must now belong to specific users (v2). 

If you just overwrite your existing endpoints, any older mobile apps or frontend clients that haven't updated yet will instantly crash because they don't know how to send Authentication tokens.

## 🛡️ The Solution (Directory Versioning)
By physically splitting the code into `v1` and `v2` directories, we can run both logic paths simultaneously.

### Directory Structure
```text
src/
 └── api/
      ├── v1/
      │    ├── controllers/
      │    ├── models/
      │    └── index.js       <-- Exports v1 router
      └── v2/
           ├── controllers/
           ├── models/
           └── index.js       <-- Exports v2 router
```

### Code Snippet (`server.js`)

```javascript
import express from "express";
import v1Routes from "./api/v1/index.js";
import v2Routes from "./api/v2/index.js";

const app = express();

// ... middleware ...

// 🚀 Mount the versions on different URL paths
app.use("/api/v1", v1Routes);
app.use("/api/v2", v2Routes);

app.listen(3002, () => console.log("Server running"));
```

### Code Snippet (`src/api/v1/index.js`)

```javascript
import express from "express";
import taskRoutes from "./routes/taskRoutes.js";

const router = express.Router();

// Everything here is implicitly prefixed with /api/v1/
router.use("/tasks", taskRoutes); 

export default router;
```

## ✨ Why this matters
- **Zero Downtime Migrations:** You can deploy `v2`, tell the frontend team to start migrating, and keep `v1` running for months until all users have updated their apps.
- **Mental Clarity:** Developers working in the `v2` folder know they can make breaking changes to Database Models or Controllers without worrying about destroying the legacy system.

## 🔗 Related Context
- [[balance-tracker-architecture]]
