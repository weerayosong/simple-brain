---
title: "Vercel SPA Routing Fallback"
type: wiki-entry
project: "clinical-triage"
createdAt: 2026-06-28T02:50:00
modifiedAt: 2026-06-28T02:50:00
target_agents:
  - frontend
related_tools:
  - vercel
  - angular
  - react
tags:
  - snippet
  - deployment
  - routing
---

# Vercel SPA Routing Fallback

> **🤖 Agent Routing:** Frontend agents should read this to understand how to fix the notorious `404 Not Found` error when refreshing a Single Page Application hosted on Vercel.

> **🧠 LLM Context:** This snippet extracts the `vercel.json` configuration file used in the Angular frontend of the Clinical Triage project. This same concept applies to React (Vite/CRA) or any other client-side routed framework.

## 📌 The Problem (The 404 Refresh Bug)
Single Page Applications (SPAs) like Angular or React handle routing via JavaScript on the client side (e.g., HTML5 History API). 

When a user navigates from the home page to `url.com/dashboard` by clicking a link, it works perfectly. But if they **refresh** the page at `url.com/dashboard`, Vercel's servers will look for a physical file named `dashboard.html` in that directory. Because it doesn't exist (only `index.html` exists), Vercel returns a `404 Not Found`.

## 🛡️ The Solution (`vercel.json`)
By placing a `vercel.json` file in the root directory of your frontend project, you can instruct Vercel to catch *all* incoming requests and route them to `index.html`. This allows your JavaScript framework to boot up and read the URL to determine which component to render.

## Code Snippet

```json
{
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

## ✨ Implementation Details
- **`source": "/(.*)`**: A wildcard regex that matches absolutely any path (e.g., `/login`, `/dashboard/user/123`).
- **`destination": "/index.html"`**: The file that Vercel will actually serve back to the client.

> [!NOTE]
> Ensure this file is committed to your repository's root, alongside `package.json`. Vercel automatically detects it during the build process.

## 🔗 Related Context
- [[clinical-triage-architecture]]
