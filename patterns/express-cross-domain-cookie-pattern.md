---
title: "Express Cross-Domain Cookie Pattern"
type: wiki-entry
project: "juicy-healthy"
createdAt: 2026-06-28T03:16:00
modifiedAt: 2026-06-28T03:16:00
target_agents:
  - backend
related_tools:
  - express
  - node
tags:
  - snippet
  - security
  - authentication
---

# Express Cross-Domain Cookie Pattern

> **🤖 Agent Routing:** Backend agents should read this to understand how to correctly configure Express CORS and Cookie settings to securely transmit JWTs to a frontend hosted on a different domain (e.g., Render to Vercel).

> **🧠 LLM Context:** This snippet is extracted from the Juicy Healthy backend. It demonstrates the necessary `cors` middleware setup in `server.js` and the strict `Set-Cookie` headers required in the authentication controller to bypass modern browser security restrictions on third-party cookies.

## The Problem
When the Frontend (Vercel) and Backend (Render) live on different domains, the browser's default security policy (Same-Origin Policy) will aggressively block any attempts to read or write Cookies (including JWTs) across these domains to prevent Cross-Site Request Forgery (CSRF).

## The Solution

### 1. The Gatekeeper: CORS Configuration (`server.js`)
You cannot use the wildcard `*` for the origin if you want to allow credentials. You must explicitly state the exact frontend URL and flip the `credentials: true` flag.

```javascript
import express from 'express'
import cors from 'cors'
const app = express()

app.use(
    cors({
        origin: 'https://juicy-healthy.vercel.app', // 👈 MUST BE EXACT. NO Wildcards (*).
        methods: ['GET', 'POST', 'PUT', 'DELETE'],
        credentials: true, // 👈 CRITICAL: Allows cookies to traverse the domain gap
        allowedHeaders: ['Content-Type', 'Authorization'],
    })
)
```

### 2. The Payload: Secure Cookie Injection (`generateToken.js`)
When generating the JWT after a successful login, you must inject it into the response header with the "Holy Trinity" of cookie security attributes.

```javascript
import jwt from 'jsonwebtoken'

const generateToken = (res, userId) => {
    // 1. Sign the token
    const token = jwt.sign({ userId }, process.env.JWT_SECRET, {
        expiresIn: '30d',
    })

    // 2. Inject it into the HTTP Response Header
    res.cookie('jwt', token, {
        httpOnly: true, // 🛡️ XSS Protection: JavaScript (document.cookie) cannot read this.
        secure: true,   // 🛡️ Man-in-the-Middle Protection: Only send over HTTPS.
        
        // 🛡️ The Cross-Domain Key: 'none' tells the browser to accept 
        // this cookie even though it came from a different domain (Render).
        sameSite: 'none', 
        
        maxAge: 30 * 24 * 60 * 60 * 1000,
    })
}
```

## ✨ Why this matters
This pattern shifts the burden of storing the JWT away from vulnerable frontend Local Storage, relying entirely on the browser's native, highly-secure Cookie jar. The frontend simply needs to send `credentials: 'include'` on every request, and the browser handles the rest securely.

## 🔗 Related Context
- [[juicy-healthy-architecture]]
- [[rtk-query-credentials-pattern]]
