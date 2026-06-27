---
title: "Cross-Origin Cookie Authentication Pattern"
type: wiki-entry
project: "group5-ecommerce"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - architect
  - backend
  - frontend
related_tools:
  - express
  - react
tags:
  - architecture-pattern
  - security
  - cors
---

# Cross-Origin Cookie Authentication Pattern

> **🤖 Agent Routing:** All agents (Frontend, Backend, and Architect) MUST read this to understand the "Handshake" required to make HTTP-Only cookies work across different domains (e.g., `localhost:5173` talking to `localhost:3000`).

> **🧠 LLM Context:** Explores the "Holy Trinity" of settings required for secure JWT-in-Cookie auth. Without all three of these configured correctly, cross-origin requests will strip the cookie and authentication will fail.

## 📌 The Problem
Traditional SPA auth stores JWTs in `localStorage` and sends them via the `Authorization: Bearer <token>` header. This is highly vulnerable to **XSS (Cross-Site Scripting)** attacks, as any malicious JavaScript can read `localStorage`.

The solution is **HTTP-Only Cookies**. However, browsers aggressively block cookies from being sent across different origins (e.g., frontend on Port 5173, backend on Port 3000) unless explicitly allowed by both sides.

## 🛡️ The Solution (The Holy Trinity of CORS + Cookies)
To make this work securely, Group 5 E-commerce implemented a strict 3-part handshake:

### 1. The Backend Response (Set-Cookie)
When the user logs in, the backend must issue the cookie with specific flags:
- `httpOnly: true`: Prevents JavaScript (and XSS) from reading it.
- `sameSite: "none"` (Prod) or `"lax"` (Dev): `"none"` is required for cross-domain cookies.
- `secure: true`: Required if `sameSite` is `"none"`. Forces HTTPS.

### 2. The Backend CORS Policy
By default, Express CORS drops cookies. You MUST explicitly configure `cors()` to allow them, and you **cannot** use a wildcard (`*`) origin when credentials are true.

```javascript
// src/config/cors.config.js
export const corsOptions = {
    origin: [
        "http://localhost:5173",
        "https://my-frontend.vercel.app",
    ], // Must be explicit domains! Wildcard "*" will throw an error.
    credentials: true, // CRITICAL: Tells browser it's okay to accept cookies from this server
};
app.use(cors(corsOptions));
```

### 3. The Frontend Fetch Request
Even if the backend sends the cookie, the browser's `fetch` API will *not* send it back on subsequent requests unless explicitly told to include "credentials".

```javascript
// Frontend API wrapper
const response = await fetch(url, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    credentials: "include" // CRITICAL: Forces browser to attach the HTTP-Only cookie
});
```

## ✨ Summary
If you ever encounter an issue where "Login succeeds, but subsequent API calls return 401 Unauthorized", it means one of these three pillars is missing. Check `credentials: "include"` on the client, and `credentials: true` + explicit origins on the server.

## 🔗 Related Context
- [[group5-backend-architecture]]
- [[group5-cookie-fetcher]]
- [[group5-jwt-cookie-login]]
