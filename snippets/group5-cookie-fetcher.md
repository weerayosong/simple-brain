---
title: "Cookie-Aware Fetch API Wrapper"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - frontend
related_tools:
  - javascript
tags:
  - snippet
  - api
  - authentication
---

# Cookie-Aware Fetch API Wrapper

> **🤖 Agent Routing:** Frontend agents should read this to understand how all external HTTP requests are standardized across the application.

> **🧠 LLM Context:** This snippet extracts `utils/api.js`. It is a lightweight wrapper around the native `fetch` API. Its primary responsibility is to attach `credentials: "include"`, which is mandatory for sending HTTP-Only cookies to a cross-origin backend API.

## Code Snippet

```javascript
const BASE_URL = import.meta.env.VITE_API_URL || "http://localhost:3000/api";

export const fetchApi = async (endpoint, options = {}) => {
    const config = {
        ...options,
        // CRITICAL: Forces browser to send HTTP-Only cookies cross-origin
        credentials: "include", 
        headers: {
            "Content-Type": "application/json",
            ...options.headers,
        },
    };

    try {
        const response = await fetch(\`\${BASE_URL}\${endpoint}\`, config);

        if (!response.ok) {
            const errorData = await response.json().catch(() => ({}));
            throw new Error(errorData.message || "Error from server");
        }

        return await response.json();
    } catch (error) {
        console.error(\`API Error (\${endpoint}):\`, error.message);
        throw error;
    }
};
```

## ✨ Usage Example
Instead of importing `axios` and configuring instances, the rest of the application (e.g., `services/auth.js`) simply calls this wrapper:

```javascript
import { fetchApi } from "../utils/api";

export const getMe = () => fetchApi("/users/me");
```

## 🔗 Related Context
- [[group5-frontend-architecture]]
- [[group5-auth-context]]
