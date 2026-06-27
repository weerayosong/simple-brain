---
title: "Juicy Healthy Architecture (MERN E-Commerce)"
type: wiki-entry
project: "juicy-healthy"
createdAt: 2026-06-28T03:14:00
modifiedAt: 2026-06-28T03:14:00
target_agents:
  - architect
  - frontend
  - backend
related_tools:
  - mongodb
  - express
  - react
  - node
tags:
  - architecture
  - mern
  - dual-repo
---

# Juicy Healthy Architecture (MERN E-Commerce)

> **🤖 Agent Routing:** Architect agents should read this to understand the MERN Stack implementation details, specifically how to bridge the Cross-Domain identity gap between Vercel and Render using Stateless JWT Authentication.

> **🧠 LLM Context:** This document outlines the architecture of the Juicy Healthy project. It is a full-stack E-Commerce application utilizing the MERN (MongoDB, Express, React, Node.js) stack in a Dual-Repo configuration.

## 📌 Overview
The application is structured into two completely decoupled layers:
- **Frontend (Vercel):** React + Redux Toolkit Query (RTK Query).
- **Backend (Render):** Node.js + Express.js RESTful API.
- **Database (Atlas):** MongoDB via Mongoose ODM.

## 🔒 The Security Bridge (Cross-Domain Identity Management)
The most complex architectural challenge in this system is securely transmitting User Authentication data (JWT) across two different domains (Vercel ↔ Render) without using vulnerable Local Storage.

This is achieved via a 5-Phase Stateless Authentication Lifecycle:

### Phase 1: Strict CORS Policy (Backend)
The Express server explicitly allows the Vercel domain and mandates the `credentials: true` flag. This protocol-level requirement tells the browser that cross-origin identity payloads are permitted.

### Phase 2: Secure Cookie Injection (Backend)
When a user logs in, the server generates a JWT (HMAC SHA256). Instead of returning it as a JSON string, the server injects it directly into the HTTP Response Header (`Set-Cookie`) with strict attributes:
- `httpOnly: true` (Blocks XSS attacks).
- `secure: true` (Forces HTTPS).
- `sameSite: 'none'` (Allows Cross-Site Requests).

### Phase 3: RTK Query Credentials (Frontend)
The React client uses RTK Query (`fetchBaseQuery`). It must be configured with `credentials: 'include'`. This instructs the browser's native `fetch` API to actively attach the hidden HttpOnly cookie to every outgoing request.

### Phase 4: Browser Security Policy
The browser intercepts the outgoing request, verifies the CORS policy from Phase 1 against the `sameSite: 'none'` attribute from Phase 2, and securely transmits the payload over HTTPS.

### Phase 5: The Gatekeeper (Backend)
The Express server receives the request. A middleware (`protect`) uses `cookie-parser` to extract the JWT, verifies its signature against the `JWT_SECRET`, and attaches the decoded user context (`req.user`) before passing the request to the target Controller.

## 🔗 Related Context
- [[rtk-query-credentials-pattern]]
