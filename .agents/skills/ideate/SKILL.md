---
name: Ideate (Brainstorm)
description: Expand a raw idea from the user into a structured project plan and save it to the wiki.
---

# Instruction

The user has a raw idea and wants you to act as a Senior Architect and Product Manager to flesh it out into a concrete project plan.

When the user triggers this skill (e.g., "ช่วย Ideate โปรเจกต์ใหม่ให้หน่อย" or "ขยายไอเดียแอปใหม่"):

1. **Identify the Idea**:
   - Ask the user which raw note or idea they want to expand, or let them type it directly in the chat.
2. **Brainstorm & Structure**:
   - Create a comprehensive project plan that includes:
     - **Project Goals & MVP Features** (What is the core value?)
     - **Proposed Tech Stack** (Suggest tools based on the user's preferences in `CONTEXT.md`)
     - **High-level Architecture**
     - **Step-by-step Implementation Tasks**
3. **Save to Wiki**:
   - Save this plan as a new markdown file in the `/wiki/` directory (e.g., `/wiki/plan-new-app.md`).
   - **Crucial**: You MUST use the standard Multi-Agent Routing YAML Frontmatter template defined in `AGENTS.md` / `ingest-codebase` skill, setting `target_agents: [architect, planning]` so future sub-agents know this is a planning document.
