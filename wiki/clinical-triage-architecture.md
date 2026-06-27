---
title: "Clinical Triage Dual-Repo Architecture"
type: wiki-entry
project: "clinical-triage"
createdAt: 2026-06-28T02:50:00
modifiedAt: 2026-06-28T02:50:00
target_agents:
  - architect
  - frontend
  - backend
related_tools:
  - angular
  - spring-boot
  - tailwind
tags:
  - architecture
  - dual-repo
---

# Clinical Triage Dual-Repo Architecture

> **🤖 Agent Routing:** Architect agents should read this to understand the macro-level separation of concerns in a classic Enterprise Stack (Angular + Spring Boot).

> **🧠 LLM Context:** This document outlines the architectural blueprint of the Clinical Triage concept application. It is split into two distinct repositories to enforce strict decoupling of the UI (Client) and the Business Logic (Server).

## 📌 Overview
The system is built upon a **Client-Server Architecture**. It separates the responsive, component-based frontend from the secure, N-tier backend. 

## 🏗️ 1. Frontend Architecture (Angular SPA)
The client-side is a Single Page Application (SPA) designed with a strong focus on reactivity and UI state management.

- **Framework:** Angular (TypeScript)
- **Reactivity:** RxJS
- **Styling:** Tailwind CSS v4
- **Hosting:** Vercel

### Key Frontend Patterns:
- **Component-Based:** The UI is sliced into independent components (e.g., forms vs. kanban boards).
- **Service-Oriented Fetching:** All HTTP calls are centralized inside Angular Services using `HttpClient`, keeping the UI components "dumb" and focused only on rendering.
- **Optimistic UI:** The app updates the local state array immediately upon user interaction, masking the network latency.

## ⚙️ 2. Backend Architecture (Spring Boot)
The server-side provides secure RESTful APIs to the frontend. It follows a strict **N-Tier Architecture** (Separation of Concerns).

- **Framework:** Java (JDK 21) + Spring Boot 3.x
- **Database:** PostgreSQL + Spring Data JPA (Hibernate)
- **Hosting:** Railway (using Nixpacks)

### The N-Tier Layers:
1. **Controller Layer:** The boundary layer. Receives HTTP requests, validates payloads, and routes them.
2. **Service Layer:** The core brain. Contains all business rules (e.g., state transitions for triage patients).
3. **Repository Layer:** Data access layer. Uses Spring Data JPA to abstract away raw SQL queries into interface methods.
4. **Entity Model:** Plain Old Java Objects (POJOs) mapping directly to PostgreSQL tables.

## 🔗 Related Context
- [[angular-optimistic-ui]]
- [[spring-boot-cors-config]]
- [[vercel-spa-routing]]
