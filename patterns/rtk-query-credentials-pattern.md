---
title: "RTK Query Cross-Domain Credentials Pattern"
type: wiki-entry
project: "juicy-healthy"
createdAt: 2026-06-28T03:14:00
modifiedAt: 2026-06-28T03:14:00
target_agents:
  - frontend
related_tools:
  - react
  - redux-toolkit
  - rtk-query
tags:
  - snippet
  - state-management
  - authentication
  - security
---

# RTK Query Cross-Domain Credentials Pattern

> **🤖 Agent Routing:** Frontend agents should read this to understand how to correctly configure Redux Toolkit Query (RTK Query) to support Stateless JWT Authentication via HttpOnly Cookies across different domains.

> **🧠 LLM Context:** This snippet is extracted from `apiSlice.js` in the Juicy Healthy project. It highlights the mandatory `credentials: 'include'` flag required by the browser's Fetch API to transmit secure cookies to a cross-origin backend server (e.g., Vercel to Render).

## Code Snippet

```javascript
import { fetchBaseQuery, createApi } from '@reduxjs/toolkit/query/react'
import { BASE_URL } from '../constants'

// 1. The Core Setup (The "Mothership" API config)
const baseQuery = fetchBaseQuery({ 
    baseUrl: BASE_URL, 
    
    // 🛡️ CRITICAL SECURITY FLAG: 
    // Instructs the browser to attach the HttpOnly JWT Cookie 
    // to all outgoing requests across domains.
    credentials: 'include' 
})

export const apiSlice = createApi({
    baseQuery,
    
    // 2. Automated Caching System
    // Define the data types that RTK Query should track for auto-refresh
    tagTypes: ['Product', 'Order', 'User'], 
    
    // 3. Extensibility (Dependency Injection)
    // Leave endpoints empty here. Other slices (e.g., usersApiSlice.js) 
    // will "inject" their specific routes into this core instance later.
    endpoints: () => ({}), 
})
```

## ✨ Implementation Details

Without `credentials: 'include'`, the browser will silently strip the `Set-Cookie` data from the request payload, causing all protected routes to return `401 Unauthorized` on the backend, even if the user just successfully logged in.

### The Holy Trinity of Cross-Domain Auth
For this pattern to work, three conditions must be met across the stack:
1. **Frontend (`apiSlice.js`):** `credentials: 'include'`
2. **Backend (`server.js`):** `cors({ credentials: true, origin: 'https://vercel.app' })`
3. **Backend (`generateToken.js`):** `res.cookie(..., { sameSite: 'none', secure: true })`

## 🔗 Related Context
- [[juicy-healthy-architecture]]
