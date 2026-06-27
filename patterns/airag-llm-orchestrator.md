---
title: "Strict Output LLM Orchestration Pattern"
type: wiki-entry
project: "ai-rag-agent"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - architect
  - backend
related_tools:
  - gemini
  - express
tags:
  - architecture-pattern
  - rag
  - ai
---

# Strict Output LLM Orchestration Pattern

> **🤖 Agent Routing:** Architect and Backend agents should read this to understand how to prevent LLM Hallucination and enforce strict JSON responses for seamless UI integration.

> **🧠 LLM Context:** Explores the Retrieval-Augmented Generation (RAG) implementation in `ai-rag-agent` that uses strict schema constraints and payload reduction to force Gemini to act as a deterministic data pipeline rather than a conversational bot.

## 📌 The Problem
When building AI features, passing raw user prompts to an LLM often results in conversational, unstructured text. If the frontend expects an array of Product Objects to render UI cards, the LLM might hallucinate extra fields, change the data types, or return markdown text that breaks the `JSON.parse()`. Furthermore, feeding the entire database into the LLM context is expensive and hits token limits.

## 🛡️ The Solution (The Strict RAG Pipeline)
The `ai-rag-agent` solves this through a 4-step orchestration pipeline implemented in Express.js.

### How it works:

1. **Information Retrieval & Reduction:**
   - Instead of passing the entire DummyJSON response to the LLM, the `chat.controller.ts` maps and reduces the array. It drops heavy, irrelevant fields (like `reviews`, `stock`, `brand`) and passes only `id`, `title`, `price`, `category`, and `thumbnail`. 
   - This drastically reduces token usage and prevents the LLM from getting confused by too much context.

2. **System Instruction Fencing:**
   - The `gemini.service.ts` explicitly fences the AI's behavior: 
     *"Your task is to answer user queries and recommend products strictly based on the provided DummyJSON product data. DO NOT hallucinate..."*
   - This prevents the LLM from inventing products that aren't in the context window.

3. **Strict JSON Schema Enforcement (`@google/genai`):**
   - The application defines a `@google/genai` `Schema` object with `type: Type.OBJECT` and strictly types the nested `recommendedProducts` array.
   - By passing this schema into the `responseSchema` config and setting `responseMimeType: 'application/json'`, the Gemini API is forced to return a strictly typed JSON string. No markdown wrappers, no conversational filler outside of the `text` field.

4. **Deterministic Settings:**
   - `temperature: 0.2` is used. A low temperature makes the model more deterministic and analytical, which is exactly what you want when extracting data from a provided context, minimizing the chance of hallucination.

## 🔗 Related Context
- [[airag-api]]
- [[airag-gemini-service]]
